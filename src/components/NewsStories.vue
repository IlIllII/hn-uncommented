<script>
export default {
    data() {
        return {
            stories: [],
            max_score: 0,
            weeksAgo: 1,
            checked: false
        };
    },

    props: {
        isWeekly: {
            type: Boolean,
        },
        startDate: {
            type: Date,
        },
        endDate: {
            type: Date,
        },
    },

    mounted() {
        this.getStories(this.isWeekly);
    },

    watch: {
        // eslint-disable-next-line no-unused-vars
        isWeekly: function (newVal, oldVal) {
            this.getStories(newVal);
        },
        // eslint-disable-next-line no-unused-vars
        startDate: function (newVal, oldVal) {
            this.getStories(this.isWeekly);
        },
    },

    methods: {
        async fetchWeeklyStories() {
            try {
                let dateRange = { endDate: this.endDate, startDate: this.startDate }
                const startTimestamp = Math.floor(dateRange.startDate.getTime() / 1000);
                const endTimestamp = Math.floor(dateRange.endDate.getTime() / 1000);
                // CORS proxy deployed using a cloudflare worker - see that code for routing and details
                let url = "https://algolia.uncommentedhn.com"
                const res = await fetch(url + "?tags=story&numericFilters=created_at_i>" + startTimestamp + ",created_at_i<" + endTimestamp + ",points>300&hitsPerPage=1000").then(res => res.json());
                const stories = res.hits.sort((a, b) => b.points - a.points).slice(0, 30);
                let formattedStories = []
                for (const story of stories) {
                    formattedStories.push({
                        id: story.objectID,
                        title: story.title,
                        url: story.url,
                        points: story.points
                    })
                }
                return formattedStories
            } catch (error) {
                console.log(error)
            }
        },

        async fetchFrontPageStories() {
            try {
                const response = await fetch('https://hacker-news.firebaseio.com/v0/topstories.json');
                const storyIds = await response.json();
                const topIds = storyIds.slice(0, 30);

                const storyPromises = topIds.map(id =>
                    fetch(`https://hacker-news.firebaseio.com/v0/item/${id}.json`).then(response => response.json())
                );

                let stories = [];
                for (const story of await Promise.all(storyPromises)) {
                    stories.push({
                        id: story.id,
                        title: story.title,
                        url: story.url,
                        points: story.score
                    })
                }
                return stories;
            } catch (error) {
                console.error("An error occurred while fetching data:", error);
            }
        },

        async getStories(isWeekly) {
            console.log(isWeekly)
            let stories = isWeekly ? await this.fetchWeeklyStories() : await this.fetchFrontPageStories();
            this.stories = stories.sort((a, b) => b.points - a.points).slice(0, 30);
            this.max_score = Math.max(...this.stories.map(story => story.points));
        },

        paddedScore(score) {
            const scoreString = score.toString();
            const maxScoreString = this.max_score.toString();
            const maxScoreStringLength = maxScoreString.length;
            const scoreStringLength = scoreString.length;
            const paddingLength = maxScoreStringLength - scoreStringLength;
            const padding = " ".repeat(paddingLength);
            return `${padding}${scoreString}`;
        },

        getDomain(url) {
            try {
                const urlObject = new URL(url);
                return urlObject.hostname;
            } catch (error) {
                return "";
            }
        },
    }
}
</script>

<template>
    <div class="news-stories">
        <ul>
            <li v-for="story in stories.sort((a, b) => b.score - a.score)" :key="story.id">
                <a :href="story.url" target="_blank" rel="noopener">[{{ this.paddedScore(story.points) }}] {{ story.title }}
                </a>
                <span> {{ getDomain(story.url) }}</span>
            </li>
        </ul>
    </div>
</template>

<style scoped>
span {
    margin-left: 1rem;
    color: var(--color-text);
    font-size: 0.8rem;
}

ul {
    list-style: none;
}

li {
    text-indent: -4rem;
    padding-left: 2rem;
}

div {
    padding: 1rem 0 0 0;
}
</style>