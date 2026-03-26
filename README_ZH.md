# Qwen2API

中文 | [English](README.md)

将通义千问（Qwen Chat）转换为 OpenAI 兼容的 API 代理服务。

## ✨ 功能特性

- 🔄 **OpenAI API 兼容** - 完全兼容 OpenAI API 格式
- 🚀 **流式响应** - 支持 SSE 实时流式输出
- 🔐 **Token 认证** - 可选的 API 密钥保护
- 🌐 **多平台部署** - Docker、Vercel、Netlify、Cloudflare Workers
- 🖼️ **图片生成** - 文生图功能
- 🎬 **多模态** - 视频分析、图片理解、文档解析
- 💬 **Web 界面** - 内置聊天界面
- 🔧 **工具调用** - 支持 MCP (Model Context Protocol)

## 📋 支持的模型

| 模型 ID | 名称 | 能力 | 上下文长度 |
|---------|------|------|-----------|
| `qwen3.5-plus` | Qwen3.5-Plus | 文本、视觉、文档、视频、音频、思考、搜索 | 100万 tokens |
| `qwen3.5-flash` | Qwen3.5-Flash | 文本、视觉、文档、视频、音频、思考、搜索 | 100万 tokens |
| `qwen3.5-397b-a17b` | Qwen3.5-397B-A17B | 文本、视觉、文档、视频、音频、思考、搜索 | 26.2万 tokens |

**所有模型均支持：**
- 📝 文本生成
- 👁️ 图片理解（视觉）
- 📄 文档解析（PDF、DOCX 等）
- 🎬 视频分析
- 🎵 音频处理
- 🧠 思考模式（链式思考）
- 🔍 网络搜索
- 🛠️ 工具调用（MCP）

**默认模型：** `qwen3.5-plus`

## 🚀 快速开始

### Docker（推荐）

```bash
# 从 GitHub Container Registry 拉取镜像
docker pull ghcr.io/YOUR_USERNAME/qwen2api:latest

# 使用 docker-compose 运行
docker-compose up -d

# 或直接运行
docker run -d \
  -p 8765:7860 \
  -e API_TOKENS=your_secret_token \
  ghcr.io/YOUR_USERNAME/qwen2api:latest
```

### Docker Compose

```yaml
version: '3.8'
services:
  qwen2api:
    image: ghcr.io/YOUR_USERNAME/qwen2api:latest
    ports:
      - "8765:7860"
    environment:
      - API_TOKENS=your_secret_token
      - CHAT_DETAIL_LOG=false
    restart: unless-stopped
```

### 从源码构建

```bash
# 克隆仓库
git clone https://github.com/YOUR_USERNAME/qwen2api.git
cd qwen2api

# 构建 Docker 镜像
docker build -t qwen2api .

# 运行容器
docker run -d -p 8765:7860 -e API_TOKENS=your_token qwen2api
```

## 🌐 其他部署方式

### Vercel

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/smanx/qwen2api)

1. Fork 本仓库
2. 导入到 Vercel
3. 设置环境变量 `API_TOKENS`（可选）

### Netlify

[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/smanx/qwen2api)

1. Fork 本仓库
2. 导入到 Netlify
3. 设置环境变量 `API_TOKENS`（可选）

### Cloudflare Workers

```bash
npm install -g wrangler
wrangler login
wrangler deploy
```

在 Cloudflare Dashboard 中设置 `API_TOKENS`。

## 📡 API 端点

| 端点 | 方法 | 说明 |
|------|------|------|
| `/v1/models` | GET | 获取模型列表 |
| `/v1/chat/completions` | POST | 聊天完成（支持流式和非流式） |
| `/v1/images/generations` | POST | 生成图片 |
| `/chat` | GET | Web 聊天界面 |
| `/` | GET | 健康检查 |

## 🔑 环境变量

| 变量 | 说明 | 默认值 | 必填 |
|------|------|--------|------|
| `API_TOKENS` | API 密钥（多个用逗号分隔） | - | 否 |
| `PORT` | 服务端口 | 7860 | 否 |
| `NODE_ENV` | Node 环境 | production | 否 |
| `CHAT_DETAIL_LOG` | 启用详细日志（`true`/`false`） | false | 否 |
| `JSON_BODY_LIMIT` | 请求体大小限制 | 10mb | 否 |
| `MIN_VIDEO_RESOLUTION` | 最低视频分辨率 | 720 | 否 |

⚠️ **安全警告：** 如果不设置 `API_TOKENS`，服务将公开访问。生产环境务必设置 Token。

## 📖 使用示例

### cURL

#### 获取模型列表
```bash
curl http://localhost:8765/v1/models \
  -H "Authorization: Bearer your_token"
```

#### 聊天完成（非流式）
```bash
curl http://localhost:8765/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_token" \
  -d '{
    "model": "qwen3.5-plus",
    "messages": [
      {"role": "user", "content": "你好！介绍一下自己。"}
    ]
  }'
```

#### 聊天完成（流式）
```bash
curl http://localhost:8765/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_token" \
  -d '{
    "model": "qwen3.5-plus",
    "messages": [
      {"role": "user", "content": "写一首关于AI的诗"}
    ],
    "stream": true
  }'
```

#### 图片生成
```bash
curl http://localhost:8765/v1/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_token" \
  -d '{
    "prompt": "一只可爱的猫咪在玩毛线球",
    "model": "qwen3.5-plus",
    "n": 1,
    "size": "1024x1024"
  }'
```

#### 多模态（图片理解）
```bash
curl http://localhost:8765/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_token" \
  -d '{
    "model": "qwen3.5-plus",
    "messages": [{
      "role": "user",
      "content": [
        {"type": "text", "text": "这张图片里有什么？"},
        {"type": "image_url", "image_url": {"url": "https://example.com/image.jpg"}}
      ]
    }]
  }'
```

### Python（OpenAI SDK）

```python
from openai import OpenAI

client = OpenAI(
    api_key="your_token",
    base_url="http://localhost:8765/v1"
)

# 聊天完成
response = client.chat.completions.create(
    model="qwen3.5-plus",
    messages=[
        {"role": "user", "content": "你好！"}
    ]
)
print(response.choices[0].message.content)

# 流式响应
stream = client.chat.completions.create(
    model="qwen3.5-plus",
    messages=[{"role": "user", "content": "讲个故事"}],
    stream=True
)
for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="")

# 图片生成
image = client.images.generate(
    model="qwen3.5-plus",
    prompt="美丽的日落",
    n=1,
    size="1024x1024"
)
print(image.data[0].url)
```

### Node.js（OpenAI SDK）

```javascript
import OpenAI from 'openai';

const client = new OpenAI({
  apiKey: 'your_token',
  baseURL: 'http://localhost:8765/v1'
});

// 聊天完成
const response = await client.chat.completions.create({
  model: 'qwen3.5-plus',
  messages: [{ role: 'user', content: '你好！' }]
});
console.log(response.choices[0].message.content);

// 流式响应
const stream = await client.chat.completions.create({
  model: 'qwen3.5-plus',
  messages: [{ role: 'user', content: '讲个故事' }],
  stream: true
});

for await (const chunk of stream) {
  process.stdout.write(chunk.choices[0]?.delta?.content || '');
}

// 图片生成
const image = await client.images.generate({
  model: 'qwen3.5-plus',
  prompt: '美丽的日落',
  n: 1,
  size: '1024x1024'
});
console.log(image.data[0].url);
```

## 🌐 Web 聊天界面

访问内置聊天界面：`http://localhost:8765/chat`

功能：
- 💬 实时流式响应
- 📎 文件附件（图片、文档、音频）
- 🎬 视频 URL 分析
- 📊 请求/响应日志面板
- 🌍 多语言支持（中/英）

## ⚠️ 限制说明

### 视频分析和大文件

**无服务器平台不支持**（Vercel、Netlify Functions、Cloudflare Workers）：
- 视频 URL 分析需要 `yt-dlp`（仅 Docker/本地部署可用）
- 大文件上传受无服务器平台限制
- 完整功能请使用 Docker 部署

### 各平台功能支持

| 功能 | Docker | Vercel | Netlify | CF Workers |
|------|--------|--------|---------|------------|
| 文本对话 | ✅ | ✅ | ✅ | ✅ |
| 图片理解 | ✅ | ✅ | ✅ | ✅ |
| 文档解析 | ✅ | ✅ | ✅ | ✅ |
| 图片生成 | ✅ | ✅ | ✅ | ✅ |
| 视频分析 | ✅ | ❌ | ❌ | ❌ |
| 大文件 (>10MB) | ✅ | ❌ | ❌ | ❌ |

## 🔧 高级配置

### 多模态消息格式

API 支持 OpenAI 兼容的多模态消息：

```json
{
  "messages": [{
    "role": "user",
    "content": [
      {"type": "text", "text": "描述这张图片"},
      {"type": "image_url", "image_url": {"url": "https://..."}}
    ]
  }]
}
```

支持的内容类型：
- `text` - 纯文本
- `image_url` - 图片 URL 或 base64
- `file` - 带 base64 数据的文档
- `audio` - 音频文件 URL 或 base64

### 自定义请求头

服务会转发以下请求头到 Qwen API：
- `bx-ua` - 浏览器用户代理指纹
- `bx-umidtoken` - 唯一设备令牌
- `bx-v` - Baxia 版本

## 🤝 贡献

欢迎贡献！请随时提交 Pull Request。

## 📄 许可证

ISC License

## 🔗 相关链接

- [通义千问官网](https://qwen.ai/)
- [OpenAI API 文档](https://platform.openai.com/docs/api-reference)
- [GitHub 仓库](https://github.com/smanx/qwen2api)

## ⭐ Star History

如果觉得这个项目有用，请给个 Star！
