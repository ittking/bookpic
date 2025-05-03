<template>
  <!-- 顶部导航栏 -->
  <div class="navbar">
    <div class="navbar-left">
      <el-button link class="back-btn" @click="goBack">
        <el-icon><i-ep-arrow-left /></el-icon>
      </el-button>
      <img src="https://placehold.co/32x32?text=LOGO" class="logo" alt="logo" />
      <span class="navbar-title">七猫作家中心 · 封面编辑器</span>
    </div>
    <div class="navbar-right">
      <el-button class="nav-btn" round>制作指南</el-button>
      <el-button class="nav-btn" round>移除模板</el-button>
      <el-button class="nav-btn" type="warning" round>保存封面</el-button>
    </div>
  </div>
  <!-- 主体内容flex布局 -->
  <div class="main-flex-layout">
    <!-- 左侧底图样式列表 -->
    <div class="template-list">
      <div class="template-flex-list">
        <div class="template-flex-row" v-for="(row, rowIdx) in templateRows" :key="rowIdx">
          <div
            v-for="(item, colIdx) in templateList.slice(rowIdx * 3, rowIdx * 3 + 3)"
            :key="colIdx + '-' + rowIdx"
            class="template-bg-item"
            :class="{'selected': selectedIndex === (rowIdx * 3 + colIdx)}"
            @click="selectTemplate(rowIdx * 3 + colIdx)"
          >
            <div class="template-bg-img-wrap">
              <img :src="item.img" alt="模板底图" class="template-bg-img" />
              <span v-if="selectedIndex === (rowIdx * 3 + colIdx)" class="template-bg-check">
                <el-icon><i-ep-check /></el-icon>
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- 中间封面预览 -->
    <div class="cover-preview">
      <div class="cover-image-canvas-wrap">
        <canvas ref="coverCanvas" :width="canvasWidth" :height="canvasHeight" class="cover-canvas"></canvas>
        <div
          class="draggable-text"
          :style="{ top: mainTitlePosY + 'px', left: '0', width: '100%' }"
          @mousedown="startDrag('mainTitle', $event)"
        >
          <div class="canvas-main-title" :style="mainTitleStyle">{{ mainTitle }}</div>
        </div>
        <div
          class="draggable-text"
          :style="{ top: subTitlePosY + 'px', left: '0', width: '100%' }"
          @mousedown="startDrag('subTitle', $event)"
        >
          <div class="canvas-sub-title" :style="subTitleStyle">{{ subTitle }}</div>
        </div>
        <div
          class="draggable-text"
          :style="{ top: authorPosY + 'px', left: '0', width: '100%' }"
          @mousedown="startDrag('author', $event)"
        >
          <div class="canvas-author" :style="authorStyle">{{ authorName }} 著</div>
        </div>
      </div>
    </div>
    <!-- 右侧文字编辑区 -->
    <div class="text-editor">
      <el-form class="p-4" label-width="120px" label-position="left">
        <el-form-item label="主标题">
          <el-input v-model="mainTitle" placeholder="请输入主标题" />
        </el-form-item>
        <el-form-item label="主标题字号">
          <el-slider v-model="mainTitleFontSize" :min="24" :max="60" />
        </el-form-item>
        <el-form-item label="主标题字距">
          <el-slider v-model="mainTitleLetterSpacing" :min="0" :max="20" />
        </el-form-item>
        <el-form-item label="副标题">
          <el-input v-model="subTitle" placeholder="请输入副标题" />
        </el-form-item>
        <el-form-item label="副标题字号">
          <el-slider v-model="subTitleFontSize" :min="16" :max="40" />
        </el-form-item>
        <el-form-item label="副标题字距">
          <el-slider v-model="subTitleLetterSpacing" :min="0" :max="20" />
        </el-form-item>
        <el-form-item label="作者名称">
          <el-input v-model="authorName" placeholder="请输入作者名称" />
        </el-form-item>
        <el-form-item label="作者字号">
          <el-slider v-model="authorFontSize" :min="12" :max="32" />
        </el-form-item>
        <el-form-item label="作者颜色">
          <el-color-picker v-model="authorColor" />
        </el-form-item>
        <el-form-item label="字体">
          <el-select v-model="fontFamily" placeholder="选择字体">
            <el-option label="思源黑体" value="思源黑体" />
            <el-option label="微软雅黑" value="微软雅黑" />
          </el-select>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted, onBeforeUnmount } from 'vue'
import { useRouter } from 'vue-router'
const fontFamily = ref('思源黑体')
const mainTitle = ref('让你学习')
const subTitle = ref('没让你修仙')
const authorName = ref('老婆叫我来码字')
const mainTitleFontSize = ref(38)
const mainTitleLetterSpacing = ref(0)
const subTitleFontSize = ref(28)
const subTitleLetterSpacing = ref(0)
const mainTitlePosY = ref(180)
const subTitlePosY = ref(240)
const authorPosY = ref(400)
const authorFontSize = ref(18)
const authorColor = ref('#fff')
const mainTitleStyle = computed(() => ({
  fontFamily: fontFamily.value,
  fontSize: mainTitleFontSize.value + 'px',
  letterSpacing: mainTitleLetterSpacing.value + 'px',
  fontWeight: 'bold',
  textAlign: 'center',
  color: '#fff',
  textShadow: '0 2px 8px rgba(0,0,0,0.3)'
}))
const subTitleStyle = computed(() => ({
  fontFamily: fontFamily.value,
  fontSize: subTitleFontSize.value + 'px',
  letterSpacing: subTitleLetterSpacing.value + 'px',
  fontWeight: 'normal',
  textAlign: 'center',
  color: '#fff',
  textShadow: '0 2px 8px rgba(0,0,0,0.3)'
}))
const authorStyle = computed(() => ({
  fontFamily: fontFamily.value,
  fontSize: authorFontSize.value + 'px',
  color: authorColor.value,
  textAlign: 'center',
  textShadow: '0 2px 8px rgba(0,0,0,0.2)'
}))
const templateList = ref([
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16938163019852403.jpg', usage: 47 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097791279568769.jpg', usage: 38 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17067550647572577.jpg', usage: 48 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/CWY60.jpg', usage: 82 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/CWY68.jpg', usage: 101 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097790661070900.jpg', usage: 38 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17090030425892866.jpg', usage: 35 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17034928376689932.jpg', usage: 52 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17090027842970301.jpg', usage: 42 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17090027836127168.jpg', usage: 31 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097792733621671.jpg', usage: 13 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16981391587568760.jpg', usage: 158 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17090027829803583.jpg', usage: 64 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16957091536423614.jpg', usage: 42 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17034929303519351.jpg', usage: 43 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16981391588509800.jpg', usage: 128 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17034928371252963.jpg', usage: 66 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16938164886070975.jpg', usage: 56 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17034929308479456.jpg', usage: 36 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16957091546060307.jpg', usage: 37 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16938165208990637.jpg', usage: 57 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16957091549164497.jpg', usage: 43 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097793913190958.jpg', usage: 7 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17090030178804416.jpg', usage: 45 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097793902806402.jpg', usage: 8 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097792763783795.jpg', usage: 7 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16970168355267810.jpg', usage: 47 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/CWX34.jpg', usage: 25 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/CWY63.jpg', usage: 45 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16938165197236505.jpg', usage: 43 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17067554674990259.jpg', usage: 46 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16938165227313157.jpg', usage: 68 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16903549947834339.jpg', usage: 58 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16908800168006460.jpg', usage: 40 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097790663466925.jpg', usage: 7 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16970168349985907.jpg', usage: 48 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17090027836144025.jpg', usage: 60 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097794194296271.jpg', usage: 7 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16938165211119981.jpg', usage: 61 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16957091554430270.jpg', usage: 83 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16903549959903650.jpg', usage: 43 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097794195951488.jpg', usage: 7 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16957091556957613.jpg', usage: 208 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097794218211983.jpg', usage: 13 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16938165205608065.jpg', usage: 35 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097794219228784.jpg', usage: 7 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/16970168346725178.jpg', usage: 43 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17090030422044801.jpg', usage: 68 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097792744999603.jpg', usage: 33 },
  { img: 'https://cdn.qimao.com/bookimg/zww/upload/cover/tpl/source/17097792042099748.jpg', usage: 7 },
])
const templateCount = templateList.value.length
const templateRows = computed(() => Math.ceil(templateCount / 3))
const selectedIndex = ref(0)
const router = useRouter()
const canvasWidth = 360
const canvasHeight = 480
const coverCanvas = ref(null)
let dragging = ref(false)
let dragType = ''
let dragStartY = 0
let dragOriginY = 0

function selectTemplate(idx) {
  selectedIndex.value = idx
}
function saveCover() {
  // 保存封面逻辑
}
function goBack() {
  router.back()
}

// 生成水印数据
function genWatermarks() {
  const count = Math.floor(Math.random() * 7) + 6 // 6-12
  const arr = []
  for (let i = 0; i < count; i++) {
    arr.push({
      x: Math.random() * (canvasWidth - 120) + 60,
      y: Math.random() * (canvasHeight - 40) + 20,
      angle: Math.random() * 360,
      alpha: 0.12 + Math.random() * 0.08
    })
  }
  return arr
}
const watermarks = ref(genWatermarks())

// 重新生成水印（如需刷新）
function refreshWatermarks() {
  watermarks.value = genWatermarks()
}

// 渲染canvas
function renderCanvas() {
  const ctx = coverCanvas.value.getContext('2d')
  ctx.clearRect(0, 0, canvasWidth, canvasHeight)
  // 底图
  const img = new window.Image()
  img.crossOrigin = 'anonymous'
  img.src = templateList.value[selectedIndex.value]?.img
  img.onload = () => {
    ctx.drawImage(img, 0, 0, canvasWidth, canvasHeight)
    // 水印
    watermarks.value.forEach(wm => {
      ctx.save()
      ctx.globalAlpha = wm.alpha
      ctx.translate(wm.x, wm.y)
      ctx.rotate((wm.angle * Math.PI) / 180)
      ctx.font = 'bold 20px sans-serif'
      ctx.fillStyle = '#fff'
      ctx.textAlign = 'center'
      ctx.textBaseline = 'middle'
      ctx.strokeStyle = 'rgba(0,0,0,0.08)'
      ctx.lineWidth = 2
      ctx.strokeText('火猿封面设计', 0, 0)
      ctx.fillText('火猿封面设计', 0, 0)
      ctx.restore()
    })
    // 作品名/作者（仅做预览，实际由div渲染可拖拽）
    // ...canvas不渲染文字，由div渲染...
  }
}

// 拖拽逻辑
function startDrag(type, e) {
  dragging.value = true
  dragType = type
  dragStartY = e.clientY
  if (type === 'mainTitle') dragOriginY = mainTitlePosY.value
  else if (type === 'subTitle') dragOriginY = subTitlePosY.value
  else if (type === 'author') dragOriginY = authorPosY.value
  document.addEventListener('mousemove', onDrag)
  document.addEventListener('mouseup', stopDrag)
}
function onDrag(e) {
  if (!dragging.value) return
  const deltaY = e.clientY - dragStartY
  if (dragType === 'mainTitle') {
    mainTitlePosY.value = Math.max(0, Math.min(600 - 60, dragOriginY + deltaY))
  } else if (dragType === 'subTitle') {
    subTitlePosY.value = Math.max(0, Math.min(600 - 40, dragOriginY + deltaY))
  } else if (dragType === 'author') {
    authorPosY.value = Math.max(0, Math.min(600 - 40, dragOriginY + deltaY))
  }
}
function stopDrag() {
  dragging.value = false
  dragType = ''
  document.removeEventListener('mousemove', onDrag)
  document.removeEventListener('mouseup', stopDrag)
}

// 监听底图/水印变化
watch([selectedIndex, watermarks], () => {
  renderCanvas()
})
onMounted(() => {
  renderCanvas()
})
onBeforeUnmount(() => {
  document.removeEventListener('mousemove', onDrag)
  document.removeEventListener('mouseup', stopDrag)
})
</script>

<style scoped>
.navbar {
  width: 100vw;
  height: 56px;
  background: #f7fbfa;
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-bottom: 1px solid #eee;
  padding: 0 32px 0 16px;
  box-sizing: border-box;
}
.navbar-left {
  display: flex;
  align-items: center;
}
.back-btn {
  margin-right: 8px;
  font-size: 20px;
  color: #222;
}
.logo {
  width: 32px;
  height: 32px;
  border-radius: 8px;
  margin-right: 8px;
}
.navbar-title {
  font-size: 16px;
  font-weight: 600;
  color: #222;
}
.navbar-right {
  display: flex;
  align-items: center;
  gap: 12px;
}
.nav-btn {
  font-weight: 500;
  font-size: 14px;
  padding: 6px 18px;
}
.main-flex-layout {
  display: flex;
  flex-direction: row;
  width: 100vw;
  height: calc(100vh - 56px);
  background: #f7fbfa;
}
.template-list {
  width: 380px;
  background: #fff;
  border-right: 1px solid #eee;
  padding: 16px 8px 16px 0;
  height: 100%;
  overflow-y: auto;
}
.template-flex-list {
  display: flex;
  flex-direction: column;
  gap: 16px;
}
.template-flex-row {
  display: flex;
  flex-direction: row;
  gap: 12px;
  margin-bottom: 0;
}
.template-bg-item {
  flex: 1 1 0;
  min-width: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  cursor: pointer;
  background: #fff;
  border: 2px solid transparent;
  transition: border 0.2s;
  position: relative;
  margin-bottom: 0;
  padding: 0;
  box-shadow: none;
}
.template-bg-item.selected .template-bg-img-wrap {
  border: 2px solid #ffd900;
  box-shadow: none;
}
.template-bg-img-wrap {
  position: relative;
  width: 90px;
  height: 120px;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 12px;

}
.template-bg-img {
  width: 100%;
  height: 120px;
  border-radius: 12px;
  object-fit: cover;
  display: block;
  box-shadow: none;
}
.template-bg-check {
  display: none;
}
.template-bg-usage {
  margin-top: 6px;
  margin-bottom: 8px;
  font-size: 13px;
  color: #fff;
  background: rgba(0,0,0,0.6);
  border-radius: 8px;
  padding: 2px 10px;
  align-self: center;
  position: relative;
  top: -12px;
  z-index: 1;
  font-weight: 500;
}
.cover-preview {
  flex: 1 1 0;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
}
.cover-image-canvas-wrap {
  position: relative;
  width: 360px;
  height: 480px;
  background: #eee;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 24px rgba(0,0,0,0.08);
}
.cover-canvas {
  width: 360px;
  height: 480px;
  display: block;
  border-radius: 12px;
}
.draggable-text {
  position: absolute;
  left: 0;
  width: 100%;
  cursor: grab;
  user-select: none;
  z-index: 10;
  text-align: center;
}
.canvas-main-title {
  color: #fff;
  font-size: 38px;
  font-weight: bold;
  text-shadow: 0 2px 8px rgba(0,0,0,0.3);
  pointer-events: none;
}
.canvas-sub-title {
  color: #fff;
  font-size: 28px;
  font-weight: normal;
  text-shadow: 0 2px 8px rgba(0,0,0,0.3);
  pointer-events: none;
}
.canvas-author {
  color: #fff;
  font-size: 18px;
  text-shadow: 0 2px 8px rgba(0,0,0,0.2);
  pointer-events: none;
}
.text-editor {
  width: 400px;
  background: #fff;
  border-left: 1px solid #eee;
  height: 100%;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}
</style>