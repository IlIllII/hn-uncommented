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
        async fetchTopStories() {
            try {
                const response = await fetch('https://hacker-news.firebaseio.com/v0/topstories.json');
                const storyIds = await response.json();
                const topIds = storyIds.slice(0, 30);

                const storyPromises = topIds.map(id =>
                    fetch(`https://hacker-news.firebaseio.com/v0/item/${id}.json`).then(response => response.json())
                );

                this.stories = await Promise.all(storyPromises);
                this.max_score = Math.max(...this.stories.map(story => story.score));

            } catch (error) {
                console.error("An error occurred while fetching data:", error);
            }
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
                <a :href="story.url" target="_blank" rel="noopener">[{{ this.paddedScore(story.score) }}] {{ story.title }} </a>
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