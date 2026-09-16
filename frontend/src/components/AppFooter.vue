<template>
  <footer class="bg-bg-card border-t border-border py-8">
    <div class="max-w-6xl mx-auto px-4 sm:px-6">
      <!-- SEO 友好描述 -->
      <div class="text-center mb-6">
        <p class="text-sm text-text-secondary leading-relaxed max-w-3xl mx-auto">
          <strong class="gradient-text font-semibold">VidGrab</strong> — 免费在线万能视频下载器，支持
          <a href="#features" class="text-primary hover:underline" title="YouTube视频下载">YouTube</a>、
          <a href="#features" class="text-primary hover:underline" title="Bilibili视频下载">Bilibili</a>、
          <a href="#features" class="text-primary hover:underline" title="抖音视频下载">抖音</a>、
          <a href="#features" class="text-primary hover:underline" title="TikTok视频下载">TikTok</a>
          等 1800+ 平台视频下载。提供
          <a href="#features" class="text-primary hover:underline" title="AI视频总结功能">AI 视频总结</a>、思维导图生成、字幕下载等智能功能。
        </p>
      </div>

      <div class="flex flex-col sm:flex-row items-center justify-between gap-4">
        <!-- 品牌 -->
        <a href="/" class="flex items-center gap-2" title="VidGrab 首页">
          <div class="w-7 h-7 rounded-lg gradient-bg flex items-center justify-center">
            <svg class="w-4 h-4 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24" aria-hidden="true">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M4 16v2a2 2 0 002 2h12a2 2 0 002-2v-2M7 10l5 5 5-5M12 15V3" />
            </svg>
          </div>
          <span class="text-sm font-bold gradient-text">VidGrab</span>
        </a>

        <!-- 版权声明 -->
        <p class="text-xs text-text-muted text-center leading-relaxed">
          本工具仅供学习交流使用，请尊重视频版权，勿用于商业用途。下载内容的版权归原作者所有。
        </p>

        <!-- 版权年份 -->
        <p class="text-xs text-text-muted">
          &copy; {{ new Date().getFullYear() }} VidGrab
        </p>
      </div>
    </div>
  </footer>
</template>
