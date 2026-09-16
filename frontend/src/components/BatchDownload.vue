<template>
  <div class="max-w-4xl mx-auto px-4 sm:px-6 py-8">
    <!-- 批量输入区 -->
    <div class="glass-card rounded-2xl p-5 sm:p-6">
      <div class="flex items-center gap-2 mb-4">
        <div class="w-10 h-10 rounded-xl gradient-bg flex items-center justify-center">
          <svg class="w-5 h-5 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M4 6h16M4 10h16M4 14h10M4 18h10" />
          </svg>
        </div>
        <div>
          <h2 class="text-lg font-semibold text-text-primary">批量下载</h2>
          <p class="text-xs text-text-muted">每行一个视频链接，支持批量解析和下载</p>
        </div>
      </div>

      <textarea
        v-model="rawInput"
        rows="6"
        placeholder="粘贴多个视频链接，每行一个&#10;https://www.youtube.com/watch?v=...&#10;https://www.bilibili.com/video/...&#10;https://www.douyin.com/video/..."
        class="w-full px-4 py-3 rounded-xl bg-bg-card border border-border text-sm text-text-primary placeholder:text-text-muted focus:outline-none focus:ring-2 focus:ring-primary/30 focus:border-primary transition-all resize-y font-mono"
        :disabled="parsing"
      ></textarea>

      <div class="flex flex-col sm:flex-row items-stretch sm:items-center gap-3 mt-4">
        <button
          @click="startBatchParse"
          :disabled="parsing || !rawInput.trim()"
          class="flex-1 sm:flex-initial inline-flex items-center justify-center gap-2 h-12 px-8 rounded-xl gradient-bg text-white font-medium transition-all disabled:opacity-50 disabled:cursor-not-allowed glow hover:opacity-90 cursor-pointer"
        >
          <svg v-if="parsing" class="animate-spin w-5 h-5" fill="none" viewBox="0 0 24 24">
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
          </svg>
          <svg v-else class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" />
          </svg>
          {{ parsing ? '解析中...' : '批量解析' }}
        </button>

        <button
          v-if="queue.length > 0"
          @click="downloadAll"
          :disabled="downloading || readyCount === 0"
          class="flex-1 sm:flex-initial inline-flex items-center justify-center gap-2 h-12 px-8 rounded-xl border border-border bg-bg-card text-text-primary font-medium hover:border-primary hover:text-primary transition-all disabled:opacity-50 disabled:cursor-not-allowed cursor-pointer"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
          </svg>
          全部下载
        </button>

        <button
          v-if="queue.length > 0"
          @click="clearQueue"
          class="inline-flex items-center justify-center gap-2 h-12 px-4 rounded-xl border border-border bg-bg-card text-text-muted hover:text-error hover:border-error transition-all cursor-pointer"
        >
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
          </svg>
          清空
        </button>
      </div>

      <!-- 进度条 -->
      <div v-if="queue.length > 0" class="mt-4">
        <div class="flex items-center justify-between text-xs text-text-secondary mb-2">
          <span>已完成 {{ doneCount }}/{{ queue.length }}</span>
          <span class="text-text-muted">{{ Math.round(doneCount / queue.length * 100) }}%</span>
        </div>
        <div class="h-2 rounded-full bg-bg-card overflow-hidden">
          <div
            class="h-full gradient-bg transition-all duration-500"
            :style="{ width: (doneCount / queue.length * 100) + '%' }"
          ></div>
        </div>
      </div>
    </div>

    <!-- 队列列表 -->
    <div v-if="queue.length > 0" class="mt-6 space-y-3">
      <div
        v-for="(item, index) in queue"
        :key="index"
        class="glass-card rounded-xl p-4 flex items-center gap-4"
      >
        <!-- 缩略图 -->
        <div class="flex-shrink-0 w-24 h-16 sm:w-32 sm:h-20 rounded-lg overflow-hidden bg-bg-card border border-border">
          <img
            v-if="item.videoData?.thumbnail"
            :src="getThumbnailUrl(item.videoData.thumbnail)"
            :alt="item.videoData?.title"
            class="w-full h-full object-cover"
            @error="(e) => e.target.style.display = 'none'"
          />
          <div v-else class="w-full h-full flex items-center justify-center text-text-muted">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"
                d="M15 10l4.553-2.276A1 1 0 0121 8.618v6.764a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z" />
            </svg>
          </div>
        </div>

        <!-- 信息 -->
        <div class="flex-1 min-w-0">
          <h3 class="text-sm font-medium text-text-primary truncate">
            {{ item.videoData?.title || item.url }}
          </h3>
          <div class="flex items-center gap-2 mt-1 text-xs text-text-muted">
            <span v-if="item.videoData?.platform" class="px-2 py-0.5 rounded-full bg-primary/10 text-primary font-medium">
              {{ item.videoData.platform }}
            </span>
            <span :class="statusClass(item.status)" class="inline-flex items-center gap-1">
              <span class="w-1.5 h-1.5 rounded-full" :class="statusDotClass(item.status)"></span>
              {{ statusLabel(item.status) }}
            </span>
            <span v-if="item.error" class="text-error truncate">{{ item.error }}</span>
          </div>
        </div>

        <!-- 下载按钮 -->
        <button
          v-if="item.status === 'ready'"
          @click="downloadItem(index)"
          :disabled="downloading"
          class="flex-shrink-0 inline-flex items-center justify-center w-10 h-10 rounded-lg gradient-bg text-white hover:opacity-90 transition-all disabled:opacity-50 cursor-pointer glow"
        >
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
          </svg>
        </button>
        <div v-else-if="item.status === 'downloading'" class="flex-shrink-0">
          <svg class="animate-spin w-6 h-6 text-primary" fill="none" viewBox="0 0 24 24">
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
          </svg>
        </div>
        <div v-else-if="item.status === 'done'" class="flex-shrink-0">
          <svg class="w-6 h-6 text-primary" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
          </svg>
        </div>
        <div v-else-if="item.status === 'error'" class="flex-shrink-0">
          <svg class="w-6 h-6 text-error" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </div>
      </div>
    </div>

    <!-- 空状态 -->
    <div v-else-if="!rawInput.trim() && !parsing" class="mt-8 text-center text-text-muted py-12">
      <svg class="w-16 h-16 mx-auto mb-3 opacity-40" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"
          d="M9 17v-2m2 2v-4m2 4v-6m2 6V8M4 19h16M4 15l4-4 3 3 4-5 3 4" />
      </svg>
      <p class="text-sm">粘贴多个视频链接开始批量下载</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { parseVideo, downloadViaServer } from '../api/video.js'

const emit = defineEmits(['need-login'])

const rawInput = ref('')
const queue = ref([])
const parsing = ref(false)
const downloading = ref(false)

const doneCount = computed(() => queue.value.filter(i => i.status === 'done').length)
const readyCount = computed(() => queue.value.filter(i => i.status === 'ready').length)

function getThumbnailUrl(url) {
  return '/api/proxy/thumbnail?url=' + encodeURIComponent(url)
}

function statusLabel(status) {
  const labels = {
    pending: '等待中',
    parsing: '解析中',
    ready: '就绪',
    downloading: '下载中',
    done: '已完成',
    error: '失败',
  }
  return labels[status] || status
}

function statusClass(status) {
  const classes = {
    pending: 'text-text-muted',
    parsing: 'text-accent',
    ready: 'text-primary',
    downloading: 'text-warning',
    done: 'text-primary',
    error: 'text-error',
  }
  return classes[status] || 'text-text-muted'
}

function statusDotClass(status) {
  const classes = {
    pending: 'bg-text-muted',
    parsing: 'bg-accent animate-pulse',
    ready: 'bg-primary',
    downloading: 'bg-warning animate-pulse',
    done: 'bg-primary',
    error: 'bg-error',
  }
  return classes[status] || 'bg-text-muted'
}

async function startBatchParse() {
  const urls = rawInput.value
    .split('\n')
    .map(u => u.trim())
    .filter(u => u.length > 0)

  if (urls.length === 0) return

  // 初始化队列
  queue.value = urls.map(url => ({
    url,
    videoData: null,
    status: 'pending',
    error: null,
  }))

  parsing.value = true

  // 逐个解析
  for (let i = 0; i < queue.value.length; i++) {
    queue.value[i].status = 'parsing'
    try {
      const data = await parseVideo(queue.value[i].url)
      queue.value[i].videoData = data
      queue.value[i].status = 'ready'
    } catch (err) {
      queue.value[i].status = 'error'
      queue.value[i].error = err.response?.data?.detail || err.message || '解析失败'
    }
  }

  parsing.value = false
}

async function downloadItem(index) {
  const item = queue.value[index]
  if (!item.videoData || item.status !== 'ready') return

  downloading.value = true
  item.status = 'downloading'

  try {
    // 选第一个格式作为默认下载
    const formatId = item.videoData.formats?.[0]?.format_id
    const res = await downloadViaServer(item.url, formatId)

    // 触发文件下载
    const blob = new Blob([res.data])
    const link = document.createElement('a')
    link.href = URL.createObjectURL(blob)
    const ext = item.videoData.formats?.[0]?.ext || 'mp4'
    const title = (item.videoData.title || 'video').replace(/[<>:"/\\|?*]/g, '_')
    link.download = `${title}.${ext}`
    link.click()
    URL.revokeObjectURL(link.href)

    item.status = 'done'
  } catch (err) {
    item.status = 'error'
    item.error = err.response?.data?.detail || err.message || '下载失败'
  }

  downloading.value = false
}

async function downloadAll() {
  for (let i = 0; i < queue.value.length; i++) {
    if (queue.value[i].status === 'ready') {
      await downloadItem(i)
    }
  }
}

function clearQueue() {
  queue.value = []
  rawInput.value = ''
}
</script>
