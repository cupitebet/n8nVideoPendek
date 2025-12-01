# Workflows - Blueprint V2

## 🌟 CometAPI Workflows (RECOMMENDED)

### 1. youtube-shorts-cometapi.json ✅ ⭐

**Full automation workflow via CometAPI** - EASIEST TO USE!

**Why CometAPI?**
- ✅ Single API key untuk all models
- ✅ No complex Google Cloud setup
- ✅ Simple pay-as-you-go billing
- ✅ 5-minute setup vs 30+ minutes

**Models Used:**
- Gemini 1.5 Pro (script generation)
- Gemini 2.5 Flash Image (character images)
- Veo 3 (video generation)

**Status:** ✅ Available | **12 nodes**

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

**Status:** ✅ Available | **4 nodes**

---

## 🔧 Google Cloud Workflows (Advanced Users)

### 3. youtube-shorts-veo-v2.json ✅

**Full automation workflow via Google Cloud Direct**

**For users who:**
- Need large scale (1000+ videos/month)
- Want custom quotas
- Have existing GCP infrastructure

**Models Used:**
- Google Veo 3.1 (video generation)
- Gemini 2.5 Flash Image (character images)
- Gemini 1.5 Pro (script generation)
- Creatomate (video assembly)

**Status:** ✅ Available | **14 nodes**

**What it does:**
1. Input: Topic (e.g., "AI Gaming Revolution")
2. Generate script with Conflict Arc structure
3. Create character reference images
4. Generate 5 video clips dengan Veo 3.1
5. Add voiceover dengan Cloud TTS
6. Assemble final video dengan subtitles
7. Output: 60-second YouTube Short ready to upload

**Setup:** [Google Cloud Setup Guide](../docs/GOOGLE-CLOUD-SETUP.md)

---

### 4. character-generator.json ✅

**Character sheet generator via Google Cloud**

**What it does:**
1. Input: Character description
2. Generate character sheet dengan multiple angles
3. Upload to Google Cloud Storage
4. Output: GCS URI untuk use in Veo

**Status:** ✅ Available | **6 nodes**

---

## 🚀 Quick Setup

### For CometAPI (Recommended) ⭐

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

### For Google Cloud (Advanced)

**[→ Google Cloud Setup Guide](../docs/GOOGLE-CLOUD-SETUP.md)**
**[→ Workflow Setup Guide](../docs/WORKFLOW-SETUP.md)**

---

## How to Use

### CometAPI Workflow Usage:

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

### Google Cloud Workflow Usage:

**First Time:**
1. Import workflow
2. Configure Google Cloud credentials (see `config/google-credentials-setup.md`)
3. Update project ID di HTTP Request nodes
4. Update bucket name di Cloud Storage nodes
5. Test run

**Regular Use:**
1. Open workflow
2. Input topic di Manual Trigger node
3. Click **Execute Workflow**
4. Wait 3-5 minutes
5. Download final video from output node

---

## Customization

### CometAPI Workflows:

**Script Generation:**
- Edit system prompt in "The Director" node
- Adjust temperature in JSON body

**Visual Style:**
- Edit prompts in "Generate Images" node
- Change image size parameter

**Video Settings:**
- Edit Veo 3 parameters (duration, aspect_ratio)
- Adjust quality via prompts

### Google Cloud Workflows:

**Script Generation (The Director):**
- Edit system prompt di Gemini 1.5 Pro node
- Adjust temperature untuk more/less creative output

**Visual Style:**
- Edit character sheet template di Image Gen node
- Change art style keywords

**Video Settings:**
- Edit Veo 3.1 parameters (videoLength, aspectRatio)
- Adjust quality settings

**Audio:**
- Change TTS voice di Cloud TTS node
- Add background music di Creatomate template

---

## Troubleshooting

### CometAPI Workflows:

**Error: "Invalid API key"**
- Check `.env` file has correct `COMETAPI_KEY`
- Verify key in CometAPI dashboard
- Restart n8n to reload environment

**Error: "Insufficient credits"**
- Top up credits at [CometAPI Dashboard](https://www.cometapi.com/dashboard)

**Workflow fails at specific node:**
- Check node output for error message
- Verify API key loaded: Add Code node with `console.log($env.COMETAPI_KEY)`
- Check CometAPI service status

---

### Google Cloud Workflows:

**Workflow fails at Gemini node:**
- Check credential configured
- Check API enabled di Google Cloud
- Check quota limits

**Workflow fails at Veo 3.1 node:**
- Check project ID correct
- Check bucket exists
- Check character reference image URL valid

**Final video has issues:**
- Check all 5 clips generated successfully
- Check Creatomate template configured
- Check subtitle generation working

---

## Comparison

| Feature | CometAPI | Google Cloud |
|---------|----------|--------------|
| **Setup Time** | 5 minutes | 30+ minutes |
| **API Keys** | 1 key | Multiple credentials |
| **Billing** | Simple credits | Complex GCP billing |
| **Cost (30 videos)** | ~$15-30 | ~$16-20 (after setup) |
| **Best For** | Quick start, ease of use | Large scale, custom control |

**Recommendation: Start with CometAPI!** ⭐

---

## Old Workflows (Archived)

Old workflows moved to `old/workflows/`:
- `majelis-digital-marketing.json` - Old Majelis workflow
- `tuyul-digital-web3.json` - Old crypto content workflow

These are **NOT compatible** with Blueprint V2!

---

## Next Steps

### For CometAPI Users:
1. ✅ [Setup CometAPI](../docs/COMETAPI-SETUP.md)
2. ✅ [Import workflows](#for-cometapi-recommended-)
3. ✅ [Test run](#cometapi-workflow-usage)
4. ✅ [Master character consistency](../docs/CHARACTER-CONSISTENCY.md)

### For Google Cloud Users:
1. ✅ [Setup Google Cloud](../docs/GOOGLE-CLOUD-SETUP.md)
2. ✅ [Import workflows](../docs/QUICKSTART.md)
3. ✅ [Test run](../docs/QUICKSTART.md#step-4-test-run)
4. ✅ [Master character consistency](../docs/CHARACTER-CONSISTENCY.md)

---

**Happy automating! 🎬**

*Choose CometAPI for easiest setup, or Google Cloud for advanced control!*
