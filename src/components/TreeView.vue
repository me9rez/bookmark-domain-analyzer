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
          <div class="bookmark-item">
            <a :href="bookmark.url" target="_blank">{{ bookmark.title }}<span class="link-icon">↗</span></a>
            <div v-if="bookmark.parents && bookmark.parents.length" class="parents-path">
              <span v-for="(parent, i) in bookmark.parents" :key="i">
                {{ parent }}<span v-if="i < bookmark.parents.length - 1"> > </span>
              </span>
            </div>
            <div class="url-display" @click="copyUrl(bookmark.url)">
              <span class="url-text">{{ bookmark.url }}</span>
              <span class="copy-hint">点击复制</span>
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

const copyUrl = (url: string) => {
  navigator.clipboard.writeText(url)
    .then(() => {
      alert('URL已复制到剪贴板');
    })
    .catch(err => {
      console.error('复制失败:', err);
    });
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
  color: #64b5f6;
  font-size: 0.9em;
  margin-left: 8px;
  font-weight: 500;
}

li>ul {
  margin-top: 8px;
  padding-left: 24px;
  background-color: rgba(79, 70, 229, 0.1);
  border-radius: var(--border-radius);
  padding: 12px 16px;
}

li>ul li {
  margin-bottom: 4px;
  padding: 4px 0 4px 12px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  position: relative;
  left: 12px;
}

a {
  display: block;
  padding: 6px 10px;
  background: var(--card-bg);
  box-shadow: var(--card-shadow);
  border-radius: var(--border-radius);
  color: #ffffff;
  text-decoration: none;
  transition: all 0.2s ease;
  margin-bottom: 4px;
}

.parents-path {
  font-size: 0.75em;
  color: rgba(255, 255, 255, 0.8);
  margin: 6px 0 4px 0;
  line-height: 1.4;
  padding: 4px 8px;
  border-radius: 4px;
  background: rgba(255, 255, 255, 0.05);
}

.parents-path::before {
  content: '📁 ';
}

.bookmark-item {
  display: flex;
  align-items: center;
  gap: 12px;
}

.url-display {
  font-size: 0.7em;
  color: #64b5f6;
  font-weight: 500;
  margin-top: 2px;
  padding: 3px 6px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 4px;
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 4px;
}

.url-display:hover {
  background: rgba(255, 255, 255, 0.15);
  color: #90caf9;
}

.copy-hint {
  font-size: 0.6em;
  color: var(--primary-color);
  opacity: 0;
  transition: opacity 0.2s;
}

.url-display:hover .copy-hint {
  opacity: 1;
}

a:hover {
  color: #ffffff;
  text-decoration: underline;
  opacity: 0.8;
}
</style>