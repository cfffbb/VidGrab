<template>
  <section id="features" class="py-16 sm:py-20 bg-bg-main" aria-labelledby="features-heading">
    <div class="max-w-6xl mx-auto px-4 sm:px-6">
      <div class="text-center mb-12">
        <h2 id="features-heading" class="text-2xl sm:text-3xl font-bold text-text-primary mb-3">
          为什么选择 <span class="gradient-text">VidGrab</span> 视频下载器
        </h2>
        <p class="text-text-secondary text-base max-w-xl mx-auto">
          简单、快速、强大的在线视频下载体验，支持 AI 智能总结
        </p>
      </div>

      <!-- Bento 网格布局 -->
      <div class="grid grid-cols-2 lg:grid-cols-3 gap-5">
        <article
          v-for="feature in features"
          :key="feature.title"
          class="glass-card rounded-2xl p-6 hover:border-primary transition-all duration-300"
          :class="feature.span"
        >
          <div class="w-12 h-12 rounded-xl flex items-center justify-center mb-4 text-2xl bg-bg-hover"
            role="img" :aria-label="feature.title">
            {{ feature.icon }}
          </div>
          <h3 class="text-base font-semibold text-text-primary mb-2">{{ feature.title }}</h3>
          <p class="text-sm text-text-secondary leading-relaxed">{{ feature.desc }}</p>
        </article>
      </div>
    </div>
  </section>
</template>

<script setup>
const features = [
  {
    icon: '🌐',
    title: '1800+ 平台',
    desc: 'YouTube、Bilibili、抖音、TikTok、Twitter、Instagram 等全球主流平台全覆盖',
    span: '',
  },
  {
    icon: '⚡',
    title: '极速下载',
    desc: '智能解析视频链接，自动匹配最优下载方式，速度快人一步',
    span: '',
  },
  {
    icon: '📱',
    title: '手机可用',
    desc: '完美适配手机浏览器，随时随地，想下就下，无需安装任何 App',
    span: '',
  },
  {
    icon: '🎬',
    title: '多种清晰度',
    desc: '支持从 360p 到 4K 多种清晰度选择，满足不同场景需求',
    span: '',
  },
  {
    icon: '🤖',
    title: 'AI 总结',
    desc: 'AI 智能分析视频内容，一键生成摘要、思维导图，还能针对视频提问',
    span: '',
  },
  {
    icon: '✨',
    title: '批量下载新功能',
    desc: '支持批量粘贴多个链接，一键排队下载，高效管理下载任务',
    span: '',
  },
]
</script>
