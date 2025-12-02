# ⚡ Quick Start Guide - Blueprint V2

**From zero to your first AI-generated video in 15 minutes!**

---

## Prerequisites

Before starting, make sure you have:
- ✅ Computer with Docker installed (or npm for n8n)
- ✅ Internet connection
- ✅ Credit card for CometAPI signup (~$5-10 initial credit recommended)

---

## Step 1: Get CometAPI Access (3 minutes)

### 1.1 Sign Up

1. Go to [https://www.cometapi.com](https://www.cometapi.com)
2. Click **Sign Up**
3. Use email or Google account
4. Verify your email

### 1.2 Add Credits

1. Navigate to **Dashboard** → **Billing**
2. Click **Add Credits**
3. Add $10 (enough for 10-20 videos)
4. Complete payment

### 1.3 Get API Key

1. Go to **Dashboard** → **API Keys**
2. Click **Create New Key**
3. Name it: `n8n-youtube-shorts`
4. **Copy the key** - it only shows once!

```
Example: sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

⚠️ **Save this key somewhere safe!**

---

## Step 2: Setup n8n (5 minutes)

### Option A: Docker (Recommended)

```bash
# 1. Clone the repository
git clone https://github.com/cupitebet/n8nVideoPendek.git
cd n8nVideoPendek

# 2. Create .env file
cp config/environment-variables-cometapi.env.example .env

# 3. Edit .env and add your API key
nano .env
# Change: COMETAPI_KEY=your-key-here

# 4. Start n8n with Docker
docker run -d \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  --env-file .env \
  n8nio/n8n

# 5. Wait 30 seconds for startup
sleep 30
```

### Option B: npm

```bash
# 1. Install n8n globally
npm install -g n8n

# 2. Set environment variable
export COMETAPI_KEY=your-key-here

# 3. Start n8n
n8n start
```

### Verify n8n is Running

1. Open browser
2. Go to: `http://localhost:5678`
3. You should see n8n interface

---

## Step 3: Import Workflows (2 minutes)

### 3.1 Import Main Workflow

1. In n8n, click **Workflows** (top left)
2. Click **Import from File**
3. Navigate to `workflows/youtube-shorts-cometapi.json`
4. Click **Open**
5. Workflow should load with 12 nodes

### 3.2 Import Character Generator (Optional)

1. Click **Workflows** → **Import from File**
2. Select `workflows/character-generator-cometapi.json`
3. Click **Open**

---

## Step 4: Create Your First Video! (5 minutes)

### 4.1 Open Main Workflow

1. Click on **"YouTube Shorts - Blueprint V2 (CometAPI)"** workflow
2. You'll see all 12 nodes connected

### 4.2 Set Your Topic

1. Click on the **"Input: Set Topic"** node (first node)
2. In the right panel, you'll see:
   ```json
   {
     "topic": "AI in Gaming"
   }
   ```
3. Change topic to something you want:
   ```json
   {
     "topic": "Cara AI Mengubah Dunia Marketing"
   }
   ```

### 4.3 Execute Workflow

1. Click **"Execute Workflow"** button (top right)
2. Watch the magic happen! 🎉

**What happens:**
- Node 1: Generates script (~5 seconds)
- Node 2: Parses script
- Nodes 3-6: Generates images for each scene (~10 seconds each)
- Nodes 7-10: Generates videos for each scene (~30-60 seconds each)
- Node 11-12: Aggregates and formats output

**Total time: 2-3 minutes**

### 4.4 Check Results

1. Click on the last node: **"Format Final Output"**
2. Click **"Output"** tab
3. You'll see:
   ```json
   {
     "status": "completed",
     "topic": "Cara AI Mengubah Dunia Marketing",
     "title": "AI Merevolusi Marketing! Wow!",
     "total_scenes": 5,
     "video_urls": [
       "https://...",
       "https://...",
       "https://...",
       "https://...",
       "https://..."
     ]
   }
   ```

### 4.5 Download Videos

1. Copy each URL from `video_urls` array
2. Open in browser
3. Right-click → **Save Video As...**
4. Save all 5 clips

---

## Step 5: Combine Videos (Optional)

### Option A: Manual (Free)

**Using CapCut:**
1. Download [CapCut](https://www.capcut.com/) (free)
2. Import all 5 video clips
3. Drag to timeline in order
4. Add transitions between clips (optional)
5. Add subtitles (auto-caption feature)
6. Add background music
7. Export as 9:16 (Shorts format)

**Using DaVinci Resolve:**
1. Import clips
2. Arrange in timeline
3. Add effects/transitions
4. Export

### Option B: Automated (Advanced)

Use Creatomate (requires separate account):
- Coming soon in future updates

---

## Step 6: Upload to YouTube Shorts!

1. Go to [YouTube Studio](https://studio.youtube.com)
2. Click **Create** → **Upload Video**
3. Select your combined video
4. Set as **Short** (YouTube detects 9:16 ratio)
5. Add title from workflow output
6. Add description and tags
7. Publish!

---

## 🎉 Congratulations!

You've just created your first AI-generated YouTube Short!

---

## Troubleshooting

### Workflow Fails at "The Director" Node

**Error:** "Invalid API key"

**Solution:**
```bash
# Check .env file
cat .env | grep COMETAPI_KEY

# Should show: COMETAPI_KEY=sk-xxx...
# If empty or wrong, edit:
nano .env

# Then restart n8n:
docker restart n8n
# or
n8n stop && n8n start
```

### Workflow Fails at "Generate Images" Node

**Error:** "Insufficient credits"

**Solution:**
1. Go to [CometAPI Dashboard](https://www.cometapi.com/dashboard)
2. Check balance
3. Top up credits if needed

### Video Generation Takes Too Long

**Issue:** Veo 3 is taking >2 minutes per clip

**Solution:**
- This is normal! Veo 3 typically takes 30-90 seconds per clip
- Total workflow time: 2-5 minutes for 5 clips
- Be patient ☕

### Videos Don't Match Character Description

**Issue:** Character looks different in each scene

**Solution:**
1. Use the **Character Generator** workflow first
2. Create a character sheet
3. Reuse the character image URL in prompts

See: [Character Consistency Guide](CHARACTER-CONSISTENCY.md)

---

## Next Steps

### Level Up Your Skills

**Now that you've created your first video:**

1. ✅ Read [Character Consistency Guide](CHARACTER-CONSISTENCY.md)
   - Master creating consistent characters
   - Learn advanced prompt engineering

2. ✅ Study the prompts
   - Open `prompts/director-system-prompt.txt`
   - Understand Conflict Arc structure
   - Customize for your niche

3. ✅ Experiment with topics
   - Try different niches
   - Test different video lengths
   - A/B test titles

4. ✅ Create 10 videos
   - Practice makes perfect
   - Learn what works
   - Build your content library

### Optimize Your Workflow

**Cost Optimization:**
- Reuse character sheets
- Use shorter clips (3s instead of 5s)
- Reduce scenes per video (3-4 instead of 5)

**Quality Optimization:**
- Study high-performing Shorts
- Improve prompt specificity
- Add better sound effect descriptions
- Use consistent art style

**Speed Optimization:**
- Batch process multiple topics
- Create character library upfront
- Use templates for common topics

---

## Common Customizations

### Change Video Length

Edit "duration" in **"Generate Video (Veo 3)"** node:

```json
{
  "duration": 3  // Change from 5 to 3 seconds
}
```

### Change Aspect Ratio

For Instagram Reels or TikTok (same as Shorts):
```json
{
  "aspect_ratio": "9:16"  // Already default
}
```

For YouTube regular videos:
```json
{
  "aspect_ratio": "16:9"
}
```

For Instagram Posts:
```json
{
  "aspect_ratio": "1:1"
}
```

### Change Script Temperature

In **"The Director"** node, change temperature:

```json
{
  "temperature": 0.9  // More creative (default: 0.7)
}
```

Or:

```json
{
  "temperature": 0.3  // More conservative
}
```

---

## Cost Estimate for First Video

Per video (5 scenes):
- Gemini 1.5 Pro (script): ~$0.001
- Gemini 2.5 Flash Image (5 images): ~$0.010
- Veo 3 (5 clips × 5s): ~$0.50-1.00
- **Total**: ~$0.51-1.01

**Very affordable for testing!** 🎉

---

## Resources

### Documentation
- [CometAPI Setup Guide](COMETAPI-SETUP.md) - Detailed setup
- [Character Consistency Guide](CHARACTER-CONSISTENCY.md) - Master consistency
- [Workflows README](../workflows/README.md) - Workflow details

### Examples
- [Sample Script Output](../examples/sample-script-output.json)
- [Sample Character Sheet](../examples/sample-character-sheet.json)

### External
- [CometAPI Docs](https://apidoc.cometapi.com) - Official API docs
- [n8n Docs](https://docs.n8n.io/) - n8n help
- [Veo 3 Guide](https://apidoc.cometapi.com/video/veo3/video-create.md) - Veo 3 details

---

## Need Help?

- 🐛 [GitHub Issues](https://github.com/cupitebet/n8nVideoPendek/issues)
- 💬 [CometAPI Help Center](https://apidoc.cometapi.com/help-center.md)
- 📖 [Full Documentation](README.md)

---

**Happy Creating! 🎬**

*You're now ready to create unlimited YouTube Shorts with AI!*
