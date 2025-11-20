# Adding Video Generation to Your Existing Workflow

Quick guide untuk add video generation nodes ke workflow `tuyul-digital-web3.json` yang sudah ada.

---

## Current Workflow Status

**✅ Yang sudah ada:**
```
Schedule Trigger
    → Fetch News
    → Filter
    → AI Research
    → AI Content Generation (script, article, caption)
    → Generate Image (thumbnail)
    → Publish to WordPress/Telegram
    → Archive
```

**❌ Yang masih kurang:**
```
Video Generation (dari script ke actual MP4 file)
```

---

## Quick Decision Tree

**Choose your path:**

```
Do you want full automation?
├─ YES, budget OK ($29/mo) ───→ Use Pictory.ai (Option 1)
│
├─ YES, but FREE ────────────→ Use FFmpeg/Templates (Option 2)
│
└─ NO, manual OK for now ────→ Manual Upload (Option 3)
```

---

## Option 1: Pictory.ai Integration (Recommended)

**Best for:** Full automation, professional quality

### Quick Setup:

1. **Sign up Pictory.ai** ($29/month)
   - Get API key

2. **Add n8n Credential**
   - Type: HTTP Header Auth
   - Name: `Authorization`
   - Value: `Bearer YOUR_API_KEY`

3. **Add these nodes AFTER "Parse Generated Content":**

```
[Parse Generated Content]
    ↓
[NEW: Prepare Pictory Script]  ← Convert AI script to Pictory format
    ↓
[NEW: Create Pictory Video]   ← Call Pictory API
    ↓
[NEW: Wait 2 minutes]          ← Wait for processing
    ↓
[NEW: Download Video]          ← Get MP4 file
    ↓
[Upload to Cloud Storage]      ← S3/Cloudinary
    ↓
[Multi-Platform Upload]        ← YouTube/TikTok/Instagram/Facebook
```

**Full guide:** [VIDEO-GENERATION-PICTORY.md](VIDEO-GENERATION-PICTORY.md)

---

## Option 2: Manual Upload (Free Testing)

**Best for:** Testing before investing

### Quick Setup:

1. **Workflow generates script** (already working ✅)

2. **Export script dari Google Sheets**

3. **Create video manually:**
   - Canva (5-10 min)
   - CapCut (10-20 min)
   - Your choice

4. **Upload to cloud storage:**
   - Google Drive (get public link)
   - AWS S3
   - Cloudinary

5. **Add "Set Video URL" node:**

```json
{
  "parameters": {
    "values": {
      "string": [
        {
          "name": "video_url",
          "value": "YOUR_VIDEO_PUBLIC_URL"
        }
      ]
    }
  },
  "name": "Set Manual Video URL",
  "type": "n8n-nodes-base.set",
  "position": [2200, 300]
}
```

6. **Connect to social media upload nodes** (already have the guides!)

**Full guide:** [VIDEO-GENERATION-MANUAL.md](VIDEO-GENERATION-MANUAL.md)

---

## Complete Updated Workflow (with Pictory)

```
┌─────────────────────┐
│ Schedule Trigger    │ Daily 8AM
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Fetch Crypto News   │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Filter Topics       │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ AI Research         │ GPT-4o-mini
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Generate Content    │ Article + Script + Caption
└──────────┬──────────┘
           │
           ├──────────────────┬─────────────────┐
           ▼                  ▼                 ▼
    ┌──────────┐      ┌──────────────┐  ┌──────────────┐
    │ Generate │      │ Prepare      │  │ Publish      │
    │ Image    │      │ Pictory      │  │ WordPress    │
    │ (Flux)   │      │ Script       │  │ Article      │
    └──────────┘      └──────┬───────┘  └──────────────┘
                             │
                             ▼
                      ┌──────────────┐
                      │ Create Video │ Pictory API
                      │ (Pictory)    │
                      └──────┬───────┘
                             │
                             ▼
                      ┌──────────────┐
                      │ Wait + Check │ 2-5 minutes
                      │ Status       │
                      └──────┬───────┘
                             │
                             ▼
                      ┌──────────────┐
                      │ Download     │ MP4 file
                      │ Video        │
                      └──────┬───────┘
                             │
                             ▼
                      ┌──────────────┐
                      │ Upload to    │ S3/Cloudinary
                      │ Cloud        │
                      └──────┬───────┘
                             │
           ┌─────────────────┴─────────────────┐
           │                                   │
           ▼                                   ▼
    ┌──────────────┐                   ┌──────────────┐
    │ Multi-Platform Upload             │ Archive      │
    │                                   │ to Sheets    │
    ├─→ YouTube Shorts                  └──────────────┘
    ├─→ TikTok
    ├─→ Instagram Reels
    ├─→ Facebook Reels
    └─→ Telegram
           │
           ▼
    ┌──────────────┐
    │ Send Admin   │
    │ Notification │
    └──────────────┘
```

---

## Node Positions (for Pictory Integration)

Add nodes di posisi ini:

| Node Name | X Position | Y Position |
|-----------|------------|------------|
| Prepare Pictory Script | 2100 | 300 |
| Create Pictory Video | 2300 | 300 |
| Wait for Processing | 2450 | 300 |
| Check Video Status | 2600 | 300 |
| Download Video | 2800 | 300 |
| Upload to Cloud | 3000 | 300 |
| (Then existing social media nodes) | 3200+ | varies |

---

## Testing Strategy

### Phase 1: Test Video Generation Only

1. **Disable semua nodes** except:
   - Manual Trigger
   - Set (with sample script)
   - Prepare Pictory Script
   - Create Pictory Video
   - Download Video

2. **Sample input:**
```json
{
  "generated_content": {
    "video_script": {
      "hook": "Test Pictory integration!",
      "scenes": [{
        "narration": "This is a test video.",
        "duration": "5s"
      }]
    }
  },
  "research": {
    "project_name": "Test",
    "category": "Technology"
  }
}
```

3. **Execute dan verify:**
   - Pictory video created?
   - Download successful?
   - MP4 file valid?

### Phase 2: Test Cloud Upload

1. **Enable Upload to Cloud node**
2. **Execute**
3. **Verify public URL accessible**

### Phase 3: Test Social Media Upload

1. **Enable ONE platform** (e.g., Telegram)
2. **Execute end-to-end**
3. **Verify video posted**
4. **Then enable other platforms**

### Phase 4: Full Automation

1. **Enable Schedule Trigger**
2. **Set to run once/day**
3. **Monitor first 3 runs**
4. **Adjust as needed**

---

## Configuration Variables

Add these to your workflow config:

```json
{
  "video_generation": {
    "enabled": true,
    "method": "pictory",
    "pictory_api_key": "YOUR_KEY",
    "fallback_to_manual": true
  },
  "video_settings": {
    "aspect_ratio": "9:16",
    "duration": 60,
    "voice_enabled": true,
    "voice_model": "en-US-Neural2-D",
    "captions_enabled": true,
    "music_enabled": true,
    "music_volume": 0.3
  },
  "cloud_storage": {
    "provider": "s3",
    "bucket": "my-shorts-bucket",
    "public_url_prefix": "https://my-bucket.s3.amazonaws.com/"
  }
}
```

---

## Error Handling

### Common Issues:

**Pictory: "API key invalid"**
- Check credential setup
- Verify API key di Pictory dashboard

**Pictory: "Processing timeout"**
- Increase wait time (2 → 5 minutes)
- Check Pictory status page

**Download: "File not found"**
- Verify video processing completed
- Check download URL format

**Upload: "Access denied"**
- Verify cloud storage credentials
- Check bucket permissions

---

## Cost Estimation (with Pictory)

### Monthly Costs:

**Content Generation:**
- OpenAI GPT-4o-mini: $10-15
- NewsAPI: $0 (free tier)

**Video Generation:**
- Pictory Standard: $29 (30 videos)
- Pictory Premium: $59 (60 videos)

**Storage & Delivery:**
- AWS S3: $2-5
- Cloudinary: $0 (free tier OK)

**Social Media APIs:**
- All free (within limits)

**Total (Standard):**
- $41-49/month untuk 1 video/day
- ~$1.37-1.63 per video

**Total (Premium):**
- $71-79/month untuk 2 videos/day
- ~$1.18-1.32 per video

---

## Alternative: Hybrid Approach

**Weekdays:** Pictory automation (5 videos)
**Weekends:** Manual creation (higher quality, 2 videos)

Benefits:
- Save Pictory quota
- Mix automated + artisanal content
- Best of both worlds

---

## Monitoring Dashboard

Track these metrics:

```javascript
{
  "video_generation": {
    "success_rate": "95%",
    "avg_processing_time": "3.2 min",
    "failed_count": 2,
    "retry_count": 5
  },
  "upload_stats": {
    "youtube_success": 28,
    "tiktok_success": 30,
    "instagram_success": 29,
    "facebook_success": 30
  },
  "costs": {
    "pictory_videos_used": "28/30",
    "storage_gb": 4.2,
    "total_month": "$42.50"
  }
}
```

Setup Google Sheets dashboard untuk daily tracking!

---

## Next Steps

**Choose your path:**

1. **Full Automation Path:**
   - [ ] Sign up Pictory
   - [ ] Add Pictory nodes
   - [ ] Test video generation
   - [ ] Connect to social media
   - [ ] Activate schedule

2. **Manual Path:**
   - [ ] Run workflow untuk script
   - [ ] Create video di Canva/CapCut
   - [ ] Upload to cloud
   - [ ] Add URL to workflow
   - [ ] Publish to platforms

3. **Hybrid Path:**
   - [ ] Setup Pictory untuk weekdays
   - [ ] Manual creation untuk weekends
   - [ ] Best of both worlds

---

## Support Resources

**Video Generation:**
- [Pictory Guide](VIDEO-GENERATION-PICTORY.md)
- [Manual Guide](VIDEO-GENERATION-MANUAL.md)
- [Options Comparison](VIDEO-GENERATION-OPTIONS.md)

**Social Media:**
- [YouTube Setup](API-SETUP-YOUTUBE.md)
- [TikTok Setup](API-SETUP-TIKTOK.md)
- [Instagram Setup](API-SETUP-INSTAGRAM.md)
- [Facebook Setup](API-SETUP-FACEBOOK.md)

**Workflow:**
- [Social Media Nodes](WORKFLOW-SOCIAL-MEDIA-NODES.md)
- [Complete Guide](SOCIAL-MEDIA-COMPLETE-GUIDE.md)

---

**Ready to add video generation? Pick your method and let's go! 🎬**
