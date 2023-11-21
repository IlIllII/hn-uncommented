<script>
export default {

    data() {
        return {
            weeksAgo: 1,
            startDate: new Date(),
            endDate: new Date(),
        }
    },

    methods: {
        computeDateRange() {
            const lastSunday = new Date();
            lastSunday.setDate(lastSunday.getDate() - lastSunday.getDay());
            lastSunday.setHours(0, 0, 0, 0);
            const lastSundayTimestamp = Math.floor(lastSunday.getTime() / 1000) - 604800 * this.weeksAgo;
            const previousSundayTimestamp = lastSundayTimestamp - 604800;
            const endDate = new Date(lastSundayTimestamp * 1000);
            const startDate = new Date(previousSundayTimestamp * 1000);
            this.startDate = startDate
            this.endDate = endDate
            const result = {
                startDate,
                endDate
            }
            this.$emit('date-range', result)
        },

        zeroPaddedDate(date) {
            const year = date.getFullYear();
            const month = (date.getMonth() + 1).toString().padStart(2, "0");
            const day = date.getDate().toString().padStart(2, "0");
            return `${year}-${month}-${day}`;
        }
    },

    mounted() {
        this.computeDateRange()
    },
}
</script>


<template>
    <div class="bordered">
        <input type="number" min="1" max="52" v-model="weeksAgo" @change="this.computeDateRange">
        <span>weeks ago ({{ this.zeroPaddedDate(this.startDate) }} to {{ this.zeroPaddedDate(this.endDate) }})</span>
    </div>
</template>


<style scoped>
.bordered {
    border: 1px solid var(--color-text);
    border-radius: 12px;
    padding: 0 4px;
}

div {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    gap: 10px;
}

input[type="number"] {
    padding: 8px;
    border: 1px solid var(--color-text);
    border: none;
    border-radius: 12px;
    background-color: var(--color-background);
    color: var(--color-text);
    width: 60px;
    text-align: center;
    outline: none;
}

input[type="number"]:focus {
    border-color: var(--color-highlight);
    box-shadow: 0 0 0 2px rgba(0, 123, 255, 0.25);
}

span {
    font-size: 1rem;
}
</style>
