<script setup>
import { ref } from 'vue'

const images = ref([
  // 示例图片，可根据实际情况修改
  { id: 1, src: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16886296355283529.jpg' },
  { id: 2, src: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16908819783322569.jpg' },
])

const selectedImage = ref(null)

const text = ref('封面标题')
const fontSize = ref(24)
const spacing = ref(10)
const author = ref('作者姓名')
</script>

<template>
  <div class="editor-container">
    <div class="image-list">
      <h2>图片列表</h2>
      <ul>
        <li v-for="image in images" :key="image.id" @click="selectedImage = image">
          <img :src="image.src" :alt="`封面图片 ${image.id}`" style="width: 100%;">
        </li>
      </ul>
    </div>
    <div class="preview-area">
      <h2>预览区域</h2>
      <div v-if="selectedImage">
        <img :src="selectedImage.src" :alt="`封面图片 ${selectedImage.id}`" style="max-width: 100%;">
        <h1 :style="{ fontSize: `${fontSize}px`, letterSpacing: `${spacing}px` }">{{ text }}</h1>
        <p :style="{ fontSize: `${fontSize}px`, letterSpacing: `${spacing}px` }">{{ author }}</p>
      </div>
    </div>
    <div class="settings-area">
      <h2>设置</h2>
      <div>
        <label>书名:</label>
        <input v-model="text" type="text">
      </div>
      <div>
        <label>作者:</label>
        <input v-model="author" type="text">
      </div>
      <div>
        <label>字体大小:</label>
        <input v-model.number="fontSize" type="number">
      </div>
      <div>
        <label>间距:</label>
        <input v-model.number="spacing" type="number">
      </div>
    </div>
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
.editor-container {
  display: flex;
  height: 100vh;
}

.image-list {
  width: 20%;
  padding: 20px;
  overflow-y: auto;
}

.preview-area {
  width: 60%;
  padding: 20px;
  border-left: 1px solid #ccc;
  border-right: 1px solid #ccc;
}

.settings-area {
  width: 20%;
  padding: 20px;
}
</style>