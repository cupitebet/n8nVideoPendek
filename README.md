# 🚀 Tuyul Digital Web3 - Automated Crypto Content Engine

**Workflow n8n otomatis untuk generate konten viral tentang Crypto, Blockchain, dan Web3 Presale**

> Sistem otomasi yang mengubah berita crypto menjadi konten viral (artikel, video script, social media) dengan AI - fully automated!

---

## 📋 Daftar Isi

- [Overview](#overview)
- [Features](#features)
- [Quick Start](#quick-start)
- [Arsitektur](#arsitektur)
- [Installation](#installation)
- [Usage](#usage)
- [Konfigurasi](#konfigurasi)
- [Output Examples](#output-examples)
- [Cost Estimation](#cost-estimation)
- [Troubleshooting](#troubleshooting)
- [Roadmap](#roadmap)
- [Contributing](#contributing)

---

## 🎯 Overview

**Tuyul Digital Web3** adalah workflow n8n yang sepenuhnya otomatis untuk:

1. 📰 **Fetch** berita terbaru tentang crypto presale, blockchain, Web3
2. 🤖 **Research** otomatis dengan AI (GPT-4o-mini)
3. ✍️ **Generate** konten multi-format:
   - Artikel blog lengkap (800-1000 kata)
   - Script video pendek 60 detik (TikTok/Reels/Shorts)
   - Caption media sosial + hashtag strategy
4. 🎨 **Create** gambar thumbnail dengan AI (Flux AI)
5. 📤 **Publish** otomatis ke WordPress, Telegram, dll
6. 💾 **Archive** ke Google Sheets untuk tracking

**Goal:** Konten edukasi crypto berkualitas, konsisten, dan viral - tanpa manual work!

---

## ✨ Features

### Core Features
- ✅ Scheduled automation (cron trigger)
- ✅ Multi-source news aggregation
- ✅ AI-powered research & analysis
- ✅ Multi-format content generation
- ✅ AI image generation
- ✅ Auto-publish to multiple platforms
- ✅ Data archiving & tracking

### Content Output
- 📝 **SEO-optimized blog article** (Bahasa Indonesia)
- 🎬 **60-second video script** with scene breakdown
- 📱 **Social media caption** with hashtags
- 🖼️ **AI-generated thumbnail** image

### Integrations

**Content Generation:**
- 🔗 NewsAPI (berita crypto)
- 🧠 OpenAI GPT-4o-mini (research & content)
- 🎨 HuggingFace Flux AI (gambar)

**Publishing Platforms:**
- 📺 **YouTube Shorts** - Auto-upload dengan API
- 🎵 **TikTok** - Content Posting API
- 📸 **Instagram Reels** - Meta Graph API
- 👥 **Facebook Reels** - Meta Graph API
- 📄 WordPress (artikel blog)
- 💬 Telegram (channel & notifikasi)

**Storage & Analytics:**
- 📊 Google Sheets (archiving)
- ☁️ AWS S3 / Cloudinary (video hosting)

---

## ⚡ Quick Start

**10 menit dari nol ke workflow jalan!**

```bash
# 1. Clone repo
git clone https://github.com/cupitebet/n8nVideoPendek.git
cd n8nVideoPendek

# 2. Install n8n (via Docker)
docker run -d --name n8n -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n

# 3. Buka n8n
# Akses: http://localhost:5678

# 4. Import workflow
# Upload file: workflows/tuyul-digital-web3.json

# 5. Setup credentials (minimal):
# - OpenAI API Key
# - NewsAPI Key

# 6. Test run!
# Execute workflow manual, lihat hasilnya
```

**Dokumentasi lengkap:** [docs/QUICKSTART.md](docs/QUICKSTART.md)

---

## 📱 Social Media Integration (NEW!)

Upload video otomatis ke semua platform short-form video!

### Supported Platforms

| Platform | Status | Setup Time | Auto-Upload |
|----------|--------|------------|-------------|
| **YouTube Shorts** | ✅ Ready | 30 min | ✅ |
| **TikTok** | ⏳ Needs Approval | 1-2 weeks | ✅ |
| **Instagram Reels** | ✅ Ready | 45 min | ✅ |
| **Facebook Reels** | ✅ Ready | 45 min | ✅ |
| **Telegram** | ✅ Ready | 5 min | ✅ |

### Quick Setup

**Start Here:** [API Quick Start Guide](docs/API-QUICK-START.md) - Setup semua platform dalam 1-2 minggu

**Platform-Specific Guides:**
- 📺 [YouTube Shorts API Setup](docs/API-SETUP-YOUTUBE.md)
- 🎵 [TikTok API Setup](docs/API-SETUP-TIKTOK.md)
- 📸 [Instagram Reels API Setup](docs/API-SETUP-INSTAGRAM.md)
- 👥 [Facebook Reels API Setup](docs/API-SETUP-FACEBOOK.md)

**Complete Guide:** [Social Media Complete Guide](docs/SOCIAL-MEDIA-COMPLETE-GUIDE.md)

**Add to Workflow:** [Workflow Social Media Nodes](docs/WORKFLOW-SOCIAL-MEDIA-NODES.md)

---

## 🎬 Video Generation (NEW!)

Workflow generates **video script** - you need to add **video creation**!

### Current Status
- ✅ AI generates 60-second script with scene breakdown
- ✅ Caption & hashtags ready
- ✅ Thumbnail image created
- ❌ **Need:** Actual video file (MP4)

### Solution Options

| Method | Cost | Setup | Quality | Automation |
|--------|------|-------|---------|------------|
| **Pictory.ai API** | $29-119/mo | 1 hour | ⭐⭐⭐⭐⭐ | ✅ Full |
| **Manual (Canva/CapCut)** | Free | 10-20 min/video | ⭐⭐⭐⭐ | ❌ Manual |
| **Hybrid Approach** | $29+/mo | Mixed | ⭐⭐⭐⭐⭐ | ⚡ Semi |

### Quick Start Guides

**Choose your path:**

1. **🤖 Full Automation** → [Pictory.ai Integration](docs/VIDEO-GENERATION-PICTORY.md)
   - Text-to-video otomatis
   - AI voice-over & stock footage
   - 2-5 minutes processing per video

2. **✋ Manual Creation** → [Manual Workflow Guide](docs/VIDEO-GENERATION-MANUAL.md)
   - Free tools: Canva, CapCut
   - 10-20 minutes per video
   - High quality control

3. **📊 Compare All Options** → [Video Generation Options](docs/VIDEO-GENERATION-OPTIONS.md)

**Integration:** [Add Video to Existing Workflow](docs/WORKFLOW-ADD-VIDEO-GENERATION.md)

---

## 🏗️ Arsitektur

```
┌─────────────────┐
│ Schedule Trigger│  (Daily 8AM WIB)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Fetch News API  │  (NewsAPI - crypto presale)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Filter Keywords │  (presale, web3, AI, L2, etc)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ AI Research     │  (GPT-4o-mini - analyze project)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ AI Content Gen  │  (GPT-4o-mini - article+script+caption)
└────────┬────────┘
         │
         ├──────────────┬──────────────┬──────────────┐
         ▼              ▼              ▼              ▼
    ┌────────┐    ┌─────────┐    ┌──────────┐  ┌──────────┐
    │ Image  │    │WordPress│    │ Telegram │  │ Sheets   │
    │Generate│    │ Publish │    │  Publish │  │ Archive  │
    └────────┘    └─────────┘    └──────────┘  └──────────┘
```

**Detail:** [docs/README.md](docs/README.md)

---

## 📦 Installation

### Prerequisites
- n8n instance (Cloud atau self-hosted)
- API Keys:
  - ✅ OpenAI
  - ✅ NewsAPI
  - 🔧 HuggingFace (opsional - untuk gambar)
  - 🔧 WordPress credentials (opsional)
  - 🔧 Telegram Bot (opsional)
  - 🔧 Google Sheets (opsional)

### Step-by-Step

**1. Setup n8n:**
```bash
# Docker (recommended)
docker run -d --restart unless-stopped --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n

# Atau via npm
npm install n8n -g
n8n start
```

**2. Import Workflow:**
- Download `workflows/tuyul-digital-web3.json`
- Di n8n: Workflows > Import from File
- Upload JSON

**3. Setup Credentials:**

Lihat template di `config/credentials-template.json`

Minimal yang wajib:
- OpenAI API (untuk AI research & content)
- NewsAPI Key (untuk fetch berita)

**4. Configure Settings:**

Edit `config/settings.json` sesuai kebutuhan:
```json
{
  "schedule_cron": "0 8 * * *",
  "max_items_per_run": 3,
  "filter_keywords": ["presale", "web3", "AI", ...]
}
```

**5. Test & Activate:**
- Manual test dulu
- Cek output
- Aktifkan schedule

**Panduan lengkap:** [docs/INSTALLATION.md](docs/INSTALLATION.md)

---

## 🎮 Usage

### Manual Execution
1. Buka workflow di n8n
2. Click "Execute Workflow"
3. Lihat hasil di setiap node

### Scheduled Automation
1. Pastikan "Schedule Trigger" enabled
2. Set cron schedule (default: 8AM daily)
3. Activate workflow
4. Workflow akan jalan otomatis sesuai schedule

### Monitoring
- **Executions tab:** Lihat history run
- **Node output:** Debug hasil tiap step
- **Google Sheets:** Archive untuk tracking

### Customization

**Edit Prompts:**
- Research: `prompts/research-prompt.txt`
- Content: `prompts/content-generation-prompt.txt`

**Edit Keywords:**
- Filter: `config/settings.json` > `filter_keywords`

**Edit Schedule:**
- Di node "Schedule Trigger" > ganti cron expression
- Default: `0 8 * * *` (8AM daily)

---

## ⚙️ Konfigurasi

### Filter Keywords

Edit di `config/settings.json`:

```json
{
  "filter_keywords": [
    "presale",
    "web3",
    "blockchain",
    "AI",
    "L2",
    "DeFi",
    "NFT",
    "gaming",
    "RWA",
    "airdrop"
  ]
}
```

### AI Models

```json
{
  "ai_models": {
    "research_model": "gpt-4o-mini",
    "content_generation_model": "gpt-4o-mini",
    "research_temperature": 0.7,
    "content_temperature": 0.8
  }
}
```

### Schedule

Cron expressions:
- `0 8 * * *` - Daily at 8AM
- `0 8,14,20 * * *` - 3x per day (8AM, 2PM, 8PM)
- `0 */6 * * *` - Every 6 hours

---

## 📊 Output Examples

### Research Output
```json
{
  "project_name": "ChainGPT",
  "category": "AI",
  "verdict": "BULLISH",
  "tokenomics": {...},
  "risk_flags": [...],
  "opportunities": [...]
}
```

**Full example:** [examples/sample-research-output.json](examples/sample-research-output.json)

### Content Output
```json
{
  "article": {
    "title": "ChainGPT: AI Revolution di Blockchain...",
    "content": "<h1>...</h1>...",
    "tags": [...]
  },
  "video_script": {
    "hook": "Guys, ada AI yang bisa audit...",
    "scenes": [...]
  },
  "social_caption": {
    "caption": "🤖 AI yang bisa audit...",
    "hashtags": [...]
  }
}
```

**Full example:** [examples/sample-content-output.json](examples/sample-content-output.json)

---

## 💰 Cost Estimation

### Monthly Cost (Estimasi)

**Minimal Setup (1 artikel/hari):**
- OpenAI GPT-4o-mini: ~$3-5/bulan
- NewsAPI Free Tier: $0
- **Total: $3-5/bulan**

**Full Setup (3 artikel/hari + gambar):**
- OpenAI GPT-4o-mini: ~$10-15/bulan
- NewsAPI Free Tier: $0
- HuggingFace Free: $0 (rate limited)
- **Total: $10-15/bulan**

**Pro Setup:**
- OpenAI GPT-4o: ~$50-100/bulan
- NewsAPI Paid: $449/bulan
- Stability AI: ~$10-20/bulan
- **Total: $500+/bulan**

**Recommendation:** Mulai dari Minimal Setup, scale up sesuai kebutuhan.

### Per-Execution Cost
- Research (GPT-4o-mini): ~$0.0003
- Content Gen (GPT-4o-mini): ~$0.0005
- Image (Flux): $0 (free tier, limited)
- **Total per run:** ~$0.001 (1 artikel)

---

## 🐛 Troubleshooting

### Common Issues

**Error: "Unauthorized" / "Invalid API Key"**
- ✅ Cek API key valid
- ✅ Re-setup credential di n8n
- ✅ Pastikan billing enabled (OpenAI)

**Error: "Rate limit exceeded"**
- ✅ NewsAPI free: max 100 req/day
- ✅ Kurangi frequency atau upgrade plan
- ✅ Tambah delay antar request

**No items filtered**
- ✅ Berita mungkin tidak match keywords
- ✅ Adjust filter keywords lebih general
- ✅ Test dengan keyword lain

**Content quality rendah**
- ✅ Edit prompt di `prompts/`
- ✅ Tambah examples di prompt
- ✅ Coba model lebih besar (GPT-4o)
- ✅ Adjust temperature (0.7-0.9)

**Gambar tidak generate**
- ✅ HuggingFace free tier lambat/limited
- ✅ Tunggu beberapa saat atau retry
- ✅ Alternatif: Stability AI, DALL-E

---

## 🗺️ Roadmap

### Phase 1: Core (✅ Done)
- [x] Workflow dasar (fetch, research, content)
- [x] AI integration (OpenAI)
- [x] Multi-platform publish
- [x] Documentation lengkap

### Phase 2: Social Media Integration (✅ Done)
- [x] TikTok API integration & documentation
- [x] YouTube Shorts upload & documentation
- [x] Instagram Reels posting & documentation
- [x] Facebook Reels posting & documentation
- [x] Complete API setup guides untuk semua platform
- [x] Workflow nodes untuk multi-platform upload

### Phase 3: Video Generation (🚧 Next)
- [ ] Text-to-video integration (Pictory, Synthesia)
- [ ] Voice-over generation (ElevenLabs)
- [ ] Template-based video creation
- [ ] Stock footage integration

### Phase 4: Advanced Analytics (📋 Planned)
- [ ] Multi-language support
- [ ] Sentiment analysis
- [ ] Trend prediction with ML
- [ ] A/B testing content
- [ ] Analytics dashboard
- [ ] Community feedback loop

### Phase 4: Scale (💡 Future)
- [ ] Multiple niches (not just crypto)
- [ ] White-label solution
- [ ] API untuk developer
- [ ] Monetization features

---

## 🤝 Contributing

Kontribusi sangat welcome!

### How to Contribute:
1. Fork repo ini
2. Create branch: `git checkout -b feature/amazing-feature`
3. Commit changes: `git commit -m 'Add amazing feature'`
4. Push: `git push origin feature/amazing-feature`
5. Open Pull Request

### Areas to Contribute:
- 🐛 Bug fixes
- ✨ New features
- 📝 Documentation
- 🎨 Prompt optimization
- 🔧 Integration baru
- 🌐 Translations

---

## 📄 License

MIT License - feel free to use, modify, distribute!

---

## 🙏 Credits

**Built with:**
- [n8n](https://n8n.io) - Workflow automation
- [OpenAI](https://openai.com) - GPT models
- [NewsAPI](https://newsapi.org) - News aggregation
- [HuggingFace](https://huggingface.co) - AI image generation

**Created by:** Cupi & Claude AI

---

## 📞 Support

- 📖 **Documentation:** [docs/](docs/)
- 💬 **Issues:** [GitHub Issues](https://github.com/cupitebet/n8nVideoPendek/issues)
- 📧 **Email:** [your-email]
- 💬 **Telegram:** [your-telegram]

---

## ⚡ Quick Links

- [Quick Start Guide](docs/QUICKSTART.md) - Setup dalam 10 menit
- [Installation Guide](docs/INSTALLATION.md) - Setup lengkap step-by-step
- [Full Documentation](docs/README.md) - Dokumentasi arsitektur
- [Config Template](config/settings.json) - Template konfigurasi
- [Prompt Templates](prompts/) - Customize AI prompts

---

**Happy Automating! 🚀**

*"Bikin konten viral, tanpa harus begadang coding" - Tuyul Digital Web3*
