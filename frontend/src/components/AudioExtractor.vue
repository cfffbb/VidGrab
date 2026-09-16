<template>
  <div class="max-w-3xl mx-auto px-4 sm:px-6 py-8">
    <!-- 输入区 -->
    <div class="glass-card rounded-2xl p-5 sm:p-6">
      <div class="flex items-center gap-2 mb-4">
        <div class="w-10 h-10 rounded-xl gradient-bg flex items-center justify-center">
          <svg class="w-5 h-5 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M9 19V6l12-3v13M9 19c0 1.105-1.343 2-3 2s-3-.895-3-2 1.343-2 3-2 3 .895 3 2zm12-3c0 1.105-1.343 2-3 2s-3-.895-3-2 1.343-2 3-2 3 .895 3 2zM9 10l12-3" />
          </svg>
        </div>
        <div>
          <h2 class="text-lg font-semibold text-text-primary">音频提取</h2>
          <p class="text-xs text-text-muted">从视频中提取 MP3 / M4A 音频，支持音乐、播客等</p>
        </div>
      </div>

      <form @submit.prevent="onParse" class="flex flex-col sm:flex-row items-stretch gap-3">
        <div class="relative flex-1">
          <svg class="absolute left-4 top-1/2 -translate-y-1/2 w-5 h-5 text-text-muted" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M13.828 10.172a4 4 0 00-5.656 0l-4 4a4 4 0 105.656 5.656l1.102-1.101m-.758-4.899a4 4 0 005.656 0l4-4a4 4 0 00-5.656-5.656l-1.1 1.1" />
          </svg>
          <input
            v-model="url"
            type="url"
            placeholder="粘贴视频链接，提取音频"
            class="w-full h-12 pl-12 pr-4 rounded-xl bg-bg-card border border-border text-sm text-text-primary placeholder:text-text-muted focus:outline-none focus:ring-2 focus:ring-primary/30 focus:border-primary transition-all"
            :disabled="parsing"
          />
        </div>
        <button
          type="submit"
          :disabled="parsing || !url.trim()"
          class="inline-flex items-center justify-center gap-2 h-12 px-8 rounded-xl gradient-bg text-white font-medium transition-all disabled:opacity-50 disabled:cursor-not-allowed glow hover:opacity-90 cursor-pointer whitespace-nowrap"
        >
          <svg v-if="parsing" class="animate-spin w-5 h-5" fill="none" viewBox="0 0 24 24">
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
          </svg>
          <svg v-else class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M9 19V6l12-3v13M9 19c0 1.105-1.343 2-3 2s-3-.895-3-2 1.343-2 3-2 3 .895 3 2zm12-3c0 1.105-1.343 2-3 2s-3-.895-3-2 1.343-2 3-2 3 .895 3 2zM9 10l12-3" />
          </svg>
          {{ parsing ? '解析中...' : '提取音频' }}
        </button>
      </form>
    </div>

    <!-- 解析结果 -->
    <div v-if="videoData" class="mt-6 space-y-4">
      <!-- 视频信息卡片 -->
      <div class="glass-card rounded-2xl p-5">
        <div class="flex gap-4">
          <!-- 缩略图 -->
          <div class="flex-shrink-0 w-32 h-20 sm:w-40 sm:h-24 rounded-xl overflow-hidden bg-bg-card border border-border">
            <img
              v-if="videoData.thumbnail"
              :src="thumbnailUrl"
              :alt="videoData.title"
              class="w-full h-full object-cover"
              @error="(e) => e.target.style.display = 'none'"
            />
          </div>
          <!-- 信息 -->
          <div class="flex-1 min-w-0">
            <h3 class="text-sm font-semibold text-text-primary line-clamp-2 leading-snug">
              {{ videoData.title }}
            </h3>
            <div class="flex flex-wrap items-center gap-2 mt-2 text-xs text-text-secondary">
              <span v-if="videoData.platform" class="px-2 py-0.5 rounded-full bg-primary/10 text-primary font-medium">
                {{ videoData.platform }}
              </span>
              <span v-if="videoData.uploader" class="inline-flex items-center gap-1">
                <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z" />
                </svg>
                {{ videoData.uploader }}
              </span>
              <span v-if="videoData.duration_string" class="inline-flex items-center gap-1">
                <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
                {{ videoData.duration_string }}
              </span>
            </div>
          </div>
        </div>
      </div>

      <!-- 音频格式列表 -->
      <div v-if="audioFormats.length > 0" class="glass-card rounded-2xl p-5">
        <h4 class="text-sm font-medium text-text-primary mb-3 flex items-center gap-2">
          <svg class="w-4 h-4 text-primary" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M9 19V6l12-3v13M9 19c0 1.105-1.343 2-3 2s-3-.895-3-2 1.343-2 3-2 3 .895 3 2zm12-3c0 1.105-1.343 2-3 2s-3-.895-3-2 1.343-2 3-2 3 .895 3 2z" />
          </svg>
          音频格式
        </h4>

        <div class="grid grid-cols-1 gap-2">
          <button
            v-for="fmt in audioFormats"
            :key="fmt.format_id"
            @click="selectedFormatId = fmt.format_id"
            :class="[
              'flex items-center gap-3 px-4 py-3 rounded-xl border text-left transition-all cursor-pointer',
              selectedFormatId === fmt.format_id
                ? 'border-primary bg-primary/10 ring-1 ring-primary/20'
                : 'border-border bg-bg-card hover:border-primary/40'
            ]"
          >
            <div
              class="flex-shrink-0 w-8 h-8 rounded-lg flex items-center justify-center"
              :class="selectedFormatId === fmt.format_id ? 'gradient-bg text-white' : 'bg-bg-hover text-text-muted'"
            >
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M9 19V6l12-3v13M9 19c0 1.105-1.343 2-3 2s-3-.895-3-2 1.343-2 3-2 3 .895 3 2z" />
              </svg>
            </div>
            <div class="min-w-0 flex-1">
              <div class="text-sm font-medium text-text-primary truncate">{{ fmt.label || fmt.ext.toUpperCase() }}</div>
              <div class="text-xs text-text-muted">{{ fmt.ext.toUpperCase() }} · 音频</div>
            </div>
          </button>
        </div>

        <p v-if="audioFormats.length === 0 && videoData" class="text-sm text-text-muted text-center py-4">
          该视频没有可提取的音频格式
        </p>

        <!-- 操作按钮 -->
        <div class="mt-5 flex flex-col sm:flex-row items-stretch gap-3">
          <button
            @click="downloadMp3"
            :disabled="downloading || !selectedFormatId"
            class="flex-1 inline-flex items-center justify-center gap-2 h-12 px-8 rounded-xl gradient-bg text-white font-medium transition-all disabled:opacity-50 disabled:cursor-not-allowed glow hover:opacity-90 cursor-pointer"
          >
            <svg v-if="downloading" class="animate-spin w-5 h-5" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            <svg v-else class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
            </svg>
            {{ downloading ? '下载中...' : `下载 ${selectedFormat?.ext?.toUpperCase() || 'MP3'}` }}
          </button>

          <button
            @click="copyDirectUrl"
            :disabled="!selectedFormatId || copying"
            class="inline-flex items-center justify-center gap-2 h-12 px-6 rounded-xl border border-border bg-bg-card text-text-primary font-medium hover:border-primary hover:text-primary transition-all disabled:opacity-50 disabled:cursor-not-allowed cursor-pointer"
          >
            <svg v-if="copying" class="animate-spin w-5 h-5" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            <svg v-else class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 5H6a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2v-1M8 5a2 2 0 002 2h2a2 2 0 002-2M8 5a2 2 0 012-2h2a2 2 0 012 2m0 0h2a2 2 0 012 2v3m2 4l3-3m0 0l-3-3m3 3H9" />
            </svg>
            {{ copied ? '已复制!' : (copying ? '获取中...' : '复制直链') }}
          </button>
        </div>
      </div>
    </div>

    <!-- 错误提示 -->
    <div v-if="error" class="mt-4 p-4 rounded-xl bg-error/10 border border-error/30 text-sm text-error flex items-center gap-2">
      <svg class="w-5 h-5 flex-shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
      </svg>
      {{ error }}
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { parseVideo, downloadViaServer, getDirectUrl } from '../api/video.js'

const emit = defineEmits(['need-login'])

const url = ref('')
const videoData = ref(null)
const parsing = ref(false)
const downloading = ref(false)
const copying = ref(false)
const copied = ref(false)
const error = ref('')
const selectedFormatId = ref('')

const thumbnailUrl = computed(() => {
  if (!videoData.value?.thumbnail) return ''
  return '/api/proxy/thumbnail?url=' + encodeURIComponent(videoData.value.thumbnail)
})

const audioFormats = computed(() => {
  if (!videoData.value?.formats) return []
  return videoData.value.formats.filter(f => {
    return f.has_audio === true || ['mp3', 'm4a', 'aac', 'opus', 'wav', 'flac'].includes((f.ext || '').toLowerCase())
  })
})

const selectedFormat = computed(() => {
  return audioFormats.value.find(f => f.format_id === selectedFormatId.value) || null
})

async function onParse() {
  const trimmed = url.value.trim()
  if (!trimmed) return

  parsing.value = true
  error.value = ''
  videoData.value = null
  selectedFormatId.value = ''

  try {
    const data = await parseVideo(trimmed)
    videoData.value = data

    if (audioFormats.value.length > 0) {
      // 优先选 mp3，其次 m4a，再选第一个
      const mp3 = audioFormats.value.find(f => f.ext?.toLowerCase() === 'mp3')
      const m4a = audioFormats.value.find(f => f.ext?.toLowerCase() === 'm4a')
      selectedFormatId.value = (mp3 || m4a || audioFormats.value[0]).format_id
    }
  } catch (err) {
    error.value = err.response?.data?.detail || err.message || '解析失败'
  }

  parsing.value = false
}

async function downloadMp3() {
  if (!selectedFormat.value || !videoData.value) return

  downloading.value = true
  error.value = ''

  try {
    const res = await downloadViaServer(url.value, selectedFormat.value.format_id)

    const blob = new Blob([res.data])
    const link = document.createElement('a')
    link.href = URL.createObjectURL(blob)
    const ext = selectedFormat.value.ext || 'mp3'
    const title = (videoData.value.title || 'audio').replace(/[<>:"/\\|?*]/g, '_')
    link.download = `${title}.${ext}`
    link.click()
    URL.revokeObjectURL(link.href)
  } catch (err) {
    error.value = err.response?.data?.detail || err.message || '下载失败'
  }

  downloading.value = false
}

async function copyDirectUrl() {
  if (!selectedFormat.value) return

  copying.value = true
  copied.value = false
  error.value = ''

  try {
    const data = await getDirectUrl(url.value, selectedFormat.value.format_id)
    const directUrl = data.url || data.direct_url || data.dl_url || ''
    if (directUrl) {
      await navigator.clipboard.writeText(directUrl)
      copied.value = true
      setTimeout(() => { copied.value = false }, 2000)
    } else {
      error.value = '未能获取直链地址'
    }
  } catch (err) {
    error.value = err.response?.data?.detail || err.message || '获取直链失败'
  }

  copying.value = false
}
</script>
