<template>
  <div class="analyzer-container">
    <div class="features-container">
      <div class="feature-card">
        <div class="feature-icon">📤</div>
        <h3>上传书签</h3>
        <p>支持上传浏览器导出的JSON格式书签文件，一键解析所有书签。</p>
      </div>
      <div class="feature-card">
        <div class="feature-icon">🔍</div>
        <h3>智能分类</h3>
        <p>自动按域名分类整理书签，清晰展示每个域名的书签数量。</p>
      </div>
      <div class="feature-card">
        <div class="feature-icon">📊</div>
        <h3>可视化展示</h3>
        <p>树形结构展示书签层级关系，支持展开/折叠查看详细内容。</p>
      </div>
    </div>
    <div class="info-card">
      <h3>书签分析工具</h3>
      <p>上传您的浏览器书签文件(JSON格式)，工具将自动分析并按域名分类展示。</p>
    </div>
    <div class="upload-section">
      <label class="upload-btn">
        选择书签文件
        <input type="file" @change="handleFileUpload" class="file-input" />
      </label>
    </div>
    <div v-if="analysisResult" class="result-section">
      <tree-view :data="analysisResult" />
    </div>
  </div>
</template>

<style scoped>
.analyzer-container {
  width: 90%;
  margin: 0;
  padding: 2rem;
  height: 100vh;
  overflow: auto;
  background-color: #1a1a1a;
  color: rgba(255, 255, 255, 0.87);
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.analyzer-container::-webkit-scrollbar {
  width: 8px;
}

.analyzer-container::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 10px;
}

.analyzer-container::-webkit-scrollbar-thumb {
  background: #888;
  border-radius: 10px;
}

.analyzer-container::-webkit-scrollbar-thumb:hover {
  background: #555;
}

.upload-section {
  text-align: center;
}

.upload-btn {
  display: inline-block;
  padding: 0.75rem 1.5rem;
  background-color: #4CAF50;
  color: white;
  border-radius: 0.5rem;
  cursor: pointer;
  transition: all 0.2s ease;
  font-weight: 500;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.upload-btn:hover {
  background-color: #388E3C;
  transform: translateY(-2px);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.15);
}

.file-input {
  display: none;
}

.features-container {
  display: flex;
  flex-wrap: wrap;
  gap: 1.5rem;
  margin-bottom: 2rem;
  justify-content: center;
}

.feature-card {
  background: #2d2d2d;
  border-radius: 0.5rem;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
  padding: 1.5rem;
  animation: fadeIn 0.3s ease;
  color: rgba(255, 255, 255, 0.87);
  transition: all 0.2s ease;
}

.feature-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 15px rgba(0, 0, 0, 0.3);
}

.feature-icon {
  font-size: 2rem;
  margin-bottom: 1rem;
}

.feature-card h3 {
  margin: 0 0 0.5rem 0;
  color: #4CAF50;
}

.feature-card p {
  margin: 0;
  font-size: 0.9rem;
  opacity: 0.8;
}

.info-card {
  background: #2d2d2d;
  border-radius: 0.5rem;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
  padding: 1.5rem;
  animation: fadeIn 0.3s ease;
  color: rgba(255, 255, 255, 0.87);
}

.result-section {
  background: #2d2d2d;
  border-radius: 0.5rem;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
  padding: 1.5rem;
  animation: fadeIn 0.3s ease;
  color: rgba(255, 255, 255, 0.87);
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>

<script setup lang="ts">
import { ref } from 'vue'
import TreeView from './TreeView.vue'

interface Bookmark {
  title: string
  url: string
  parents: string[]
}

interface AnalysisResult {
  domain: string
  count: number
  bookmarks: Bookmark[]
}

const analysisResult = ref<AnalysisResult[] | null>(null)

function handleFileUpload(event: Event) {
  const file = (event.target as HTMLInputElement).files?.[0]
  if (file) {
    const reader = new FileReader()
    reader.onload = (e) => {
      const bookmarks = parseBookmarks(e.target?.result as string)
      analysisResult.value = analyzeBookmarks(bookmarks)
    }
    reader.readAsText(file)
  }
}

function parseBookmarks(jsonStr: string): Bookmark[] {
  if (jsonStr) {
    try {
      const data = JSON.parse(jsonStr);
      const bookmarks: Bookmark[] = [];
      
      function traverseNodes(nodes: any[], parents: string[] = []) {
        nodes.forEach(node => {
          if (node.type === 'url' && node.url) {
            bookmarks.push({
              title: node.name || '',
              url: node.url,
              parents: [...parents]
            });
          }
          if (node.children) {
            const newParents = [...parents];
            if (node.name) {
              newParents.push(node.name);
            }
            traverseNodes(node.children, newParents);
          }
        });
      }
      
      if (data.roots) {
        Object.values(data.roots).forEach(root => {
          if ((root as { children?: any[] }).children) {
            const typedRoot = root as { children?: any[] };
            if (typedRoot.children) {
              traverseNodes(typedRoot.children);
            }
          }
        });
      }
      
      return bookmarks;
    } catch (e) {
      console.error('Error parsing bookmarks JSON:', e);
    }
  }
  return [];
}

function analyzeBookmarks(bookmarks: Bookmark[]): AnalysisResult[] {
  const domainMap = new Map<string, { count: number; bookmarks: Bookmark[] }>();
  bookmarks.forEach(bookmark => {
    const url = new URL(bookmark.url);
    const domain = url.hostname;
    if (domainMap.has(domain)) {
      const entry = domainMap.get(domain)!;
      entry.count++;
      entry.bookmarks.push(bookmark);
    } else {
      domainMap.set(domain, { count: 1, bookmarks: [bookmark] });
    }
  });
  const result: AnalysisResult[] = [];
  domainMap.forEach((value, key) => {
    result.push({
      domain: key,
      count: value.count,
      bookmarks: value.bookmarks
    });
  });
  // 按count降序排序
  return result.sort((a, b) => b.count - a.count);
}
</script>