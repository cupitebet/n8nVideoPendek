# 📚 Documentation - Blueprint V2

**Complete guide untuk YouTube Shorts Automation menggunakan CometAPI**

---

## 🚀 Getting Started

**New to Blueprint V2? Start here:**

1. **[CometAPI Setup Guide](COMETAPI-SETUP.md)** ⭐ **START HERE**
   - Sign up for CometAPI (5 minutes)
   - Get your API key
   - Setup n8n with environment variables
   - Import workflows and go!

2. **[Quick Start Guide](QUICKSTART.md)** ⚡
   - Dari nol ke video pertama dalam 15 menit
   - Step-by-step walkthrough
   - First test run tutorial

3. **[Character Consistency Guide](CHARACTER-CONSISTENCY.md)** 👤
   - Master character consistency (the #1 challenge)
   - Using character sheets effectively
   - Prompt engineering for consistency

---

## 🎓 Core Concepts

### What is CometAPI?

**CometAPI** adalah API aggregator yang menyediakan akses ke berbagai AI models dengan **satu API key**:

✅ **Gemini 1.5 Pro** - Script generation
✅ **Gemini 2.5 Flash Image** ("Nano Banana") - Character images
✅ **Veo 3** - Video generation
✅ **100+ other models** - OpenAI, Claude, Stable Diffusion, etc.

**Why CometAPI?**
- ✅ **Single API key** untuk semua models
- ✅ **Simple billing** - pay-as-you-go credit system
- ✅ **Fast setup** - 5 minutes vs 30+ minutes
- ✅ **No complex credentials** - just one environment variable

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

**Usage:**
```javascript
// In n8n workflow - already embedded!
// Check "The Director" node in youtube-shorts-cometapi.json
```

---

## ⚙️ Configuration

### Config Files

**Location:** `../config/`

**Files:**
- `environment-variables-cometapi.env.example` - CometAPI setup template
- `veo-settings.json` - Veo 3 API configuration reference

**What you can configure:**
- Video length (3-10 seconds per clip)
- Aspect ratio (9:16 for Shorts, 16:9, 1:1)
- Temperature untuk AI models (creativity level)
- Cost limits dan alerts

**Setup:**
```bash
# Copy template
cp config/environment-variables-cometapi.env.example .env

# Edit and add your key
COMETAPI_KEY=sk-your-key-here
```

---

## 🔧 Workflows

### n8n Workflows

**Location:** `../workflows/`

**Available workflows:**
- `youtube-shorts-cometapi.json` ✅ **Production Ready**
  - Full automation from topic to video URLs
  - 12 nodes, fully tested

- `character-generator-cometapi.json` ✅ **Production Ready**
  - Standalone character sheet generator
  - 4 nodes, reusable across videos

**See:** [Workflows README](../workflows/README.md)

---

## 📖 Guides by Topic

### Setup & Installation
- ✅ **[CometAPI Setup](COMETAPI-SETUP.md)** ⭐ START HERE - 5 minute setup
- ✅ **[Quick Start](QUICKSTART.md)** - First video in 15 minutes

### Content Creation
- ✅ **[Character Consistency](CHARACTER-CONSISTENCY.md)** - Master character consistency
- 📝 **Prompt Engineering Guide** (see templates in `../prompts/`)
- 📝 **Script Writing Guide** (see director-system-prompt.txt)

### Technical
- ✅ **[Config Files](../config/)** - Configuration reference
- ✅ **[Workflows](../workflows/README.md)** - Workflow documentation
- 📝 **API Reference** (see [CometAPI Docs](https://apidoc.cometapi.com))

---

## 💡 Examples

### Sample Outputs

**Location:** `../examples/`

**Files:**
- `sample-script-output.json` - Example script dari The Director
- `sample-character-sheet.json` - Example character definition
- `README.md` - How to use examples

**Usage:**
Study these files to understand the expected output format from each workflow stage.

---

## 🗺️ Learning Path

### For Beginners (Week 1)

**Day 1-2: Setup**
1. [Sign up for CometAPI](COMETAPI-SETUP.md#step-1-sign-up--get-api-key-2-minutes)
2. Install n8n (Docker recommended)
3. Import workflows

**Day 3-4: First Videos**
1. Follow [Quick Start Guide](QUICKSTART.md)
2. Create your first 3 videos
3. Learn from outputs

**Day 5-7: Optimization**
1. Study [Character Consistency Guide](CHARACTER-CONSISTENCY.md)
2. Experiment with prompts
3. Create character library

### For Advanced Users

**Focus areas:**
- Custom prompt engineering
- Batch processing workflows
- Cost optimization strategies
- Integration with other tools (Creatomate, etc.)

---

## 🆘 Getting Help

### Common Issues

**"API not enabled" or "Invalid API key"**
→ [CometAPI Setup](COMETAPI-SETUP.md#troubleshooting)

**"Insufficient credits"**
→ Top up at [CometAPI Dashboard](https://www.cometapi.com/dashboard)

**"Character not consistent"**
→ [Character Consistency Guide](CHARACTER-CONSISTENCY.md)

**Workflow errors**
→ [Workflows Troubleshooting](../workflows/README.md#-troubleshooting)

### Community Support
- 🐛 [GitHub Issues](https://github.com/cupitebet/n8nVideoPendek/issues)
- 💬 [CometAPI Help Center](https://apidoc.cometapi.com/help-center.md)
- 📖 [CometAPI Documentation](https://apidoc.cometapi.com)

---

## 📊 Architecture Overview

```
Input: Topic
    ↓
┌─────────────────────────────────────┐
│  STAGE 1: THE DIRECTOR              │
│  (Gemini 1.5 Pro via CometAPI)      │
│  - Generate script                  │
│  - Conflict Arc structure           │
│  - Scene breakdown with prompts     │
└─────────────┬───────────────────────┘
              ↓
┌─────────────────────────────────────┐
│  STAGE 2: VISUAL FACTORY            │
│                                     │
│  For Each Scene:                    │
│    A. Generate Character Image      │
│       (Gemini 2.5 Flash via CometAPI)│
│                                     │
│    B. Generate Video Clip           │
│       (Veo 3 via CometAPI)          │
│       - Image-to-video              │
│       - Native audio (SFX)          │
│       - 5 second clips               │
└─────────────┬───────────────────────┘
              ↓
┌─────────────────────────────────────┐
│  STAGE 3: OUTPUT                    │
│  - Aggregate all scenes             │
│  - Format video URLs                │
│  - Ready for download/assembly      │
└─────────────┬───────────────────────┘
              ↓
Output: Video URLs for each scene
        (Download & combine manually or
         use Creatomate for automation)
```

---

## 💰 Cost Breakdown

### Per Video (5 scenes, 60 seconds total)

| Component | Cost | Details |
|-----------|------|---------|
| Gemini 1.5 Pro (script) | ~$0.001 | 1 request |
| Gemini Flash (5 images) | ~$0.010 | 5 images × $0.002 |
| Veo 3 (5 clips) | ~$0.50-1.00 | 5 clips × 5s |
| **Total** | **~$0.51-1.01** | per video |

### Monthly Estimates

| Videos/Month | Cost/Month | Per Video |
|--------------|------------|-----------|
| 10 videos | ~$5-10 | ~$0.50-1.00 |
| 30 videos | ~$15-30 | ~$0.50-1.00 |
| 100 videos | ~$50-100 | ~$0.50-1.00 |

**Way cheaper than subscriptions!** 💰

Compare with traditional tools:
- Runway Gen-3: ~$10-20/month (limited)
- Midjourney: $30-60/month
- Various subscriptions: $90+/month total

**CometAPI = Pay only for what you use!**

---

## 🔗 Quick Links

### Essential Docs
- ⭐ [CometAPI Setup](COMETAPI-SETUP.md) - **START HERE**
- ⚡ [Quick Start](QUICKSTART.md) - First video in 15 min
- 👤 [Character Consistency](CHARACTER-CONSISTENCY.md) - Master consistency

### Resources
- [Main README](../README.md) - Project overview
- [Workflows](../workflows/README.md) - Workflow docs
- [Examples](../examples/README.md) - Sample outputs
- [Prompts](../prompts/) - Prompt templates
- [Config](../config/) - Configuration files

### External
- [CometAPI Docs](https://apidoc.cometapi.com) - Official API docs
- [CometAPI Pricing](https://api.cometapi.com/pricing) - Current pricing
- [n8n Documentation](https://docs.n8n.io/) - n8n help

---

## 🎯 Next Steps

**Ready to start?**

1. ✅ [Setup CometAPI](COMETAPI-SETUP.md) (5 minutes)
2. ✅ [Run Quick Start](QUICKSTART.md) (15 minutes)
3. ✅ [Master Character Consistency](CHARACTER-CONSISTENCY.md)
4. ✅ Create your first 5 videos
5. ✅ Share results dan optimize!

---

## 📦 Archived Content

**Google Cloud Direct workflows** have been moved to `old/workflows/`:
- `youtube-shorts-veo-v2.json` - Google Cloud version
- `character-generator.json` - Google Cloud version

**Why archived?**
- ❌ Complex setup (30+ minutes)
- ❌ Multiple credentials needed
- ❌ Complex GCP billing
- ✅ **CometAPI is simpler, faster, and easier**

For users who need Google Cloud direct (large scale 1000+ videos/month), files are still available but not actively maintained.

---

**Happy Learning! 📚✨**

*Blueprint V2 - Making AI video creation accessible to everyone through CometAPI*
