<script setup lang="ts">
import BookmarkAnalyzer from './components/BookmarkAnalyzer.vue'
</script>

<template>
  <BookmarkAnalyzer />
</template>

<style>
:root {
  --bg-color: #1a1a1a;
  --text-color: rgba(255, 255, 255, 0.87);
  --card-bg: #2d2d2d;
  --card-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
  --primary-color: #4CAF50;
  --hover-color: #3a3a3a;
}

body {
  background-color: var(--bg-color);
  color: var(--text-color);
  margin: 0;
  padding: 0;
  font-family: Inter, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu,
    Cantarell, 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif;
}

#app {
  /* width: 100vw; */
  /* margin: 0 auto; */
  padding: 2rem;
  font-weight: normal;
  overflow-x: hidden;
}
</style>
