# Manual Video Generation Workflow (Free Testing Solution)

Panduan untuk testing workflow dengan manual video creation sebelum invest ke automated solution.

---

## Overview

**Use Case:** Testing social media publishing tanpa invest dulu ke Pictory/paid services

**Process:**
1. Workflow generates script ✅
2. You manually create video (Canva, CapCut, etc) ⚠️
3. Upload video to cloud storage ✅
4. Workflow auto-uploads ke semua platforms ✅

**50% automated, 50% manual**

---

## Tools Needed (All Free!)

### Video Creation:
- **Canva** (free tier) - Template-based, easy
- **CapCut** (completely free) - More advanced editing
- **Clipchamp** (Windows built-in) - Simple editor

### Stock Assets:
- **Pexels Videos** - Free stock footage
- **Unsplash** - Free images
- **Pixabay** - Free videos & music

### Voice-Over (Optional):
- **ElevenLabs** (free tier: 10,000 chars/month)
- **Google Text-to-Speech** (free)
- **Your own voice** (best for authenticity!)

---

## Workflow Integration

### Current Workflow Output

Workflow sudah menghasilkan untuk Anda:

```json
{
  "video_script": {
    "hook": "Guys, ada AI yang bisa audit smart contract!",
    "scenes": [
      {
        "duration": "3-20s",
        "narration": "ChainGPT ini basically ChatGPT untuk blockchain. Dia bisa bantu developer bikin smart contract, generate NFT pake AI...",
        "visual_cue": "Show ChainGPT interface, logo, AI visualization"
      },
      {
        "duration": "20-40s",
        "narration": "Yang bikin menarik, backing-nya gila sih. DWF Labs, Polygon Ventures...",
        "visual_cue": "Show logos DWF Labs, Polygon, partnerships"
      }
    ],
    "cta": {
      "narration": "Kalian gimana? Bullish apa bearish?",
      "duration": "55-60s",
      "visual_cue": "CTA text: 'COMMENT BULLISH!'"
    }
  },
  "social_caption": {
    "caption": "🤖 AI yang bisa audit smart contract? ChainGPT backed by DWF Labs!...",
    "hashtags": ["#ChainGPT", "#AICrypto", "#Web3"]
  }
}
```

**Simpan ke file atau Google Sheet untuk reference!**

---

## Method 1: Canva (Easiest, Recommended for Beginners)

### Step 1: Setup Canva

1. **Go to Canva.com**
2. **Sign up free account**
3. **Search template:** "Instagram Reels" atau "TikTok Video"
4. **Filter:**
   - Aspect ratio: 9:16 (vertical)
   - Style: Modern, Tech, Crypto

### Step 2: Create Video

1. **Select template yang tech/crypto theme**

2. **Scene 1 - Hook (0-3s):**
   - Text: "Guys, ada AI yang bisa audit smart contract!"
   - Background: Gradient atau tech animation
   - Font: Bold, eye-catching

3. **Scene 2-4 - Content (3-55s):**
   - Follow script scenes
   - Add text overlays untuk key points
   - Insert relevant images/icons:
     - ChainGPT logo (search Google, download)
     - DWF Labs logo
     - Blockchain visualizations

4. **Scene 5 - CTA (55-60s):**
   - Big text: "Comment BULLISH!"
   - Like/subscribe icons
   - Your branding

### Step 3: Add Voice-Over

**Option A: Record yourself**
1. Canva > Upload > Record
2. Read script naturally
3. Best for authenticity!

**Option B: Use AI voice**
1. Go to ElevenLabs.io
2. Paste narration text
3. Generate voice
4. Download MP4
5. Upload to Canva > Audio

### Step 4: Add Music

1. Canva > Audio
2. Search: "Upbeat" atau "Technology"
3. Add to timeline
4. Lower volume: 20-30%

### Step 5: Export

1. Click "Share" > "Download"
2. File type: **MP4 Video**
3. Quality: **1080p**
4. Download

**Done! You have MP4 file ready to upload.**

---

## Method 2: CapCut (More Advanced)

### Step 1: Setup

1. **Download CapCut**
   - Desktop: https://www.capcut.com/
   - Mobile: App Store / Play Store

2. **Create New Project**
   - Ratio: 9:16
   - FPS: 30

### Step 2: Get Stock Footage

1. **Go to Pexels Videos**
   - URL: https://www.pexels.com/videos/
   - Search: "blockchain", "technology", "crypto", "AI"
   - Download 5-7 clips (free, no attribution needed)

2. **Import to CapCut**
   - Drag clips to timeline

### Step 3: Edit Scenes

**Follow script timing:**

```
00:00 - 00:03  Hook scene
00:03 - 00:20  Scene 1 (ChainGPT explanation)
00:20 - 00:40  Scene 2 (Backing/partnerships)
00:40 - 00:55  Scene 3 (Verdict)
00:55 - 01:00  CTA
```

**For each scene:**
1. Add stock footage clip
2. Add text overlay (narration key points)
3. Add transitions between scenes

### Step 4: Add Captions

1. CapCut > Text > Auto Captions
2. Select language: Indonesian
3. Generate
4. Style: Choose modern preset
5. Position: Center or bottom

### Step 5: Voice-Over

**Option A: Record in CapCut**
1. Audio > Record
2. Read script
3. Adjust timing

**Option B: Upload pre-recorded**
1. Record dengan phone
2. Import to CapCut
3. Sync with video

### Step 6: Add Music

1. Audio > Music
2. CapCut has free music library
3. Or upload your own
4. Volume: 20-30%

### Step 7: Export

1. Export > 1080p > 30FPS
2. Wait for rendering
3. Save MP4 file

---

## Method 3: Quick & Dirty (Fastest - 5 Minutes)

For urgent posts atau testing:

1. **Open Canva on phone**
2. **Instagram Reels template**
3. **Replace text dengan script key points**
4. **Record voice-over langsung**
5. **Export**
6. **Done!**

Quality: ⭐⭐⭐ (acceptable)
Speed: ⚡⚡⚡⚡⚡ (super fast)

---

## Upload to Workflow

### Step 1: Save Video File

Save dengan naming convention:
```
{project-name}-{date}.mp4

Example:
chaingpt-20251120.mp4
```

### Step 2: Upload to Cloud Storage

**Option A: Google Drive (Simplest)**

1. Upload video to Google Drive
2. Right-click > Get Link
3. Change permission: "Anyone with link can view"
4. Copy link
5. Convert ke direct download link:
   ```
   https://drive.google.com/uc?export=download&id=FILE_ID
   ```

**Option B: AWS S3**

```bash
aws s3 cp chaingpt-20251120.mp4 s3://my-bucket/videos/ --acl public-read
```

Get URL:
```
https://my-bucket.s3.amazonaws.com/videos/chaingpt-20251120.mp4
```

**Option C: Cloudinary**

1. Go to Cloudinary dashboard
2. Upload video
3. Copy public URL

### Step 3: Add to n8n Workflow

**Add "Set Video URL" node setelah content generation:**

```json
{
  "parameters": {
    "values": {
      "string": [
        {
          "name": "video_url",
          "value": "https://your-storage.com/videos/chaingpt-20251120.mp4"
        }
      ]
    },
    "options": {}
  },
  "name": "Set Manual Video URL",
  "type": "n8n-nodes-base.set",
  "typeVersion": 3,
  "position": [2200, 300]
}
```

**Then connect ke social media upload nodes!**

---

## Daily Workflow (Manual Video Creation)

**Morning (15-20 minutes):**

1. **Run n8n workflow manual trigger**
   - Generates script, caption, hashtags
   - Archive to Google Sheets

2. **Export script** dari Google Sheets
   - Copy narration untuk semua scenes

3. **Create video** dengan Canva/CapCut
   - Follow script scenes
   - Add voice-over
   - Export MP4

4. **Upload video** to cloud storage
   - Get public URL

**Afternoon (5 minutes):**

5. **Add video URL** ke n8n Set node
6. **Run workflow** untuk auto-publish
7. **Video posted** ke semua platforms!

**Total time:** 20-25 minutes/day untuk 1 video

---

## Tips untuk Faster Manual Creation

### 1. Create Template Library

Buat 3-5 Canva templates:
- **Bullish template** (green theme)
- **Bearish template** (red theme)
- **Neutral template** (blue theme)
- **Analysis template** (professional)
- **News template** (urgent vibe)

**Reuse every day, only change:**
- Text content
- Project name
- Voice-over

### 2. Batch Recording

Record voice-overs untuk 3-5 videos sekaligus:
- Monday: Record untuk whole week
- Save audio files
- Use throughout week

### 3. Stock Footage Library

Download 50-100 tech/crypto clips:
- Save to folder
- Categorize: AI, Blockchain, DeFi, NFT, etc
- Reuse across videos

### 4. Automation Scripts

If technical, create scripts:
```bash
# Auto-upload to S3
./upload-video.sh chaingpt-20251120.mp4

# Auto-trigger n8n workflow
curl -X POST https://your-n8n.com/webhook/publish-video \
  -d '{"video_url": "..."}'
```

---

## Quality Checklist

Before uploading video:

- [ ] Aspect ratio 9:16 (vertical)
- [ ] Resolution 1080x1920
- [ ] Duration 15-60 seconds
- [ ] Audio clear (no background noise)
- [ ] Captions readable
- [ ] Hook dalam 3 detik pertama
- [ ] CTA jelas di akhir
- [ ] File size < 100MB
- [ ] Format MP4

---

## Transition to Automation

**When ready untuk full automation:**

1. Test manual workflow selama 1-2 minggu
2. Analyze:
   - Which content performs best?
   - Average creation time?
   - Worth investing di automation?

3. If YES → Invest di Pictory ($29/month)
4. If NO → Continue manual (or improve efficiency)

**Break-even calculation:**
```
Manual: 20 min/day × 30 days = 10 hours/month
Your time value: $X/hour

If 10 × $X > $29 → Automation worth it!
```

---

## Alternative Free Tools

### Video Editors:
- **DaVinci Resolve** (professional, free)
- **Shotcut** (open-source)
- **OpenShot** (simple, free)

### Stock Assets:
- **Mixkit** (videos)
- **Videvo** (videos + music)
- **Freepik** (vectors, images)

### Voice:
- **Murf.ai** (free tier)
- **Speechify** (free tier)
- **Natural Reader** (free)

---

## Example: 20-Minute Video Creation

**Project:** ChainGPT Analysis

**00:00 - 00:05** - Open Canva, select template
**00:05 - 00:10** - Replace all text dengan script
**00:10 - 00:15** - Record voice-over (or upload pre-recorded)
**00:15 - 00:17** - Add background music
**00:17 - 00:20** - Export video

**Total:** 20 minutes untuk production-ready video!

---

## When to Upgrade to Paid?

**Stick dengan manual if:**
- Posting 1-2 videos/week
- Enjoy video creation process
- Budget-conscious
- High quality bar

**Upgrade to Pictory if:**
- Posting 5-7 videos/week
- Time-constrained
- Want full automation
- Scaling content production

---

**Ready to create your first video? Let's go! 🎬**
