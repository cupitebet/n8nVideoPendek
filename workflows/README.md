# Workflows - Blueprint V2

## 🌟 CometAPI Workflows (RECOMMENDED)

**All workflows use CometAPI for simplicity and ease of use!**

---

### 1. youtube-shorts-cometapi.json ✅ ⭐

**Full automation workflow via CometAPI** - PRODUCTION READY!

**Why CometAPI?**
- ✅ Single API key untuk all models
- ✅ No complex Google Cloud setup
- ✅ Simple pay-as-you-go billing
- ✅ 5-minute setup vs 30+ minutes

**Models Used:**
- Gemini 1.5 Pro (script generation)
- Gemini 2.5 Flash Image (character images)
- Veo 3 (video generation)

**Status:** ✅ Production Ready | **12 nodes**

**What it does:**
1. Input: Topic (e.g., "AI in Gaming")
2. Generate script with Conflict Arc structure
3. Create character images per scene
4. Generate 5 video clips with Veo 3
5. Output: Video URLs for each clip

**Setup:** [CometAPI Setup Guide](../docs/COMETAPI-SETUP.md)

---

### 2. character-generator-cometapi.json ✅ ⭐

**Character sheet generator via CometAPI**

**What it does:**
1. Input: Character name + description
2. Generate character sheet dengan multiple angles
3. Output: Image URL untuk use in main workflow

**Status:** ✅ Production Ready | **4 nodes**

**Why use this?**
- Creates consistent character reference images
- Multiple angles for better Veo 3 consistency
- Reusable across multiple videos

---

## 🚀 Quick Setup

**Step 1: Get API Key**
1. Sign up at [CometAPI](https://www.cometapi.com)
2. Get API key from dashboard
3. Copy to `.env` file:
   ```bash
   cp config/environment-variables-cometapi.env.example .env
   # Edit: COMETAPI_KEY=your-key-here
   ```

**Step 2: Import Workflows**
1. Open n8n (`http://localhost:5678`)
2. **Workflows** → **Import from File**
3. Import:
   - `youtube-shorts-cometapi.json` ⭐
   - `character-generator-cometapi.json` ⭐

**Step 3: Test!**
- Just click Execute - API key loaded automatically
- No credentials configuration needed!

**[→ Full CometAPI Setup Guide](../docs/COMETAPI-SETUP.md)**

---

## 📖 How to Use

### Main Workflow Usage:

**First Time:**
1. Import workflow
2. Set environment variable: `COMETAPI_KEY`
3. Test run dengan simple topic

**Regular Use:**
1. Open workflow
2. Edit topic di "Input: Set Topic" node
3. Click **Execute Workflow**
4. Wait 2-3 minutes
5. Get video URLs from output

**No credential configuration needed!** 🎉

---

### Character Generator Usage:

**Create a Character:**
1. Open "Character Generator - CometAPI" workflow
2. Set character details:
   - `character_name`: "Alex Chen"
   - `character_description`: "young Asian male, 22 years old, round glasses, red gaming hoodie"
3. Execute workflow
4. Save the `image_url` from output
5. Use this URL in main workflow for consistent characters!

---

## 🎨 Customization

### Script Generation:
- Edit system prompt in "The Director" node (line 46)
- Adjust temperature in JSON body (currently 0.7)

**Prompt location:**
```javascript
// In "The Director (Gemini 1.5 Pro)" node
"content": `ROLE: Sutradara YouTube Shorts...`
```

### Visual Style:
- Edit prompts in "Generate Images" node
- Change image size parameter (currently 1024x1024)

**Models available:**
- `gemini-2.5-flash-image` (current, recommended)
- `gemini-1.5-flash-image` (faster, cheaper)
- Check [CometAPI docs](https://apidoc.cometapi.com) for more

### Video Settings:
- Edit Veo 3 parameters in "Generate Video" node:
  - `duration`: 5 (seconds)
  - `aspect_ratio`: "9:16" (for Shorts)

**Available options:**
```json
{
  "duration": 3-10,
  "aspect_ratio": "9:16" | "16:9" | "1:1"
}
```

---

## 🐛 Troubleshooting

### Error: "Invalid API key"
- Check `.env` file has correct `COMETAPI_KEY`
- Verify key in CometAPI dashboard
- Restart n8n to reload environment:
  ```bash
  docker restart n8n
  # or
  n8n stop && n8n start
  ```

### Error: "Insufficient credits"
- Top up credits at [CometAPI Dashboard](https://www.cometapi.com/dashboard)
- Check current balance and usage

### Workflow fails at specific node:
1. Click failed node
2. Check **Output** tab for error message
3. Common issues:
   - Empty prompts → Check input data
   - Timeout → Increase wait time in "Wait for Video Processing" node
   - Invalid format → Check JSON syntax

### Images/Videos not generating:
**Checklist:**
- ✅ API key valid and has credits
- ✅ Model names correct: `gemini-2.5-flash-image`, `veo3`
- ✅ Prompts not empty
- ✅ CometAPI service status OK

**Debug mode:**
```javascript
// Add Code node before failed node
console.log('Input data:', $json);
return $input.all();
```

### Video processing takes too long:
- Veo 3 typically takes 30-60 seconds per clip
- Current wait time: 30 seconds (may need adjustment)
- If fails, increase wait time to 60 seconds:
  ```json
  // In "Wait for Video Processing" node
  "amount": 60
  ```

---

## 💰 Cost Estimates (CometAPI)

### Per Video (5 scenes)

| Component | Usage | Est. Cost |
|-----------|-------|-----------|
| Gemini 1.5 Pro (script) | 1 request | ~$0.001 |
| Gemini 2.5 Flash Image | 5 images | ~$0.01 |
| Veo 3 (5 clips × 5s) | 25 seconds | ~$0.50-1.00 |
| **Total per video** | | **~$0.51-1.01** |

### Monthly Estimates

| Videos/Month | Total Cost |
|--------------|------------|
| 10 videos | ~$5-10 |
| 30 videos | ~$15-30 |
| 100 videos | ~$50-100 |

**Much cheaper than subscriptions ($90+/month)!** 💰

**Cost optimization tips:**
1. Use shorter clips (3s instead of 5s)
2. Reduce scenes per video (3-4 instead of 5)
3. Reuse character sheets across videos
4. Monitor usage in CometAPI dashboard

---

## 📚 Workflow Architecture

### youtube-shorts-cometapi.json

```
Input Topic
    ↓
┌─────────────────────────────┐
│  The Director               │
│  (Gemini 1.5 Pro)          │
│  - Generate script          │
└────────────┬────────────────┘
             ↓
┌─────────────────────────────┐
│  Parse Script               │
│  - Extract scenes           │
└────────────┬────────────────┘
             ↓
┌─────────────────────────────┐
│  Split Scenes               │
│  - Process individually     │
└────────────┬────────────────┘
             ↓
    ┌────────────────┐
    │  For each scene │
    └────────┬────────┘
             ↓
┌─────────────────────────────┐
│  Generate Images            │
│  (Gemini 2.5 Flash)        │
└────────────┬────────────────┘
             ↓
┌─────────────────────────────┐
│  Generate Video (Veo 3)    │
│  - Image to video           │
│  - With audio               │
└────────────┬────────────────┘
             ↓
┌─────────────────────────────┐
│  Wait & Check Status        │
└────────────┬────────────────┘
             ↓
┌─────────────────────────────┐
│  Aggregate All Scenes       │
└────────────┬────────────────┘
             ↓
      Final Output
   (Video URLs ready!)
```

---

## 📦 Old Workflows (Archived)

**Google Cloud Direct workflows moved to `old/workflows/`:**
- `youtube-shorts-veo-v2.json` - Google Cloud version (archived)
- `character-generator.json` - Google Cloud version (archived)

**Why archived?**
- ❌ Complex setup (30+ minutes)
- ❌ Multiple credentials needed
- ❌ Complex GCP billing
- ✅ CometAPI is simpler and faster

**Note:** If you need Google Cloud direct for large scale (1000+ videos/month), workflows are still available in `old/workflows/` but not actively maintained.

---

## 🎯 Next Steps

1. ✅ [Setup CometAPI](../docs/COMETAPI-SETUP.md)
2. ✅ [Import workflows](#quick-setup)
3. ✅ [Quick Start Guide](../docs/QUICKSTART.md)
4. ✅ [Master character consistency](../docs/CHARACTER-CONSISTENCY.md)
5. ✅ Start creating videos! 🎬

---

## 🆘 Need Help?

**Documentation:**
- 📖 [Quick Start](../docs/QUICKSTART.md)
- 📖 [CometAPI Setup](../docs/COMETAPI-SETUP.md)
- 📖 [Character Consistency Guide](../docs/CHARACTER-CONSISTENCY.md)

**External Resources:**
- 💬 [CometAPI Help Center](https://apidoc.cometapi.com/help-center.md)
- 📖 [CometAPI Documentation](https://apidoc.cometapi.com)
- 🎥 [n8n Documentation](https://docs.n8n.io/)

**Community:**
- 🐛 [GitHub Issues](https://github.com/cupitebet/n8nVideoPendek/issues)
- 💬 Discord/Telegram (Coming Soon)

---

**Happy automating! 🎬**

*CometAPI makes AI video creation accessible to everyone!*
