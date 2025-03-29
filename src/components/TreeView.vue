<template>
  <div class="domain-nav-container">
    <div class="domain-nav">
      <div v-for="(item, index) in data" :key="index" @click="scrollToDomain(index)">
        <span>{{ item.domain }}</span>
        <span>({{ item.count }})</span>
      </div>
    </div>
  </div>
  <ul class="scroll-container">
    <li v-for="(item, index) in data" :key="index">
      <div @click="toggleExpand(index)">
        <span class="toggle-icon">{{ isExpanded(index) ? '▼' : '▶' }}</span>
        <span class="domain-icon">🌐</span>
        <span class="domain">{{ item.domain }}</span>
        <span class="count">({{ item.count }})</span>
      </div>
      <ul v-if="item.bookmarks.length && isExpanded(index)">
        <li v-for="(bookmark, idx) in item.bookmarks" :key="idx">
          <div>
            <a :href="bookmark.url" target="_blank">{{ bookmark.title }}<span class="link-icon">↗</span></a>
            <div v-if="bookmark.parents && bookmark.parents.length" class="parents-path">
              <span v-for="(parent, i) in bookmark.parents" :key="i">
                {{ parent }}<span v-if="i < bookmark.parents.length - 1"> > </span>
              </span>
            </div>
          </div>
        </li>
      </ul>
    </li>
  </ul>
</template>

<script setup lang="ts">
import { ref } from 'vue'

defineProps<{
  data: {
    domain: string
    count: number
    bookmarks: {
      title: string
      url: string
      parents?: string[]
    }[]
  }[]
}>()

const expandedItems = ref<number[]>([])

const toggleExpand = (index: number) => {
  const idx = expandedItems.value.indexOf(index)
  if (idx === -1) {
    expandedItems.value.push(index)
  } else {
    expandedItems.value.splice(idx, 1)
  }
}

const isExpanded = (index: number) => {
  return expandedItems.value.includes(index)
}

const scrollToDomain = (index: number) => {
  const container = document.querySelector('.scroll-container');
  const target = container?.querySelectorAll('li')[index];
  if (target) {
    target.scrollIntoView({ behavior: 'smooth', block: 'start' });
  }
}
</script>
<style scoped>
:root {
  --card-bg: #2d2d2d;
  --card-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
  --primary-color: #4f46e5;
  --hover-color: #3a3a3a;
  --text-color: rgba(255, 255, 255, 0.87);
  --border-radius: 8px;
}

ul {
  list-style-type: none;
  padding-left: 0;
}

.domain-nav-container {
  position: sticky;
  top: 0;
  background: var(--card-bg);
  z-index: 100;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  overflow-x: auto;
  scroll-behavior: smooth;
}

.domain-nav {
  display: flex;
  gap: 16px;
  padding: 12px 16px;
  min-width: fit-content;
}

.domain-nav > div {
  cursor: pointer;
  padding: 4px 8px;
  border-radius: 4px;
}

.domain-nav > div:hover {
  background: var(--hover-color);
}

.scroll-container {
  max-height: 70vh;
  overflow-y: auto;
  scroll-behavior: smooth;
}

li {
  margin-bottom: 8px;
}

li>div {
  background: var(--card-bg);
  box-shadow: var(--card-shadow);
  border-radius: var(--border-radius);
  padding: 12px 16px;
  color: var(--text-color);
  font-weight: 600;
  font-size: 1.1em;
  transition: all 0.2s ease;
  cursor: pointer;
  display: flex;
  align-items: center;
}

li>div:hover {
  background: var(--hover-color);
  transform: translateY(-2px);
}

li>div span.domain {
  color: var(--primary-color);
  font-weight: 700;
}

.toggle-icon {
  margin-right: 8px;
  width: 16px;
  display: inline-block;
}

.domain-icon {
  margin-right: 8px;
}

.link-icon {
  margin-left: 4px;
  font-size: 0.8em;
}

li>div span.count {
  color: var(--text-color);
  opacity: 0.8;
}

li>ul {
  margin-top: 8px;
  padding-left: 24px;
  background-color: rgba(79, 70, 229, 0.1);
  border-radius: var(--border-radius);
  padding: 12px 16px;
}

li>ul li {
  margin-bottom: 8px;
  padding: 6px 0 6px 16px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  position: relative;
  left: 16px;
}

a {
  display: block;
  padding: 8px 12px;
  background: var(--card-bg);
  box-shadow: var(--card-shadow);
  border-radius: var(--border-radius);
  color: #ffffff;
  text-decoration: none;
  transition: all 0.2s ease;
}

.parents-path {
  font-size: 0.8em;
  color: rgba(255, 255, 255, 0.6);
  margin-top: 4px;
  padding-left: 12px;
}

a:hover {
  color: #ffffff;
  text-decoration: underline;
  opacity: 0.8;
}
</style>