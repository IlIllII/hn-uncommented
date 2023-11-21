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
    <div>
        <input type="number" min="1" max="52" v-model="weeksAgo" @change="this.computeDateRange">
        <span>weeks ago ({{ this.zeroPaddedDate(this.startDate) }} to {{ this.zeroPaddedDate(this.endDate) }})</span>
    </div>
</template>


<style scoped>
div {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    margin: 0 0 10px 0 ;
    gap: 10px;
    /* Adjust the space between the input and the label */
    padding: 0 0 10px 0;
    /* background-color: #f5f5f5; */
    /* Light gray background for better visibility */
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    /* Soft shadow for depth */
}

input[type="number"] {
    padding: 8px;
    border: 1px solid #ccc;
    /* Light border */
    border-radius: 4px;
    background-color: var(--color-background);
    color: var(--color-text);
    width: 60px;
    /* Fixed width for the input */
    text-align: center;
    /* Center the text inside the input */
    outline: none;
    /* Removes the default focus outline */
}

input[type="number"]:focus {
    border-color: #007bff;
    /* Highlight color when focused */
    box-shadow: 0 0 0 2px rgba(0, 123, 255, 0.25);
    /* Glow effect on focus */
}

span {
    font-size: 1rem;
    /* Reasonable font size */
    /* color: #333; */
    /* Darker text for better readability */
}
</style>
