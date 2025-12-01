# Today's Mission: Create Your First Video (Manual Test)

**Goal:** Dari workflow → script → video → published di Telegram

**Time:** 30-45 menit total

**Cost:** $0 (semua free!)

---

## 📋 Checklist Hari Ini

- [ ] Step 1: Run workflow, dapat script (5 min)
- [ ] Step 2: Create video di Canva (15 min)
- [ ] Step 3: Upload video to storage (5 min)
- [ ] Step 4: Add URL ke workflow (2 min)
- [ ] Step 5: Publish ke Telegram (3 min)
- [ ] Step 6: Celebrate! 🎉

---

## Step 1: Run Workflow untuk Dapat Script (5 menit)

### 1.1 Buka n8n Workflow

```
http://localhost:5678
```

atau URL n8n Anda

### 1.2 Open Workflow "Tuyul Digital Web3"

Klik workflow yang sudah di-import

### 1.3 Disable Nodes yang Belum Siap

**Untuk testing hari ini, disable nodes ini:**
- Generate Image with Flux AI (opsional)
- Upload Image to WordPress
- Publish Article to WordPress
- Publish to Telegram (yang existing)
- Archive to Google Sheets (opsional)

**Cara disable:**
- Right-click node → Disable

### 1.4 Execute Workflow

**Klik tombol:** "Execute Workflow" (atau test dengan manual trigger)

### 1.5 Cek Output di "Parse Generated Content"

Klik node "Parse Generated Content" dan lihat output.

**Cari field:**
```json
{
  "generated_content": {
    "video_script": {
      "hook": "...",
      "scenes": [...],
      "cta": {...}
    },
    "social_caption": {
      "caption": "...",
      "hashtags": [...]
    }
  },
  "research": {
    "project_name": "...",
    "category": "..."
  }
}
```

### 1.6 Copy Script ke Text File

**Buat file:** `today-script.txt`

**Copy semua:**
1. Hook
2. Scene 1 narration
3. Scene 2 narration
4. Scene 3 narration
5. CTA narration
6. Caption & hashtags

**Contoh:**
```
PROJECT: ChainGPT
CATEGORY: AI

--- HOOK (0-3s) ---
Guys, ada AI yang bisa audit smart contract otomatis!

--- SCENE 1 (3-20s) ---
ChainGPT ini basically ChatGPT untuk blockchain. Dia bisa bantu developer bikin smart contract, generate NFT pake AI, analisis trading, sampe audit code otomatis.

Visual: Show ChainGPT interface, logo, AI visualization

--- SCENE 2 (20-40s) ---
Yang bikin menarik, backing-nya gila sih. DWF Labs, Polygon Ventures, Crypto.com Capital. Partnership sama Trust Wallet, BNB Chain, bahkan Google Cloud.

Visual: Show logos DWF Labs, Polygon, partnerships

--- SCENE 3 (40-55s) ---
Narrative AI + Blockchain lagi hot banget 2024 ini. Risk? Jelas ada. Tapi dengan backing segini kuat dan product yang udah jalan, ini BULLISH untuk long-term hold.

Visual: Chart, Risk vs Opportunity

--- CTA (55-60s) ---
Kalian gimana? Bullish apa bearish? Comment 'BULLISH' kalau tertarik!

Visual: CTA text

--- CAPTION ---
🤖 AI yang bisa audit smart contract? ChainGPT backed by DWF Labs!
[full caption...]

--- HASHTAGS ---
#ChainGPT #AICrypto #Web3 ...
```

**✅ Step 1 Done!** Script ready untuk video creation.

---

## Step 2: Create Video di Canva (15 menit)

### 2.1 Setup Canva Account

1. **Go to:** https://canva.com
2. **Sign up** free account (jika belum)
3. **Verify** email

### 2.2 Create New Video Project

1. **Klik:** "Create a design"
2. **Search:** "Instagram Reel" atau "TikTok Video"
3. **Pilih:** Instagram Reel (1080 x 1920 px)

### 2.3 Choose Template (or Start Blank)

**Recommended untuk pemula:**
- Search template: "Tech" atau "Crypto" atau "Modern"
- Filter: Video templates only
- Pilih yang vertical (9:16)
- Pilih yang ada animated elements

**Atau start dari blank jika mau full control**

### 2.4 Setup Timeline (60 seconds)

**Di Canva timeline, set total duration:** 60 seconds

### 2.5 Create Scene 1 - HOOK (0-3 seconds)

**Page 1 in Canva:**

**Background:**
- Gradient (dark blue → purple) atau
- Upload gambar related (blockchain visual)

**Text:**
- **Large, bold text:** "AI yang bisa audit smart contract?"
- Font: Impact atau Montserrat Bold
- Size: 80-100pt
- Color: White atau Yellow (eye-catching)
- Animation: Pop atau Fade in

**Atau simpler:**
- Just your hook text with emoji
- "Guys, ada AI yang bisa audit smart contract! 🤖"

**Duration:** 3 seconds

### 2.6 Create Scene 2 - Content Part 1 (3-20 seconds)

**Page 2 in Canva:**

**Background:**
- Tech/blockchain image dari Elements
- Search Canva: "blockchain", "technology", "AI"
- Atau use stock photo

**Text overlay (key points):**
```
ChainGPT = ChatGPT for Blockchain

✅ Smart Contract Development
✅ NFT Generator
✅ Trading Analysis
✅ Code Auditor
```

**Font:** Smaller, readable
**Duration:** 17 seconds (total so far: 20s)

### 2.7 Create Scene 3 - Content Part 2 (20-40 seconds)

**Page 3 in Canva:**

**Background:**
- Partnership/investor theme

**Text/Elements:**
```
Backed by:
💎 DWF Labs
🟣 Polygon Ventures
💳 Crypto.com Capital

Partnership:
🔗 Trust Wallet
🔗 BNB Chain
☁️ Google Cloud
```

**Use logos if you can find them** (search Canva elements)

**Duration:** 20 seconds (total: 40s)

### 2.8 Create Scene 4 - Verdict (40-55 seconds)

**Page 4 in Canva:**

**Background:**
- Chart visual atau gradient

**Text:**
```
Verdict: BULLISH 🚀

✅ Strong backers
✅ Real product (live!)
✅ Trending narrative
⚠️ Risks manageable
```

**Duration:** 15 seconds (total: 55s)

### 2.9 Create Scene 5 - CTA (55-60 seconds)

**Page 5 in Canva:**

**Big centered text:**
```
Comment "BULLISH"
if you're interested! 💬

👍 Like | 📢 Share | ➕ Follow
```

**Background:** Eye-catching color
**Duration:** 5 seconds

### 2.10 Add Transitions

**Between each page:**
- Canva → Transitions
- Choose: "Dissolve" atau "Slide"
- Keep it simple

### 2.11 Add Background Music

**Canva → Audio → Music:**
1. Search: "Upbeat" atau "Tech" atau "Corporate"
2. Choose track
3. Add to timeline
4. **Important:** Lower volume to 20-30%
5. Trim to exactly 60 seconds

### 2.12 Record Voice-Over

**Option A: Record in Canva**
1. Click "Record" button in toolbar
2. Read your script naturally
3. Pace yourself (60 seconds total)
4. Re-record if needed

**Option B: Skip voice for first test**
- Text overlays are enough untuk testing
- Musik saja sudah OK
- Focus on completing workflow first

### 2.13 Review Video

**Click Play button:**
- Check timing (exactly 60s or less)
- Text readable?
- Transitions smooth?
- Music not too loud?

### 2.14 Export Video

1. **Click:** "Share" → "Download"
2. **File type:** MP4 Video
3. **Quality:** 1080p (Full HD)
4. **Wait** untuk download (30 seconds - 2 minutes)

**Save as:** `chaingpt-test-20251120.mp4`

**✅ Step 2 Done!** Video file ready!

---

## Step 3: Upload Video to Storage (5 menit)

**Pilih salah satu metode:**

### Option A: Google Drive (Easiest for Testing)

1. **Upload to Google Drive:**
   - Drag video file to Drive
   - Wait untuk upload complete

2. **Get shareable link:**
   - Right-click video → Share → Get link
   - Change to: "Anyone with the link"
   - Copy link

3. **Convert ke direct link:**

   **Original link:**
   ```
   https://drive.google.com/file/d/1ABC123xyz/view?usp=sharing
   ```

   **Direct download link:**
   ```
   https://drive.google.com/uc?export=download&id=1ABC123xyz
   ```

   Extract `1ABC123xyz` (the file ID) dari URL

4. **Test link:**
   - Paste di browser
   - Should start download or show video

**Save this URL!** You need it untuk n8n.

---

### Option B: Cloudinary (Better for Production)

1. **Sign up Cloudinary:**
   - https://cloudinary.com/users/register/free
   - Free tier: 25GB

2. **Upload video:**
   - Dashboard → Media Library → Upload
   - Drag video file

3. **Get public URL:**
   - Click uploaded video
   - Copy "URL" (should be like `https://res.cloudinary.com/xxx/video/upload/vxxx/video.mp4`)

4. **Test URL** di browser

---

### Option C: File.io (Quick Temporary)

**For testing only (link expires after 1 download!):**

```bash
curl -F "file=@chaingpt-test-20251120.mp4" https://file.io
```

Returns:
```json
{
  "success": true,
  "link": "https://file.io/abc123",
  "expiry": "14 days"
}
```

**Better:** Use Drive atau Cloudinary untuk persistent link.

---

**✅ Step 3 Done!** Video accessible via URL.

---

## Step 4: Add Video URL ke Workflow (2 menit)

### 4.1 Open n8n Workflow

Go back to your workflow

### 4.2 Add "Set Video URL" Node

**After "Parse Generated Content" node:**

1. **Add node:** Code → Set
2. **Name it:** "Set Manual Video URL"
3. **Configure:**

```
Values to Set:
- Name: video_url
  Value: YOUR_VIDEO_URL_HERE
```

**Paste URL dari Step 3!**

### 4.3 Save Workflow

Click "Save" di kanan atas

**✅ Step 4 Done!** Video URL ready di workflow.

---

## Step 5: Publish to Telegram (3 menit)

**Untuk test pertama, kita publish ke Telegram dulu** (paling simple!)

### 5.1 Setup Telegram Bot (Jika Belum)

**Skip jika sudah ada bot:**

1. **Open Telegram app**
2. **Search:** @BotFather
3. **Send:** `/newbot`
4. **Follow** instructions
5. **Copy** bot token yang diberikan

### 5.2 Get Chat ID

**Create channel atau use your own chat:**

1. **Add bot** ke channel/group
2. **Send test message**
3. **Get updates:**
   ```
   https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates
   ```
4. **Find chat ID** di response JSON

### 5.3 Add Telegram Node to Workflow

**After "Set Manual Video URL" node:**

1. **Add node:** Telegram → Send Video
2. **Configure:**
   - **Credential:** Add your bot token
   - **Chat ID:** Your channel/chat ID
   - **Video URL:** `={{ $json.video_url }}`
   - **Caption:** `={{ $json.generated_content.social_caption.caption }}`

3. **Save**

### 5.4 Test Execute

**Execute workflow:**
1. Click "Execute Workflow"
2. Wait...
3. Check Telegram channel!

**Video should appear di Telegram dengan caption!** 🎉

---

## Step 6: Celebrate! 🎉

**If video muncul di Telegram:**

✅ Workflow generates script
✅ You create video
✅ Video uploaded to cloud
✅ Workflow publishes to platform

**YOU DID IT!** Full workflow working end-to-end! 🚀

---

## What You Learned Today

1. ✅ **Script generation** dari AI (automated)
2. ✅ **Video creation** dengan Canva (manual - 15 min)
3. ✅ **Cloud upload** workflow
4. ✅ **Social media publishing** (automated)

**Time breakdown:**
- Workflow script: 5 min (automated)
- Video creation: 15 min (manual)
- Upload & setup: 10 min
- **Total:** 30 minutes untuk full content piece!

---

## Tomorrow: Add More Platforms

**Now that you know it works, add:**
- YouTube Shorts (follow: `docs/API-SETUP-YOUTUBE.md`)
- Instagram Reels (follow: `docs/API-SETUP-INSTAGRAM.md`)

**One platform at a time!**

---

## Common Issues & Fixes

### Issue: Video too large for Telegram
**Solution:**
- Telegram max: 50MB for bots, 2GB for regular
- Compress video di Canva export settings
- Or use 720p instead of 1080p

### Issue: Video URL not accessible
**Solution:**
- Check link is public (not private)
- Test in incognito browser
- Make sure it's direct link (not preview page)

### Issue: Caption not showing
**Solution:**
- Check variable name correct: `$json.generated_content.social_caption.caption`
- Add manual caption untuk testing

### Issue: Canva video too short/long
**Solution:**
- Check total timeline duration
- Adjust each page duration
- Target: 50-60 seconds (allow buffer)

---

## Next Steps (This Week)

**Day 1 (Today):** ✅ Manual test - Telegram
**Day 2:** Add YouTube Shorts publishing
**Day 3:** Add Instagram Reels publishing
**Day 4:** Add TikTok (if approved) or Facebook
**Day 5:** Optimize video creation (templates, faster workflow)

**Week 2:** Consider Pictory automation if you're creating daily

---

## Pro Tips

### Speed up Canva creation:
1. **Save as template** after first video
2. **Next video:** Duplicate template, change text only
3. **Batch record:** Voice-overs untuk 3-5 videos sekaligus

### Video quality tips:
- **Text:** Large, bold, high contrast
- **Pacing:** 3 seconds per key point minimum
- **Hook:** First 3 seconds crucial!
- **CTA:** Clear dan specific

### Workflow optimization:
- **Save script** to Google Sheets automatically
- **Morning:** Generate 3 scripts
- **Afternoon:** Create 3 videos batch
- **Evening:** Schedule publish

---

## Resources

**Canva help:** https://www.canva.com/help/
**Stock footage:** https://www.pexels.com/videos/
**Free music:** https://www.youtube.com/audiolibrary

---

**Ready to start? Let's create your first video! 🎬**

**Questions? Stuck somewhere? Let me know which step!**
