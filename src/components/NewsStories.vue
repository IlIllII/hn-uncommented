<script>
export default {
    data() {
        return {
            stories: [],
            max_score: 0
        };
    },
    mounted() {
        this.fetchTopStories();
    },
    methods: {
        async fetchWeeklyStories() {
            try {
                const numWeeksAgo = 1;
                const lastSunday = new Date();
                lastSunday.setDate(lastSunday.getDate() - lastSunday.getDay());
                lastSunday.setHours(0, 0, 0, 0);
                const lastSundayTimestamp = Math.floor(lastSunday.getTime() / 1000) - 604800 * numWeeksAgo;
                const previousSundayTimestamp = lastSundayTimestamp - 604800;
                const endDate = new Date(lastSundayTimestamp * 1000);
                const startDate = new Date(previousSundayTimestamp * 1000);
                console.log(startDate, endDate)
                const res = await fetch("http://hn.algolia.com/api/v1/search_by_date?tags=story&numericFilters=created_at_i>" + previousSundayTimestamp + ",created_at_i<" + lastSundayTimestamp + ",points>300&hitsPerPage=1000").then(res => res.json());
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
        async fetchTopStories() {
            let b = true;
            let stories = b ? await this.fetchWeeklyStories() : await this.fetchFrontPageStories();
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
        }
    }
}
</script>

<template>
    <div class="news-stories">
        <ul>
            <li v-for="story in stories.sort((a, b) => b.score - a.score)" :key="story.id">
                <!-- <a :href="story.url" target="_blank" rel="noopener">[{{ this.paddedScore(story.score) }}] {{ story.title }} </a> -->
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
    color: #828282;
    font-size: 0.8rem;
}

a {
    white-space: pre;
}

ul {
    list-style: none;
    /* padding: 0; */
}
</style>