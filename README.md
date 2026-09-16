# VidGrab - 智能视频下载与摘要助手

基于 yt-dlp 的万能视频下载工具，支持 1800+ 平台，内置 AI 视频总结、批量下载、音频提取等功能。

## 功能特性

- **1800+ 平台支持** — YouTube、Bilibili、抖音、TikTok、Twitter/X、Instagram 等
- **AI 视频总结** — 自动生成摘要、思维导图、字幕文本，支持 AI 问答
- **批量下载** — 粘贴多个视频链接，队列解析+批量下载
- **音频提取** — 从任意视频提取 MP3 音频
- **多种清晰度** — 360p 到 4K 多种格式可选
- **抖音无水印** — 内置抖音专用解析模块
- **手机可用** — 响应式设计，完美适配移动端

## 技术栈

- **前端**：Vue 3 + Vite 7 + Tailwind CSS v4 + Axios + marked + markmap
- **后端**：Python + FastAPI + yt-dlp + httpx
- **数据库**：SQLite
- **AI**：DeepSeek API

## 快速开始

### 后端

```bash
cd backend
pip install -r requirements.txt
cp .env.example .env  # 编辑 .env 填入 DeepSeek API Key
python -m uvicorn main:app --host 0.0.0.0 --port 8000
```

### 前端

```bash
cd frontend
npm install --legacy-peer-deps
npm run dev
```

访问 http://localhost:5173 即可使用。

### 生产构建

```bash
cd frontend
npm run build
# 构建产物在 dist/ 目录
```

## 环境变量

| 变量 | 说明 | 必需 |
|------|------|------|
| `DEEPSEEK_API_KEY` | DeepSeek API Key（AI摘要功能） | 是 |
| `JWT_SECRET` | JWT 签名密钥 | 否（有默认值） |
| `STRIPE_SECRET_KEY` | Stripe 支付密钥 | 否 |
| `FRONTEND_URL` | 前端地址 | 否 |

## API 接口

| 端点 | 方法 | 说明 |
|------|------|------|
| `/api/health` | GET | 健康检查 |
| `/api/parse` | POST | 解析视频信息 |
| `/api/download` | POST | 下载视频 |
| `/api/direct-url` | POST | 获取视频直链 |
| `/api/summarize` | POST | AI 视频总结（SSE 流式） |
| `/api/chat` | POST | AI 视频问答（SSE 流式） |
| `/api/auth/register` | POST | 用户注册 |
| `/api/auth/login` | POST | 用户登录 |
| `/api/auth/me` | GET | 获取用户信息 |
| `/api/payment/create-checkout` | POST | 创建支付订单 |

## 许可证

MIT License

## 免责声明

本工具仅供学习交流使用，请尊重视频版权，下载内容的版权归原作者所有。
