# VidGrab - 智能视频下载与摘要助手

基于 yt-dlp 的万能视频下载工具，支持 1800+ 平台，内置 AI 视频总结、批量下载、音频提取等功能。

## 功能特性

- **1800+ 平台支持** — YouTube、Bilibili、抖音、TikTok、Twitter/X、Instagram 等
- **AI 视频总结** — 自动生成摘要、思维导图、字幕文本，支持 AI 问答
- **批量下载** — 粘贴多个视频链接，队列解析+批量下载
- **音频提取** — 从任意视频提取 MP3 音频
- **多种清晰度** — 360p 到 4K 多种格式可选
- **抖音无水印** — 内置抖音专用解析模块
- **用户系统** — 注册登录、VIP 会员、Stripe 支付
- **响应式设计** — 完美适配移动端

## 技术栈

- **前端**：Vue 3 + Vite 7 + Tailwind CSS v4 + Axios + marked + markmap
- **后端**：Python + FastAPI + yt-dlp + httpx + SQLite
- **AI**：DeepSeek API

## 前置要求

- Python 3.9+
- Node.js 18+
- FFmpeg（视频合并/音频提取必需，[下载地址](https://ffmpeg.org/download.html)）

## 快速开始

### 1. 克隆项目

```bash
git clone https://github.com/cfffbb/VidGrab.git
cd VidGrab
```

### 2. 启动后端

```bash
cd backend

# 安装依赖
pip install -r requirements.txt

# 配置环境变量
cp .env.example .env
# 编辑 .env，至少填入 DEEPSEEK_API_KEY（AI 功能需要，不填不影响下载）

# 启动服务
python -m uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

后端启动后访问 http://localhost:8000/docs 查看 API 文档。

> 注意：确保已安装 FFmpeg 并添加到系统 PATH 中，否则视频合并和音频提取会失败。

### 3. 启动前端

```bash
cd frontend

# 安装依赖
npm install --legacy-peer-deps

# 启动开发服务器
npm run dev
```

访问 http://localhost:5173 即可使用。

前端通过 Vite 代理将 `/api` 请求转发到后端 `http://localhost:8000`，如需修改后端端口，编辑 `vite.config.js` 中的 `proxy.target`。

### 生产构建

```bash
cd frontend
npm run build
# 构建产物在 dist/ 目录
```

## 环境变量

| 变量 | 说明 | 必需 |
|------|------|------|
| `DEEPSEEK_API_KEY` | DeepSeek API Key（AI 摘要/问答功能） | 否（不填则 AI 功能不可用） |
| `JWT_SECRET` | JWT 签名密钥 | 否（有默认值） |
| `STRIPE_SECRET_KEY` | Stripe 支付密钥 | 否 |
| `STRIPE_WEBHOOK_SECRET` | Stripe Webhook 签名 | 否 |
| `STRIPE_PRICE_ID_MONTHLY` | Stripe 月度会员价格 ID | 否 |
| `FRONTEND_URL` | 前端地址（支付回跳用） | 否 |

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

## 常见问题

**Q: 启动后端报错 `ModuleNotFoundError: No module named 'xxx'`**
A: 确保在 backend 目录下执行了 `pip install -r requirements.txt`

**Q: 下载视频报错找不到 ffmpeg**
A: 安装 FFmpeg 并添加到系统 PATH 环境变量

**Q: 前端页面空白**
A: 检查后端是否已启动，默认后端端口 8000，前端通过代理转发请求

**Q: AI 功能没反应**
A: 需要在 `.env` 中配置 `DEEPSEEK_API_KEY`，在 https://platform.deepseek.com 申请

## 许可证

MIT License

## 免责声明

本工具仅供学习交流使用，请尊重视频版权，下载内容的版权归原作者所有。<img width="793" height="397" alt="image" src="https://github.com/user-attachments/assets/7d72d1d6-f098-44ab-bd1e-1e942c8e5b8f" />
<img width="812" height="356" alt="image" src="https://github.com/user-attachments/assets/fc7eb44c-70e0-41e4-b0f4-c4d126b1d118" />
<img width="806" height="390" alt="image" src="https://github.com/user-attachments/assets/7c63ab96-c7e9-46e9-a2b1-137bd70b51fd" />
<img width="770" height="397" alt="image" src="https://github.com/user-attachments/assets/63e521af-ea66-4ac1-aa99-c098253779c4" />


