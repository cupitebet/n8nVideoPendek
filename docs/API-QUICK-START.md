# Quick Start: Setup All Social Media APIs

Panduan ringkas untuk setup semua platform dalam urutan yang efisien.

---

## Timeline Overview

**Total Time:** 1-2 minggu (mostly waiting untuk TikTok approval)

**Active Work:** ~4-6 jam total

---

## Day 1: Foundation (2 jam)

### ✅ Telegram Bot (5 menit)

1. Chat dengan @BotFather di Telegram
2. `/newbot` dan ikuti instruksi
3. Dapatkan bot token
4. Save token

**Credential di n8n:**
- Type: Telegram API
- Token: [paste bot token]

**Get Chat ID:**
```bash
# Send message ke bot Anda, lalu:
https://api.telegram.org/bot<TOKEN>/getUpdates
```

---

### ✅ YouTube Shorts (30 menit)

**Step 1: Google Cloud Console**
1. https://console.cloud.google.com/
2. Create Project: "n8n-youtube"
3. Enable "YouTube Data API v3"

**Step 2: OAuth Consent**
1. Configure OAuth consent screen
2. Add scopes:
   - `youtube.upload`
   - `youtube.force-ssl`
3. Add test users

**Step 3: Create Credentials**
1. Create OAuth 2.0 Client ID
2. Add redirect URI:
   ```
   http://localhost:5678/rest/oauth2-credential/callback
   ```
3. Save Client ID & Secret

**Credential di n8n:**
- Type: Google OAuth2 API
- Paste Client ID & Secret
- Connect account

**Full Guide:** [API-SETUP-YOUTUBE.md](API-SETUP-YOUTUBE.md)

---

### ✅ TikTok Developer (15 menit - apply & tunggu)

**Apply Now! (Approval 1-2 minggu)**

1. https://developers.tiktok.com/
2. Register developer account
3. Create app: "n8n Content Creator"
4. Select product: **Content Posting API**
5. Submit untuk review

**Sambil tunggu approval, lanjut platform lain!**

**Full Guide:** [API-SETUP-TIKTOK.md](API-SETUP-TIKTOK.md)

---

### ✅ Facebook Page (10 menit)

1. https://facebook.com/pages/create
2. Create Business Page
3. Add profile picture & cover
4. Publish page

Save Page ID (dari URL page Anda)

---

## Day 2: Meta Platforms (1.5 jam)

### ✅ Facebook Developer App (20 menit)

1. https://developers.facebook.com/
2. Create App > Business
3. Name: "n8n Social Automation"
4. Add products:
   - Facebook Login
   - Instagram Graph API

**Full Guide:** [API-SETUP-FACEBOOK.md](API-SETUP-FACEBOOK.md)

---

### ✅ Instagram Business (15 menit)

**Convert Instagram to Business:**
1. Instagram app > Settings
2. Account > Switch to Professional
3. Choose "Business"
4. **Connect to Facebook Page** (important!)

**Verify connection:**
- Instagram > Settings > Account > Linked Accounts
- Should see Facebook Page

**Full Guide:** [API-SETUP-INSTAGRAM.md](API-SETUP-INSTAGRAM.md)

---

### ✅ Get Facebook/Instagram Tokens (30 menit)

**Graph API Explorer:**
1. https://developers.facebook.com/tools/explorer
2. Select your app
3. Add permissions:
   - `pages_manage_posts`
   - `pages_show_list`
   - `instagram_content_publish`
   - `instagram_basic`
4. Generate User Access Token

**Get Long-Lived Token:**
```bash
curl "https://graph.facebook.com/v18.0/oauth/access_token?\
grant_type=fb_exchange_token&\
client_id=YOUR_APP_ID&\
client_secret=YOUR_APP_SECRET&\
fb_exchange_token=SHORT_LIVED_TOKEN"
```

**Get Page Token (Never Expires!):**
```bash
curl "https://graph.facebook.com/v18.0/me/accounts?\
access_token=LONG_LIVED_USER_TOKEN"
```

Save:
- Page Access Token
- Page ID
- Instagram Business Account ID

**Credential di n8n:**
- Type: HTTP Query Auth
- Name: `access_token`
- Value: [Page Access Token]

---

## Day 3: Setup Cloud Storage (45 menit)

### Option A: AWS S3 (Recommended)

**Create S3 Bucket:**
```bash
aws s3 mb s3://my-shorts-videos
```

**Bucket Policy (Public Read):**
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "PublicRead",
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::my-shorts-videos/*"
  }]
}
```

**Credential di n8n:**
- Type: AWS
- Access Key & Secret Key

**Cost:** ~$0.023/GB storage + bandwidth

---

### Option B: Cloudinary (Simpler)

1. https://cloudinary.com/users/register/free
2. Get Cloud Name, API Key, API Secret
3. Free tier: 25GB storage, 25GB bandwidth

**Credential di n8n:**
- Type: Cloudinary API
- Paste Cloud Name, Key, Secret

**Cost:** Free untuk start

---

## Day 7: Check TikTok Status

1. https://developers.tiktok.com/apps
2. Check app approval status
3. Jika approved, proceed dengan token generation
4. Jika belum, follow up via support

**When Approved:**

**Get Access Token:**
1. Go to your app
2. Copy Client Key & Secret
3. Setup OAuth in n8n:
   - Authorization URL: `https://www.tiktok.com/auth/authorize/`
   - Access Token URL: `https://open-api.tiktok.com/oauth/access_token/`
   - Scopes: `user.info.basic,video.upload,video.publish`

---

## Day 8-14: Testing & Optimization

### Test Checklist

**Per Platform:**
- [ ] Credentials setup
- [ ] Test upload with sample video
- [ ] Verify post visible
- [ ] Check analytics access

**Integration:**
- [ ] Multi-platform upload works
- [ ] Error handling tested
- [ ] Notifications working
- [ ] Archive to sheets working

---

## Quick Reference: All Credentials

```javascript
// Save this as reference
{
  "telegram": {
    "bot_token": "FROM @BotFather",
    "chat_id": "FROM getUpdates API"
  },

  "youtube": {
    "type": "Google OAuth2",
    "client_id": "FROM Google Cloud Console",
    "client_secret": "FROM Google Cloud Console"
  },

  "instagram": {
    "type": "HTTP Query Auth",
    "access_token": "FROM Graph API Explorer > /me/accounts",
    "business_account_id": "FROM Graph API > /{page-id}?fields=instagram_business_account"
  },

  "facebook": {
    "type": "HTTP Query Auth",
    "access_token": "SAME as Instagram (Page Token)",
    "page_id": "FROM Facebook Page URL atau Graph API"
  },

  "tiktok": {
    "type": "OAuth2 API",
    "client_key": "FROM TikTok Developer Console",
    "client_secret": "FROM TikTok Developer Console"
  },

  "cloudinary": {
    "cloud_name": "FROM Cloudinary Dashboard",
    "api_key": "FROM Cloudinary Dashboard",
    "api_secret": "FROM Cloudinary Dashboard"
  }
}
```

---

## Common Pitfalls & Solutions

### ❌ Instagram upload fails
**Cause:** Using Personal account
**Fix:** Convert to Business/Creator account

### ❌ YouTube quota exceeded
**Cause:** 10,000 units/day limit (1,600 per upload = 6 uploads/day)
**Fix:** Request quota increase atau space out uploads

### ❌ TikTok approval rejected
**Cause:** Vague use case or policy violation
**Fix:** Resubmit dengan clear educational use case

### ❌ Video URL not accessible
**Cause:** Private S3 bucket atau CORS issue
**Fix:** Set bucket policy public read

### ❌ Facebook/Instagram token expired
**Cause:** Using short-lived token
**Fix:** Get Page token (never expires)

---

## Cost Summary

**Minimal Setup (1-3 videos/day):**
```
OpenAI GPT-4o-mini: $5-10/month
Cloudinary free tier: $0
Total: $5-10/month
```

**Full Setup (10+ videos/day):**
```
OpenAI: $20-30/month
AWS S3 + CloudFront: $10-20/month
Video generation (Pictory): $29/month
Total: $60-80/month
```

---

## Next Steps After Setup

1. **Import Enhanced Workflow**
   - Use nodes from [WORKFLOW-SOCIAL-MEDIA-NODES.md](WORKFLOW-SOCIAL-MEDIA-NODES.md)

2. **Create Test Video**
   - Simple 15-second vertical video
   - Upload to cloud storage
   - Get public URL

3. **Test Each Platform**
   - Start with Telegram (easiest)
   - Then YouTube
   - Then Meta platforms (Instagram/Facebook)
   - Finally TikTok

4. **Setup Automation**
   - Configure schedule
   - Setup error notifications
   - Monitor first few runs

5. **Optimize**
   - Analyze which platform performs best
   - Adjust posting times
   - Refine content based on engagement

---

## Support & Resources

**Documentation:**
- [Complete Guide](SOCIAL-MEDIA-COMPLETE-GUIDE.md)
- [YouTube Setup](API-SETUP-YOUTUBE.md)
- [TikTok Setup](API-SETUP-TIKTOK.md)
- [Instagram Setup](API-SETUP-INSTAGRAM.md)
- [Facebook Setup](API-SETUP-FACEBOOK.md)
- [Workflow Nodes](WORKFLOW-SOCIAL-MEDIA-NODES.md)

**Community:**
- n8n Community: https://community.n8n.io
- Platform-specific developer forums

**Need Help?**
- Check troubleshooting sections di setiap guide
- Join Telegram group (buat group untuk users)
- GitHub issues

---

**Let's Go! Start dengan Telegram & YouTube hari ini! 🚀**
