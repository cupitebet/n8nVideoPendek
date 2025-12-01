# 🌟 CometAPI Setup Guide - Blueprint V2

**Cara termudah untuk akses Gemini, Veo 3, dan model AI lainnya!**

---

## Why CometAPI?

CometAPI adalah API aggregator yang menyediakan akses ke berbagai AI models dengan **satu API key**:

✅ **Gemini 1.5 Pro** - Script generation
✅ **Gemini 2.5 Flash Image** ("Nano Banana") - Character images
✅ **Veo 3** - Video generation
✅ **OpenAI, Claude, dan 100+ models lainnya**

### Keuntungan vs Direct Google Cloud:

| Feature | CometAPI | Google Cloud Direct |
|---------|----------|---------------------|
| Setup Time | **5 minutes** | 30+ minutes |
| API Keys | **1 key** for all | Multiple accounts |
| Billing | Simple credit system | Complex GCP billing |
| Quota | Ready to use | Need quota requests |
| Service Account | Not needed | Complex setup |
| Credentials | Just API key | JSON keys, OAuth2 |

**🎉 CometAPI = Lebih mudah, lebih cepat!**

---

## Step 1: Sign Up & Get API Key (2 minutes)

### 1.1 Create Account

1. Go to [https://www.cometapi.com](https://www.cometapi.com)
2. Click **Sign Up**
3. Register dengan email atau Google
4. Verify email

### 1.2 Top Up Credits

1. Go to **Dashboard** → **Billing**
2. Choose top-up amount (minimum biasanya $5-10)
3. Add credits via:
   - Credit Card
   - PayPal
   - Crypto (jika tersedia)

**Recommended starting amount: $20** (cukup untuk ~30-40 videos)

### 1.3 Get API Key

1. Go to **Dashboard** → **API Keys**
2. Click **Create New Key**
3. Name: `n8n-youtube-shorts`
4. **Copy and save your API key** - ini hanya muncul sekali!

**⚠️ IMPORTANT: Simpan key dengan aman! Jangan share atau commit ke Git!**

---

## Step 2: Setup Environment Variables (1 minute)

### 2.1 Copy Template

```bash
cd /path/to/n8nVideoPendek
cp config/environment-variables-cometapi.env.example .env
```

### 2.2 Edit .env File

```bash
nano .env
# atau
vim .env
# atau buka dengan text editor
```

Fill in your API key:

```bash
# CometAPI API Key (Required)
COMETAPI_KEY=sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

# Optional settings (defaults are fine)
DAILY_MAX_VIDEOS=10
MONTHLY_BUDGET_USD=100
```

### 2.3 Verify .env Protected

```bash
# Check gitignore includes .env
cat .gitignore | grep "\.env"

# Should output: .env
```

---

## Step 3: Load in n8n (2 minutes)

### Option A: Docker (Recommended)

```bash
docker run -d --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  --env-file .env \
  n8nio/n8n
```

### Option B: npm Install

```bash
# Set environment variable
export COMETAPI_KEY=your-key-here

# Start n8n
n8n start
```

### Option C: n8n Cloud

1. Go to n8n **Settings** → **Environment Variables**
2. Add variable:
   - Name: `COMETAPI_KEY`
   - Value: `your-key-here`
3. Save

### Verify Environment Variable Loaded

In n8n, create a test workflow:

1. Add **Code** node
2. Code:
   ```javascript
   console.log('API Key:', $env.COMETAPI_KEY);
   return [{ json: { loaded: !!$env.COMETAPI_KEY } }];
   ```
3. Execute

**Expected output:** `{ loaded: true }`

---

## Step 4: Import Workflows (2 minutes)

### 4.1 Import Main Workflow

1. Open n8n: `http://localhost:5678`
2. **Workflows** → **Import from File**
3. Select: `workflows/youtube-shorts-cometapi.json`
4. Click **Import**

### 4.2 Import Character Generator

1. **Workflows** → **Import from File**
2. Select: `workflows/character-generator-cometapi.json`
3. Click **Import**

---

## Step 5: Test Run (3 minutes)

### Test Character Generator First

1. Open **Character Generator - CometAPI** workflow
2. Click **"Input: Character Details"** node
3. Set values:
   ```
   character_name: Test Character
   character_description: young male with glasses and red hoodie
   ```
4. Click **Execute Workflow**
5. Wait ~10-15 seconds
6. Check output:
   - ✅ `status`: "completed"
   - ✅ `image_url`: https://...
   - ✅ Click URL to see character sheet!

### Test Main Workflow

1. Open **YouTube Shorts - CometAPI** workflow
2. Click **"Input: Set Topic"** node
3. Set value:
   ```
   topic: AI in Gaming
   ```
4. Click **Execute Workflow**
5. Wait ~2-3 minutes (Veo 3 takes time)
6. Monitor progress through each node
7. Final output should contain:
   - ✅ Script with 5 scenes
   - ✅ Image URLs for each scene
   - ✅ Video URLs for each clip

---

## Pricing & Cost Estimate

### CometAPI Pricing

Check latest prices: [https://api.cometapi.com/pricing](https://api.cometapi.com/pricing)

**Estimated costs per video (as of Dec 2025):**

| Model | Usage | Est. Cost |
|-------|-------|-----------|
| Gemini 1.5 Pro | 1 script generation | ~$0.001 |
| Gemini 2.5 Flash Image | 5 images | ~$0.01 |
| Veo 3 | 5 video clips (5s each) | ~$0.50-1.00 |
| **Total per video** | | **~$0.51-1.01** |

**Monthly (30 videos): ~$15-30**

💡 **Tip:** Costs vary by model usage. Check your dashboard regularly!

---

## Troubleshooting

### Error: "Invalid API key"

**Solution:**
```bash
# Check .env file
cat .env | grep COMETAPI_KEY

# Verify key format (should start with sk-)
# Re-create key in dashboard if needed
```

### Error: "Insufficient credits"

**Solution:**
1. Go to [CometAPI Dashboard](https://www.cometapi.com/dashboard)
2. Check **Balance**
3. Top up if needed

### Error: "Rate limit exceeded"

**Solution:**
1. CometAPI has rate limits per key
2. Wait a few seconds and retry
3. Or upgrade your plan for higher limits

### Workflow fails at specific node

**Debug steps:**
1. Click failed node
2. Check **Input** tab - what data received?
3. Check **Output** tab - what error?
4. Verify API key loaded: Add Code node with `console.log($env.COMETAPI_KEY)`

### Images/Videos not generating

**Check:**
1. ✅ API key valid and has credits
2. ✅ Model names correct: `gemini-2.5-flash-image`, `veo3`
3. ✅ Prompts not empty
4. ✅ CometAPI service status: [Status Page](https://status.cometapi.com) (if exists)

---

## API Documentation Links

### CometAPI Official Docs

- 📖 [Quick Start](https://apidoc.cometapi.com/qucick-start.md)
- 📖 [n8n Integration Guide](https://apidoc.cometapi.com/n8n.md)
- 📖 [Gemini 2.5 Flash Image Guide](https://apidoc.cometapi.com/guide-nanobanana.md)
- 📖 [Veo 3 Documentation](https://apidoc.cometapi.com/video/veo3/video-create.md)
- 📖 [Chat Completions (Gemini 1.5 Pro)](https://apidoc.cometapi.com/chat.md)
- 📖 [Full API Reference](https://apidoc.cometapi.com)

### Useful Links

- 💰 [Pricing](https://api.cometapi.com/pricing)
- 📊 [Dashboard](https://www.cometapi.com/dashboard)
- 💬 [Help Center](https://apidoc.cometapi.com/help-center.md)

---

## Security Best Practices

### ✅ DO:
- Store API key in `.env` file
- Add `.env` to `.gitignore`
- Rotate keys every 90 days
- Monitor usage in dashboard
- Set billing alerts

### ❌ DON'T:
- Commit API key to Git
- Share key via email/chat
- Use same key for prod & dev
- Leave unused keys active
- Ignore unusual usage spikes

---

## Cost Optimization Tips

### 1. Test with Character Generator First
- Character generator only uses image generation (~$0.002)
- Test your prompts before running full workflow

### 2. Reduce Video Length
- Veo 3 charges by second
- Use 3-5 second clips instead of 8-10 seconds
- Still effective for Shorts!

### 3. Reuse Character Sheets
- Generate character once
- Save image URL
- Reuse in multiple videos
- Saves on image generation costs

### 4. Monitor Dashboard
- Check daily usage
- Set up billing alerts
- Identify cost spikes early

### 5. Batch Processing
- Don't generate 1 video at a time
- Queue multiple topics
- Process in batches for efficiency

---

## Next Steps

After CometAPI setup complete:

1. ✅ [Test workflows](QUICKSTART-COMETAPI.md)
2. ✅ [Master character consistency](CHARACTER-CONSISTENCY.md)
3. ✅ [Customize prompts](../prompts/)
4. ✅ [Learn n8n best practices](WORKFLOW-SETUP-COMETAPI.md)

---

## Comparison with Google Cloud

**When to use CometAPI:**
- ✅ Quick setup needed
- ✅ Don't want complex GCP setup
- ✅ Prefer simple billing
- ✅ Want one API for all models
- ✅ Small to medium scale (< 1000 videos/month)

**When to use Google Cloud Direct:**
- Large scale (> 1000 videos/month) - better pricing
- Need custom quotas
- Want full control over infrastructure
- Enterprise security requirements

**For most users: CometAPI is recommended!** 🎉

---

**Setup complete! Time to create videos! 🎬**

*Questions? Check [CometAPI Help Center](https://apidoc.cometapi.com/help-center.md)*
