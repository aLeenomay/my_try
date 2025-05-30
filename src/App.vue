<template>
  <div class="live-page">
    <!-- 左侧视频区 -->
    <div class="video-section">
      <div class="video-wrapper">
        <video
          class="video-player"
          :src="videoSrc"
          controls
          autoplay
          muted
          playsinline
        ></video>

        <!-- 弹幕层 -->
        <div class="danmu-layer">
          <div
            v-for="(item, index) in danmus"
            :key="item.id"
            class="danmu"
            :style="{
              top: item.top + 'px',
              animationDuration: item.speed + 's'
            }"
            @animationend="() => removeDanmu(index)"
          >
            {{ item.text }}
          </div>
        </div>
      </div>
    </div>

    <!-- 右侧信息面板 -->
    <div class="info-section">
      <!-- 系统提示 -->
      <div class="system-message">
        系统提示：直播内容及互动评论严禁传播违法或不良信息，如有违反，小喇叭将采取封禁措施。
      </div>

      <!-- 弹幕输入框 -->
      <div class="danmu-input">
        <input v-model="danmuText" type="text" placeholder="发送弹幕..." />
        <button @click="sendDanmu">发送</button>
      </div>

      <!-- 视频上传接口 -->
      <div class="upload-area">
        <label for="videoUpload">上传视频文件：</label>
        <input id="videoUpload" type="file" accept="video/*" @change="handleUpload" />
      </div>

      <!-- 标签页 -->
      <div class="tabs">
        <button :class="{ active: activeTab === '讲解' }" @click="activeTab = '讲解'">讲解</button>
        <button :class="{ active: activeTab === '讨论' }" @click="activeTab = '讨论'">讨论</button>
        <button :class="{ active: activeTab === '文件' }" @click="activeTab = '文件'">文件</button>
      </div>

      <!-- 标签内容 -->
      <div class="tab-content">
        <div v-if="activeTab === '讲解'">
          <p>欢迎进入直播间：</p>
          <ul class="guides">
            <li>1. 请自行调节手机音量至合适的状态。</li>
            <li>2. 播放界面显示讲师发布的内容，听众可在讨论区进行提问或弹幕式查看。</li>
            <li>3. 直播结束后，你可以随时回看全部内容。</li>
          </ul>
        </div>
        <div v-if="activeTab === '讨论'">评论功能区（你可以嵌入输入框和评论列表）</div>
        <div v-if="activeTab === '文件'">下载文件区域（你可以加文件名、链接等）</div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import videoFile from './assets/ocean-480p-2500k.mp4'

const videoSrc = ref(videoFile)
const danmuText = ref('')
const danmus = ref<{ id: number; text: string; top: number; speed: number }[]>([])
const activeTab = ref('讲解')

// 发送弹幕
function sendDanmu() {
  if (danmuText.value.trim() === '') return
  const id = Date.now()
  const text = danmuText.value.trim()
  const top = Math.random() * 200 + 20
  const speed = Math.random() * 3 + 4
  danmus.value.push({ id, text, top, speed })
  danmuText.value = ''
}

// 动画结束后移除弹幕
function removeDanmu(index: number) {
  danmus.value.splice(index, 1)
}

// 上传本地视频
function handleUpload(event: Event) {
  const file = (event.target as HTMLInputElement).files?.[0]
  if (file) {
    const blobUrl = URL.createObjectURL(file)
    videoSrc.value = blobUrl
  }
}
</script>

<style scoped>
.live-page {
  display: flex;
  height: 100vh;
  width: 100vw;
  overflow: hidden;
  font-family: sans-serif;
  background-color: #111;
  color: white;
}

.video-section {
  flex: 0 0 65%;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: black;
  position: relative;
}

.video-wrapper {
  width: 100%;
  height: 100%;
  position: relative;
}

.video-player {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border: none;
}

/* 弹幕层 */
.danmu-layer {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
  overflow: hidden;
}

.danmu {
  position: absolute;
  left: 100%;
  white-space: nowrap;
  font-size: 1rem;
  color: black;
  text-shadow: 0 0 5px rgb(201, 34, 34);
  animation-name: danmu-move;
  animation-timing-function: linear;
  animation-fill-mode: forwards;
  pointer-events: none;
}

@keyframes danmu-move {
  0% {
    transform: translateX(0%);
  }
  100% {
    transform: translateX(-100vw);
  }
}

.info-section {
  flex: 0 0 35%;
  background-color: #1e1e1e;
  display: flex;
  flex-direction: column;
  padding: 1.5rem;
  box-sizing: border-box;
}

.system-message {
  background-color: #333;
  padding: 1rem;
  margin-bottom: 1rem;
  border-radius: 8px;
  font-size: 0.95rem;
}

.danmu-input {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.danmu-input input {
  flex: 1;
  padding: 0.5rem;
  background: #333;
  border: 1px solid #555;
  color: white;
  border-radius: 4px;
}

.danmu-input button {
  background-color: #00aaff;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
}

.upload-area {
  margin-bottom: 1rem;
}

.upload-area input {
  margin-top: 0.5rem;
}

.tabs {
  display: flex;
  gap: 1rem;
  margin-bottom: 1rem;
}

.tabs button {
  flex: 1;
  background-color: #2a2a2a;
  color: white;
  border: none;
  padding: 0.75rem 1rem;
  font-size: 1rem;
  cursor: pointer;
}

.tabs button.active {
  background-color: #444;
  border-bottom: 2px solid #00aaff;
  font-weight: bold;
}

.tab-content {
  flex: 1;
  overflow-y: auto;
  font-size: 1rem;
  line-height: 1.6;
}

.guides li {
  margin-bottom: 1rem;
}
</style>
