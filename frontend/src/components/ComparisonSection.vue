<template>
  <section id="comparison" class="py-16 sm:py-20 bg-bg-main" aria-labelledby="comparison-heading">
    <div class="max-w-5xl mx-auto px-4 sm:px-6">
      <div class="text-center mb-12">
        <h2 id="comparison-heading" class="text-2xl sm:text-3xl font-bold text-text-primary mb-3">
          <span class="gradient-text">VidGrab</span> 与其他视频下载工具对比
        </h2>
        <p class="text-text-secondary text-base max-w-xl mx-auto">
          一张表看清 VidGrab 的核心优势，更新于 2026 年 9 月
        </p>
      </div>

      <div class="overflow-x-auto rounded-2xl border border-border shadow-lg">
        <table class="w-full text-sm">
          <thead>
            <tr class="bg-bg-card text-text-primary">
              <th class="text-left px-5 py-3.5 font-semibold border-b border-border">功能对比</th>
              <th class="px-5 py-3.5 font-semibold text-white gradient-bg border-b border-border border-r-2 border-r-primary/50">
                VidGrab
              </th>
              <th class="px-5 py-3.5 font-semibold border-b border-border">其他在线工具</th>
              <th class="px-5 py-3.5 font-semibold border-b border-border">桌面下载软件</th>
            </tr>
          </thead>
          <tbody class="bg-bg-card">
            <tr
              v-for="(row, i) in rows"
              :key="row.feature"
              :class="i % 2 === 1 ? 'bg-bg-hover/30' : ''"
            >
              <td class="px-5 py-3 text-text-primary font-medium border-b border-border-light">{{ row.feature }}</td>
              <td class="px-5 py-3 text-center bg-primary/5 border-b border-border-light border-r-2 border-r-primary/50">
                <span v-if="row.vidgrab === true" class="text-success text-base font-bold">✓</span>
                <span v-else-if="row.vidgrab === false" class="text-text-muted">✗</span>
                <span v-else class="text-primary font-medium">{{ row.vidgrab }}</span>
              </td>
              <td class="px-5 py-3 text-center text-text-secondary border-b border-border-light">
                <span v-if="row.online === true" class="text-success text-base">✓</span>
                <span v-else-if="row.online === false" class="text-text-muted">✗</span>
                <span v-else>{{ row.online }}</span>
              </td>
              <td class="px-5 py-3 text-center text-text-secondary border-b border-border-light">
                <span v-if="row.desktop === true" class="text-success text-base">✓</span>
                <span v-else-if="row.desktop === false" class="text-text-muted">✗</span>
                <span v-else>{{ row.desktop }}</span>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </section>
</template>

<script setup>
const rows = [
  { feature: '平台数量', vidgrab: '1800+', online: '10-50', desktop: '100-500' },
  { feature: 'AI 总结', vidgrab: true, online: false, desktop: false },
  { feature: '批量下载', vidgrab: true, online: false, desktop: '部分支持' },
  { feature: '音频提取', vidgrab: true, online: '部分支持', desktop: true },
  { feature: '思维导图', vidgrab: true, online: false, desktop: false },
  { feature: '字幕下载', vidgrab: 'SRT/VTT/TXT', online: '部分支持', desktop: '部分支持' },
  { feature: '无水印', vidgrab: true, online: '部分支持', desktop: '需登录' },
  { feature: '最高画质', vidgrab: '4K', online: '720p-1080p', desktop: '4K' },
  { feature: '无需安装', vidgrab: true, online: true, desktop: false },
  { feature: '手机可用', vidgrab: true, online: '部分支持', desktop: false },
  { feature: '费用', vidgrab: '免费', online: '免费/付费', desktop: '付费为主' },
]
</script>
