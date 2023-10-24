<script setup>
import NewsStories from './components/NewsStories.vue';

</script>

<template>
  <header>
    <h1>Hacker News: Uncommented</h1>
  </header>
  
  <main>
    <NewsStories />
  </main>

</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
h1 {
  font-size: 2rem;
  font-weight: 700;
  margin: auto;
  padding: 1rem;
}
</style>
