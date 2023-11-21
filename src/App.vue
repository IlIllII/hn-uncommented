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
    <div class="wrapper">
      <WeeklySlider @toggle-weekly="toggleWeekly" />
      <DateRange v-if="isWeekly" @date-range="changeDates" />
    </div>
  </header>

  <main>
    <NewsStories :isWeekly="isWeekly" :startDate="startDate" :endDate="endDate" />
    <!-- <span>Found a bug?</span> -->
  </main>
</template>

<style scoped>
.wrapper {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  flex-wrap: wrap;
  gap: 10px;
}

header {
  line-height: 1.5;
  display: flex;
  flex-direction: column;
  place-items: baseline;
  flex-wrap: wrap;
  gap: 10px;
}


main {
  display: flex;
  flex-direction: column;
  place-items: flex-start;
  max-width: 100%;
}

h1 {
  font-size: 2rem;
  font-weight: 700;
}
</style>
