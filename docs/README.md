# 📚 Documentation - Blueprint V2

**Complete guide untuk YouTube Shorts Automation menggunakan Google Veo 3.1**

---

## 🚀 Getting Started

**New to Blueprint V2? Start here:**

1. **[Quick Start Guide](QUICKSTART.md)** ⚡
   - Dari nol ke video pertama dalam 30 menit
   - Prerequisites dan setup checklist
   - First test run walkthrough

2. **[Google Cloud Setup](GOOGLE-CLOUD-SETUP.md)** ☁️
   - Enable APIs (Veo 3.1, Gemini, Cloud TTS)
   - Create service account
   - Setup billing dan quotas
   - Security best practices

---

## 🎓 Core Concepts

### Character Consistency

**[Character Consistency Guide](CHARACTER-CONSISTENCY.md)** 👤

The #1 problem dengan AI video: karakter berubah-ubah.

**Learn:**
- How to create character sheets dengan Gemini Flash
- Using reference images dengan Veo 3.1
- Prompt engineering untuk consistency
- Testing dan troubleshooting

**Essential reading untuk professional-looking videos!**

---

## 🎨 Content Creation

### Prompts & Templates

**Location:** `../prompts/`

**Files:**
- `director-system-prompt.txt` - The Director system prompt
- `character-sheet-template.txt` - Template untuk character creation
- `visual-prompt-template.txt` - Structure untuk visual prompts

**Key concepts:**
- Conflict Arc storytelling structure
- Visual prompt anatomy
- Sound effect descriptions
- Consistency keywords

---

## ⚙️ Configuration

### Config Files

**Location:** `../config/`

**Files:**
- `veo-settings.json` - Veo 3.1 API configuration
- `google-credentials-setup.md` - Credentials setup guide

**What you can configure:**
- Video length (5s, 8s, 10s)
- Aspect ratio (9:16, 1:1, 16:9)
- Quality settings
- Temperature untuk AI models
- Cost limits dan alerts

---

## 🔧 Workflows

### n8n Workflows

**Location:** `../workflows/`

**Available workflows:**
- `youtube-shorts-veo-v2.json` - Full automation (🚧 Coming Soon)
- `youtube-shorts-manual.json` - Semi-manual for learning (🚧 Coming Soon)
- `character-generator.json` - Standalone character creator (🚧 Coming Soon)

**See:** [Workflows README](../workflows/README.md)

---

## 📖 Guides by Topic

### Setup & Installation
- ✅ [Quick Start](QUICKSTART.md) - 30 minute setup
- ✅ [Google Cloud Setup](GOOGLE-CLOUD-SETUP.md) - Detailed GCP guide

### Content Creation
- ✅ [Character Consistency](CHARACTER-CONSISTENCY.md) - Master character consistency
- 🚧 Prompt Engineering Guide (Coming Soon)
- 🚧 Script Writing Guide (Coming Soon)

### Technical
- ✅ [Config Files](../config/) - Configuration reference
- 🚧 API Reference (Coming Soon)
- 🚧 Troubleshooting Guide (Coming Soon)

### Advanced
- 🚧 Multi-Character Videos (Coming Soon)
- 🚧 Custom Styles & Themes (Coming Soon)
- 🚧 Batch Processing (Coming Soon)
- 🚧 Analytics & Optimization (Coming Soon)

---

## 💡 Examples

### Sample Outputs

**Location:** `../examples/`

**Files:**
- `sample-script-output.json` - Example script dari The Director
- `sample-character-sheet.json` - Example character definition
- 🚧 `sample-veo-request.json` (Coming Soon)
- 🚧 `sample-final-video-metadata.json` (Coming Soon)

**See:** [Examples README](../examples/README.md)

---

## 🗺️ Learning Path

### For Beginners

**Week 1: Foundation**
1. Day 1-2: Setup Google Cloud
2. Day 3: Import workflow dan test run pertama
3. Day 4-5: Understand output structure
4. Day 6-7: Create first custom character

**Week 2: Customization**
1. Day 8-10: Master prompt engineering
2. Day 11-12: Experiment dengan styles
3. Day 13-14: Create 5 different videos

**Week 3: Optimization**
1. Day 15-17: Optimize for consistency
2. Day 18-19: Improve quality settings
3. Day 20-21: Batch process videos

### For Advanced Users

**Focus areas:**
- Multi-character storylines
- Custom art styles
- Advanced Veo 3.1 parameters
- Cost optimization strategies
- Production workflows

---

## 🆘 Getting Help

### Common Issues

**"API not enabled"**
→ [Google Cloud Setup](GOOGLE-CLOUD-SETUP.md#step-3-enable-required-apis)

**"Quota exceeded"**
→ [Google Cloud Setup](GOOGLE-CLOUD-SETUP.md#step-6-request-quota-increase)

**"Character not consistent"**
→ [Character Consistency Guide](CHARACTER-CONSISTENCY.md#common-issues--solutions)

**"Permission denied"**
→ [Credentials Setup](../config/google-credentials-setup.md#troubleshooting)

### Full Troubleshooting
🚧 Comprehensive troubleshooting guide (Coming Soon)

### Community Support
- 💬 [GitHub Issues](https://github.com/cupitebet/n8nVideoPendek/issues)
- 📧 Email support
- 💬 Discord community (Coming Soon)

---

## 📊 Architecture Overview

```
Input: Topic
    ↓
┌─────────────────────────────────────┐
│  STAGE 1: THE DIRECTOR              │
│  (Gemini 1.5 Pro)                   │
│  - Generate script                  │
│  - Conflict Arc structure           │
│  - Scene breakdown                  │
└─────────────┬───────────────────────┘
              ↓
┌─────────────────────────────────────┐
│  STAGE 2: VISUAL FACTORY            │
│                                     │
│  Node A: Character Images           │
│  (Gemini 2.5 Flash Image)          │
│  - Create character sheets          │
│  - Generate scene images            │
│                                     │
│  Node B: Video Generation           │
│  (Veo 3.1)                          │
│  - Image-to-video conversion        │
│  - Native audio (SFX + ambience)   │
│  - 5-10 second clips                │
└─────────────┬───────────────────────┘
              ↓
┌─────────────────────────────────────┐
│  STAGE 3: ASSEMBLY                  │
│                                     │
│  Node C: Voiceover (Optional)       │
│  (Cloud Text-to-Speech)             │
│  - Narrator voice                   │
│                                     │
│  Node D: Final Edit                 │
│  (Creatomate)                       │
│  - Combine clips                    │
│  - Add subtitles                    │
│  - Add background music             │
└─────────────┬───────────────────────┘
              ↓
Output: 60-second YouTube Short (MP4)
```

---

## 💰 Cost Breakdown

### Per Video (30 videos/month)

| Component | Cost | Monthly |
|-----------|------|---------|
| Gemini 1.5 Pro (script) | $0.001 | $0.03 |
| Gemini Flash (5 images) | $0.0025 | $0.075 |
| Veo 3.1 (5 clips) | $0.50 | $15.00 |
| Cloud TTS (500 chars) | $0.008 | $0.24 |
| Cloud Storage | $0.02 | $0.60 |
| **Total** | **~$0.51** | **~$16** |

**Way cheaper than subscriptions ($90+/month)!**

---

## 🔗 Quick Links

### Essential Docs
- [Quick Start](QUICKSTART.md)
- [Google Cloud Setup](GOOGLE-CLOUD-SETUP.md)
- [Character Consistency](CHARACTER-CONSISTENCY.md)

### Resources
- [Main README](../README.md)
- [Workflows](../workflows/README.md)
- [Examples](../examples/README.md)
- [Prompts](../prompts/)
- [Config](../config/)

### External
- [Google Veo 3.1 Docs](https://cloud.google.com/vertex-ai/docs/generative-ai/video/overview)
- [Gemini API Docs](https://ai.google.dev/gemini-api/docs)
- [n8n Documentation](https://docs.n8n.io/)

---

## 🎯 Next Steps

**Already read the docs?**

1. ✅ [Setup Google Cloud](GOOGLE-CLOUD-SETUP.md)
2. ✅ [Run Quick Start](QUICKSTART.md)
3. ✅ [Master Character Consistency](CHARACTER-CONSISTENCY.md)
4. ✅ Create your first 5 videos
5. ✅ Share results dan iterate!

---

**Happy Learning! 📚✨**

*Blueprint V2 - Making AI video creation accessible to everyone*
