# Complete Social Media API Setup Guide

Panduan all-in-one untuk setup semua platform social media sekaligus.

---

## Table of Contents

1. [Quick Setup Roadmap](#quick-setup-roadmap)
2. [Setup Priority & Timeline](#setup-priority--timeline)
3. [Credentials Master List](#credentials-master-list)
4. [Platform Comparison](#platform-comparison)
5. [Complete Workflow Example](#complete-workflow-example)
6. [Troubleshooting All Platforms](#troubleshooting-all-platforms)
7. [Cost Analysis](#cost-analysis)
8. [Best Practices](#best-practices)

---

## Quick Setup Roadmap

### Phase 1: Immediate (Can Start Today)
✅ **Telegram** - 5 menit setup
- Easiest to setup
- No approval needed
- Great untuk testing dan notifications

### Phase 2: Quick (1-3 hari)
✅ **YouTube Shorts** - ~30 menit setup + instant approval
- Google Cloud project creation
- OAuth setup
- Good quota limits

✅ **Facebook Reels** - ~45 menit setup
- Facebook Page creation
- Meta App setup
- Page token generation

✅ **Instagram Reels** - ~45 menit (if have Facebook setup)
- Convert to Business account
- Link to Facebook Page
- Use same Meta App

### Phase 3: Medium (1-2 minggu)
⏳ **TikTok** - Setup 30 menit, approval 1-2 minggu
- Developer account application
- App review process
- Content Posting API access request

**Total Time to Full Setup:** 1-2 minggu (waiting for TikTok approval)

---

## Setup Priority & Timeline

### Week 1: Foundation
**Day 1-2:**
- [ ] Setup Telegram (for notifications)
- [ ] Setup YouTube Shorts
- [ ] Test content generation workflow

**Day 3-4:**
- [ ] Apply TikTok Developer account
- [ ] Setup Facebook Page & Meta App
- [ ] Setup Instagram Business account

**Day 5-7:**
- [ ] Complete Instagram Reels setup
- [ ] Complete Facebook Reels setup
- [ ] Test multi-platform publishing

### Week 2: Optimization
**Day 8-14:**
- [ ] Wait for TikTok approval
- [ ] Optimize posting schedule
- [ ] Monitor analytics
- [ ] Adjust content based on performance

---

## Credentials Master List

### Checklist: What You Need

| Platform | Account Type | API Approval | Time to Setup | Credentials Needed |
|----------|-------------|--------------|---------------|-------------------|
| **Telegram** | Bot | None | 5 min | Bot Token |
| **YouTube** | Google Account | None | 30 min | OAuth Client ID & Secret |
| **Instagram** | Business/Creator | None | 45 min | Page Access Token |
| **Facebook** | Page | None | 45 min | Page Access Token |
| **TikTok** | Developer | Required | 1-2 weeks | Client Key & Secret |

### Credentials Storage Template

Create file: `config/credentials-social-media.json` (DON'T commit to git!)

```json
{
  "telegram": {
    "bot_token": "123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11",
    "chat_id": "-1001234567890",
    "admin_id": "987654321"
  },
  "youtube": {
    "client_id": "xxx.apps.googleusercontent.com",
    "client_secret": "GOCSPX-xxx",
    "refresh_token": "1//xxx"
  },
  "instagram": {
    "page_access_token": "EAAGxxx",
    "business_account_id": "17841400008460056"
  },
  "facebook": {
    "page_access_token": "EAAGxxx",
    "page_id": "123456789012345"
  },
  "tiktok": {
    "client_key": "awxxx",
    "client_secret": "xxx",
    "access_token": "act.xxx"
  }
}
```

---

## Platform Comparison

### Upload Limits

| Platform | Videos/Day | File Size | Duration | Aspect Ratio | API Quota |
|----------|-----------|-----------|----------|--------------|-----------|
| **YouTube** | ~6 (quota) | 256GB | <60s | 9:16 | 10,000 units |
| **TikTok** | 10 | 4GB | 3s-10m | 9:16, 16:9, 1:1 | 1000 calls/day |
| **Instagram** | 25 | 1GB | 3-90s | 9:16 | 200/hour |
| **Facebook** | 1500 | 4GB | 3-90s | 9:16 | 200/hour |
| **Telegram** | Unlimited | 2GB | Any | Any | None |

### Optimal Posting Times (WIB)

- **YouTube:** 17:00 - 21:00
- **TikTok:** 18:00 - 22:00
- **Instagram:** 11:00, 19:00 - 21:00
- **Facebook:** 13:00 - 16:00, 19:00 - 21:00
- **Telegram:** Anytime (audience-dependent)

### Content Format Recommendations

| Platform | Ideal Duration | Caption Length | Hashtags | Hook Time |
|----------|---------------|----------------|----------|-----------|
| **YouTube** | 30-60s | 5000 chars | 5-10 | 3 seconds |
| **TikTok** | 15-30s | 2200 chars | 3-5 | 1 second |
| **Instagram** | 15-30s | 2200 chars | 10-20 | 2 seconds |
| **Facebook** | 15-60s | Unlimited | 5-10 | 3 seconds |

---

## Complete Workflow Example

### Full n8n Workflow: Content to All Platforms

```
[Generate Content] → Video Script + Caption
      ↓
[Generate Video] → Text-to-Video or Template-based
      ↓
[Upload to Cloud] → Get public URL
      ↓
[Parallel Upload] ┬→ [YouTube Shorts]
                  ├→ [TikTok]
                  ├→ [Instagram Reels]
                  ├→ [Facebook Reels]
                  └→ [Telegram Channel]
      ↓
[Archive Results] → Google Sheets
      ↓
[Send Notification] → Admin Telegram
```

### Video Generation Options

**Option 1: Pre-made Template**
- Use Canva Pro API
- Renderforest API
- Custom After Effects template

**Option 2: AI Video Generation**
- Pictory.ai
- Synthesia
- D-ID
- Runway ML

**Option 3: Stock Footage + Overlay**
- Pexels/Unsplash API untuk stock videos
- Overlay text dengan ffmpeg
- Add voiceover dengan ElevenLabs

---

## n8n Workflow Nodes Structure

### Master Workflow Breakdown

**1. Content Generation (existing)**
- Schedule Trigger
- Fetch News
- AI Research
- Generate Content

**2. Video Creation (NEW)**
```javascript
Node: Create Video from Script
- Input: Video script dari AI
- Process:
  - Template selection
  - Text overlay
  - Background music
  - Voiceover (optional)
- Output: MP4 file (9:16, 1080x1920)
```

**3. Video Hosting (NEW)**
```javascript
Node: Upload to Cloud Storage
- Options: AWS S3, Cloudinary, Backblaze
- Make publicly accessible
- Get public URL
- Output: video_url
```

**4. Multi-Platform Publishing (NEW)**

**Parallel Execution:**

```javascript
[IF Node] → Check which platforms enabled

Branch 1: YouTube
  → Upload to YouTube
  → Get video ID
  → Archive URL

Branch 2: TikTok
  → Initialize upload
  → Upload video
  → Publish
  → Archive URL

Branch 3: Instagram
  → Create container
  → Wait for processing
  → Publish reel
  → Archive URL

Branch 4: Facebook
  → Upload video
  → Mark as reel
  → Archive URL

Branch 5: Telegram
  → Send video with caption
  → Archive message ID
```

**5. Results Aggregation**
```javascript
Node: Merge Results
- Collect all platform URLs
- Success/failure status
- Engagement metrics (later)
```

**6. Archiving & Notification**
```javascript
Node: Archive to Sheets
- Date/time
- Content topic
- All platform URLs
- Status per platform

Node: Notify Admin
- Success count
- Failed platforms
- Links to posts
```

---

## Video Hosting Solutions

### Option 1: AWS S3 (Recommended)

**Pros:**
- Reliable
- Fast global CDN
- Affordable ($0.023/GB storage)

**Setup:**
```bash
# Create S3 bucket
aws s3 mb s3://my-video-bucket

# Set public access
aws s3api put-bucket-policy --bucket my-video-bucket --policy file://policy.json

# Upload video
aws s3 cp video.mp4 s3://my-video-bucket/videos/video.mp4 --acl public-read
```

**n8n Node:**
```json
{
  "name": "Upload to S3",
  "type": "n8n-nodes-base.aws",
  "parameters": {
    "resource": "s3",
    "operation": "upload"
  }
}
```

### Option 2: Cloudinary

**Pros:**
- Free tier generous
- Built-in video transformations
- Easy API

**Setup:**
```javascript
// n8n HTTP Request node
POST https://api.cloudinary.com/v1_1/{cloud_name}/video/upload
Body: {
  file: [binary data],
  public_id: "video_123",
  folder: "shorts"
}
```

### Option 3: Backblaze B2

**Pros:**
- Cheapest ($0.005/GB)
- No egress fees
- S3-compatible

**Cons:**
- Slower than S3
- Less features

---

## Troubleshooting All Platforms

### Common Cross-Platform Issues

**Issue: Video format not accepted**
```bash
# Convert to compatible format with ffmpeg
ffmpeg -i input.mov \
  -c:v libx264 \
  -c:a aac \
  -b:v 5M \
  -b:a 128k \
  -ar 44100 \
  -r 30 \
  -vf "scale=1080:1920:force_original_aspect_ratio=decrease,pad=1080:1920:(ow-iw)/2:(oh-ih)/2" \
  output.mp4
```

**Issue: Video URL not accessible**
- Check HTTPS (not HTTP)
- Test URL in browser
- Verify CORS settings jika self-hosted
- Check URL tidak redirect

**Issue: Caption/hashtags not working**
- Remove special characters
- Check character limits per platform
- Hashtags: no spaces, alphanumeric only

**Issue: Rate limits hit**
- Implement exponential backoff
- Space out posts (stagger by 5-10 minutes)
- Monitor quota usage
- Use queue system

### Platform-Specific Quick Fixes

**YouTube:**
- Ensure `#Shorts` in title/description
- Check quota at https://console.cloud.google.com/

**TikTok:**
- Verify app approval status
- Re-authenticate if token expired
- Check upload_url not expired (2 hours)

**Instagram:**
- Ensure Business account (not Personal)
- Video must be at public URL
- Wait 10-30s between create & publish

**Facebook:**
- Use Page token (not User token)
- Mark as crosspost for Reels format
- Check Page publish permissions

---

## Cost Analysis

### Free Tier (Recommended for Start)

**Monthly Costs:**
```
OpenAI (GPT-4o-mini): $10-15
NewsAPI: $0 (free tier)
Cloud Storage (S3): $1-3
Video Hosting bandwidth: $2-5
---------------------------
TOTAL: $13-23/month
```

**What You Get:**
- 3 posts per day
- All platforms
- Full automation
- Unlimited archives

### Scaled Tier (100+ videos/month)

```
OpenAI: $30-50
NewsAPI Pro: $449 (optional)
Cloud Storage: $10-20
Video Generation (Pictory): $29
CDN bandwidth: $10-20
---------------------------
TOTAL: $79-110/month (without NewsAPI Pro)
```

### Enterprise Tier (Agency/Multiple Brands)

```
OpenAI: $200+
Video Generation: $99-199
Cloud Infrastructure: $50-100
Multiple TikTok/Meta Apps: Varies
Analytics tools: $50-100
---------------------------
TOTAL: $400-600/month
```

---

## Best Practices

### Content Strategy

**1. Platform-Specific Customization**
```javascript
{
  "youtube": {
    "title": "Full context + #Shorts",
    "description": "Detailed with timestamps"
  },
  "tiktok": {
    "caption": "Short, punchy, trendy"
  },
  "instagram": {
    "caption": "Storytelling + CTA + hashtags"
  },
  "facebook": {
    "caption": "Longer form OK, questions work well"
  }
}
```

**2. Posting Schedule**
```javascript
// Stagger posts for better performance
{
  "youtube": "17:00 WIB",
  "tiktok": "18:15 WIB",   // 15 min after YouTube
  "instagram": "19:00 WIB",
  "facebook": "19:15 WIB"
}
```

**3. Hashtag Strategy**
- Universal: #crypto #web3 #blockchain
- Platform-specific trending tags
- Niche: #cryptoeducation #web3indonesia
- Branded: #TuyulDigitalWeb3

**4. A/B Testing**
- Test posting times
- Different hook variations
- Caption styles
- Thumbnail variations (YouTube)

### Performance Monitoring

**Track These Metrics:**
```javascript
{
  "per_post": {
    "views": "First 24 hours",
    "engagement_rate": "(likes+comments)/views",
    "shares": "Viral potential",
    "watch_time": "Retention",
    "click_through": "If have CTA"
  },
  "per_platform": {
    "best_time": "When highest engagement",
    "best_format": "Which content type performs",
    "growth_rate": "Follower increase"
  }
}
```

### Automation Safety

**Rate Limiting:**
```javascript
// Don't post too frequently
const MIN_INTERVAL = 15 * 60 * 1000; // 15 minutes

// Add random delay
const randomDelay = Math.random() * 300000; // 0-5 minutes
await sleep(MIN_INTERVAL + randomDelay);
```

**Error Recovery:**
```javascript
// Retry with exponential backoff
async function uploadWithRetry(platform, video, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await upload(platform, video);
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      await sleep(Math.pow(2, i) * 1000);
    }
  }
}
```

**Monitoring:**
```javascript
// Alert on failures
if (failedPlatforms.length > 0) {
  sendTelegramAlert(`⚠️ Upload failed: ${failedPlatforms.join(', ')}`);
}

// Daily summary
sendDailySummary({
  total_posts: 3,
  successful: 14, // 3 posts × 5 platforms - 1 failed
  failed: 1,
  platforms: {...}
});
```

---

## Security Checklist

- [ ] Never commit credentials to git
- [ ] Use environment variables
- [ ] Rotate tokens every 90 days
- [ ] Enable 2FA on all accounts
- [ ] Monitor API usage for anomalies
- [ ] Set up quota alerts
- [ ] Regular security audits
- [ ] Backup credentials securely

---

## Next Steps

1. **Setup Platforms** (follow individual guides)
   - [YouTube Shorts](API-SETUP-YOUTUBE.md)
   - [TikTok](API-SETUP-TIKTOK.md)
   - [Instagram Reels](API-SETUP-INSTAGRAM.md)
   - [Facebook Reels](API-SETUP-FACEBOOK.md)

2. **Import Enhanced Workflow**
   - Use `workflows/tuyul-digital-web3-full.json`
   - Configure all credentials
   - Test each platform individually

3. **Video Generation Setup**
   - Choose video generation method
   - Setup cloud storage
   - Test video creation pipeline

4. **Monitor & Optimize**
   - Track performance
   - Adjust posting times
   - Refine content based on analytics

---

**Ready to automate? Start with [YouTube Shorts Setup](API-SETUP-YOUTUBE.md)!**
