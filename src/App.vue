<script setup>
import { ref } from 'vue';
import NewsStories from './components/NewsStories.vue';
import WeeklySlider from './components/WeeklySlider.vue';
import DateRange from './components/DateRange.vue';

const isWeekly = ref(true)
const startDate = ref(new Date())
const endDate = ref(new Date())

console.log(isWeekly)
const toggleWeekly = (newValue) => {
  console.log("App.vue: toggleWeekly " + newValue)
  isWeekly.value = !newValue
}
const changeDates = (newDates) => {
  console.log("App.vue: toggleDates " + newDates)
  startDate.value = newDates.startDate
  endDate.value = newDates.endDate
}
</script>

<template>
  <header>
    <h1>Hacker News: Uncommented</h1>
  </header>

  <main>
    <WeeklySlider @toggle-weekly="toggleWeekly" />
    <DateRange v-if="isWeekly" @date-range="changeDates" />
    <NewsStories :isWeekly="isWeekly" :startDate="startDate" :endDate="endDate" />
    <!-- <span>Found a bug?</span> -->
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
  display: flex;
  place-items: center;
}

main {
  display: flex;
  flex-direction: column;
  place-items: center;
  /* border: 1px solid #ddd; */
  max-width: 100%;
}

h1 {
  font-size: 2rem;
  font-weight: 700;
  margin: auto;
}
</style>
