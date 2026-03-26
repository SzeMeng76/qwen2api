# Qwen2API

[中文文档](README_ZH.md) | English

A proxy service that converts Qwen Chat to an OpenAI-compatible API format.

## ✨ Features

- 🔄 **OpenAI API Compatible** - Drop-in replacement for OpenAI API
- 🚀 **Streaming Support** - Server-Sent Events (SSE) for real-time responses
- 🔐 **Token Authentication** - Optional API key protection
- 🌐 **Multi-Platform** - Docker, Vercel, Netlify, Cloudflare Workers
- 🖼️ **Image Generation** - Text-to-image capabilities
- 🎬 **Multimodal** - Video analysis, image understanding, document parsing
- 💬 **Web UI** - Built-in chat interface
- 🔧 **Tool Calling** - MCP (Model Context Protocol) support

## 📋 Supported Models

| Model ID | Name | Capabilities | Context Length |
|----------|------|--------------|----------------|
| `qwen3.5-plus` | Qwen3.5-Plus | Text, Vision, Document, Video, Audio, Thinking, Search | 1M tokens |
| `qwen3.5-flash` | Qwen3.5-Flash | Text, Vision, Document, Video, Audio, Thinking, Search | 1M tokens |
| `qwen3.5-397b-a17b` | Qwen3.5-397B-A17B | Text, Vision, Document, Video, Audio, Thinking, Search | 262K tokens |

**All models support:**
- 📝 Text generation
- 👁️ Image understanding (vision)
- 📄 Document parsing (PDF, DOCX, etc.)
- 🎬 Video analysis
- 🎵 Audio processing
- 🧠 Thinking mode (chain-of-thought)
- 🔍 Web search
- 🛠️ Tool calling (MCP)

**Default model:** `qwen3.5-plus`

## 🚀 Quick Start

### Docker (Recommended)

```bash
# Pull from GitHub Container Registry
docker pull ghcr.io/YOUR_USERNAME/qwen2api:latest

# Run with docker-compose
docker-compose up -d

# Or run directly
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

### Build from Source

```bash
# Clone repository
git clone https://github.com/YOUR_USERNAME/qwen2api.git
cd qwen2api

# Build Docker image
docker build -t qwen2api .

# Run container
docker run -d -p 8765:7860 -e API_TOKENS=your_token qwen2api
```

## 🌐 Alternative Deployments

### Vercel

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/smanx/qwen2api)

1. Fork this repository
2. Import to Vercel
3. Set `API_TOKENS` environment variable (optional)

### Netlify

[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/smanx/qwen2api)

1. Fork this repository
2. Import to Netlify
3. Set `API_TOKENS` environment variable (optional)

### Cloudflare Workers

```bash
npm install -g wrangler
wrangler login
wrangler deploy
```

Set `API_TOKENS` in Cloudflare Dashboard.

## 📡 API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/v1/models` | GET | List available models |
| `/v1/chat/completions` | POST | Chat completions (streaming & non-streaming) |
| `/v1/images/generations` | POST | Generate images |
| `/chat` | GET | Web chat interface |
| `/` | GET | Health check |

## 🔑 Environment Variables

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| `API_TOKENS` | API keys (comma-separated for multiple) | - | No |
| `PORT` | Server port | 7860 | No |
| `NODE_ENV` | Node environment | production | No |
| `CHAT_DETAIL_LOG` | Enable detailed logging (`true`/`false`) | false | No |
| `JSON_BODY_LIMIT` | Request body size limit | 10mb | No |
| `MIN_VIDEO_RESOLUTION` | Minimum video resolution | 720 | No |

⚠️ **Security Warning:** Without `API_TOKENS`, the service is publicly accessible. Always set tokens for production deployments.

## 📖 Usage Examples

### cURL

#### List Models
```bash
curl http://localhost:8765/v1/models \
  -H "Authorization: Bearer your_token"
```

#### Chat Completion (Non-streaming)
```bash
curl http://localhost:8765/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_token" \
  -d '{
    "model": "qwen3.5-plus",
    "messages": [
      {"role": "user", "content": "Hello! Introduce yourself."}
    ]
  }'
```

#### Chat Completion (Streaming)
```bash
curl http://localhost:8765/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_token" \
  -d '{
    "model": "qwen3.5-plus",
    "messages": [
      {"role": "user", "content": "Write a poem about AI"}
    ],
    "stream": true
  }'
```

#### Image Generation
```bash
curl http://localhost:8765/v1/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_token" \
  -d '{
    "prompt": "A cute cat playing with yarn",
    "model": "qwen3.5-plus",
    "n": 1,
    "size": "1024x1024"
  }'
```

#### Multimodal (Image Understanding)
```bash
curl http://localhost:8765/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_token" \
  -d '{
    "model": "qwen3.5-plus",
    "messages": [{
      "role": "user",
      "content": [
        {"type": "text", "text": "What is in this image?"},
        {"type": "image_url", "image_url": {"url": "https://example.com/image.jpg"}}
      ]
    }]
  }'
```

### Python (OpenAI SDK)

```python
from openai import OpenAI

client = OpenAI(
    api_key="your_token",
    base_url="http://localhost:8765/v1"
)

# Chat completion
response = client.chat.completions.create(
    model="qwen3.5-plus",
    messages=[
        {"role": "user", "content": "Hello!"}
    ]
)
print(response.choices[0].message.content)

# Streaming
stream = client.chat.completions.create(
    model="qwen3.5-plus",
    messages=[{"role": "user", "content": "Tell me a story"}],
    stream=True
)
for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="")

# Image generation
image = client.images.generate(
    model="qwen3.5-plus",
    prompt="A beautiful sunset",
    n=1,
    size="1024x1024"
)
print(image.data[0].url)
```

### Node.js (OpenAI SDK)

```javascript
import OpenAI from 'openai';

const client = new OpenAI({
  apiKey: 'your_token',
  baseURL: 'http://localhost:8765/v1'
});

// Chat completion
const response = await client.chat.completions.create({
  model: 'qwen3.5-plus',
  messages: [{ role: 'user', content: 'Hello!' }]
});
console.log(response.choices[0].message.content);

// Streaming
const stream = await client.chat.completions.create({
  model: 'qwen3.5-plus',
  messages: [{ role: 'user', content: 'Tell me a story' }],
  stream: true
});

for await (const chunk of stream) {
  process.stdout.write(chunk.choices[0]?.delta?.content || '');
}

// Image generation
const image = await client.images.generate({
  model: 'qwen3.5-plus',
  prompt: 'A beautiful sunset',
  n: 1,
  size: '1024x1024'
});
console.log(image.data[0].url);
```

## 🌐 Web Chat Interface

Access the built-in chat UI at: `http://localhost:8765/chat`

Features:
- 💬 Real-time streaming responses
- 📎 File attachments (images, documents, audio)
- 🎬 Video URL analysis
- 📊 Request/response logging panel
- 🌍 Multi-language support (EN/CN)

## ⚠️ Limitations

### Video Analysis & Large Files

**Not supported on serverless platforms** (Vercel, Netlify Functions, Cloudflare Workers):
- Video URL analysis requires `yt-dlp` (only available in Docker/local deployments)
- Large file uploads are limited by serverless constraints
- Use Docker deployment for full functionality

### Supported Platforms by Feature

| Feature | Docker | Vercel | Netlify | CF Workers |
|---------|--------|--------|---------|------------|
| Text Chat | ✅ | ✅ | ✅ | ✅ |
| Image Understanding | ✅ | ✅ | ✅ | ✅ |
| Document Parsing | ✅ | ✅ | ✅ | ✅ |
| Image Generation | ✅ | ✅ | ✅ | ✅ |
| Video Analysis | ✅ | ❌ | ❌ | ❌ |
| Large Files (>10MB) | ✅ | ❌ | ❌ | ❌ |

## 🔧 Advanced Configuration

### Multimodal Message Format

The API supports OpenAI-compatible multimodal messages:

```json
{
  "messages": [{
    "role": "user",
    "content": [
      {"type": "text", "text": "Describe this image"},
      {"type": "image_url", "image_url": {"url": "https://..."}}
    ]
  }]
}
```

Supported content types:
- `text` - Plain text
- `image_url` - Image URL or base64
- `file` - Document with base64 data
- `audio` - Audio file URL or base64

### Custom Headers

The service forwards these headers to Qwen API:
- `bx-ua` - Browser user agent fingerprint
- `bx-umidtoken` - Unique device token
- `bx-v` - Baxia version

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

ISC License

## 🔗 Links

- [Qwen Official](https://qwen.ai/)
- [OpenAI API Reference](https://platform.openai.com/docs/api-reference)
- [GitHub Repository](https://github.com/smanx/qwen2api)

## ⭐ Star History

If you find this project useful, please consider giving it a star!
