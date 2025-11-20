# TikTok API Setup Guide

Panduan lengkap untuk setup TikTok Content Posting API untuk upload video otomatis.

---

## Overview

TikTok menyediakan **Content Posting API** untuk developer yang ingin upload video secara programmatic. API ini bagian dari TikTok for Developers program.

**PENTING:** TikTok API approval bisa memakan waktu 1-2 minggu, jadi apply secepatnya!

---

## Step 1: Create TikTok Developer Account

1. **Register TikTok Developer**
   - URL: https://developers.tiktok.com/
   - Klik "Register"
   - Login dengan TikTok account Anda (buat dulu jika belum punya)

2. **Complete Profile**
   - Full Name
   - Email
   - Phone Number (will be verified)
   - Country/Region

3. **Verify Email & Phone**
   - Check email untuk verification link
   - Verify phone dengan OTP

---

## Step 2: Create App

1. **Go to My Apps**
   - Dashboard: https://developers.tiktok.com/apps
   - Klik "Create an app"

2. **App Information**
   ```
   App name: n8n TikTok Automation
   App description: Automated content posting for educational crypto content
   Category: Content Creation
   ```

3. **Select Products** (PENTING!)
   - ✅ **Content Posting API** (pilih ini!)
   - Bisa juga pilih:
     - Login Kit (untuk OAuth)
     - Video Kit (opsional)

4. **Platform**
   - Platform: **Web**
   - Website URL: `https://your-website.com` atau `http://localhost:5678`

5. **Submit untuk Review**
   - TikTok akan review app Anda
   - Approval time: 1-7 hari kerja (bisa lebih lama)
   - Status bisa dicek di dashboard

---

## Step 3: App Review Requirements

### What TikTok Looks For:

1. **Clear Use Case**
   - Jelaskan detail kenapa butuh API
   - Contoh: "Automated educational content about crypto/blockchain"

2. **Privacy Policy URL** (required!)
   - Buat simple privacy policy
   - Host di website Anda
   - Atau gunakan generator: https://www.privacypolicygenerator.info/

3. **Terms of Service URL** (recommended)
   - Similar dengan privacy policy

4. **App Icon** (512x512px)

### Tips Agar Cepat Diapprove:

- Jelaskan use case dengan detail
- Jangan mention "automation" atau "bot" secara eksplisit
- Fokus ke "content creation tools"
- Gunakan TikTok account yang sudah ada posting (bukan baru)

---

## Step 4: Get Client Key & Secret

**Setelah app approved:**

1. **Go to Your App**
   - Dashboard > My Apps > [App Name]

2. **Copy Credentials**
   ```
   Client Key: awxxxxxxxxxxxxxxx
   Client Secret: xxxxxxxxxxxxxxxxxxxxxxxx
   ```

3. **Setup Redirect URI**
   - Scroll ke "Redirect URI"
   - Add:
     - Local: `http://localhost:5678/rest/oauth2-credential/callback`
     - Production: `https://your-domain.com/rest/oauth2-credential/callback`
   - Save

---

## Step 5: Request Content Posting API Access

**PENTING:** Bahkan setelah app approved, Anda perlu request akses khusus untuk Content Posting API.

1. **Go to Permissions**
   - Di app dashboard > Permissions

2. **Request Scopes:**
   - `user.info.basic` - Get user info
   - `video.upload` - Upload videos
   - `video.publish` - Publish videos

3. **Fill Business Information**
   - Business Name
   - Business Email
   - Use Case Description
   - Expected Monthly Upload Volume

4. **Submit dan Tunggu**
   - TikTok review lagi (1-2 minggu)
   - Mereka bisa minta informasi tambahan

---

## Step 6: Setup di n8n

### 6.1 Create HTTP Request Credential

**Untuk TikTok, kita pakai HTTP Request node dengan OAuth 2.0**

1. **Buka n8n Credentials**
   - Add Credential > "OAuth2 API"

2. **Configuration**
   ```
   Credential Name: TikTok Content Posting API

   Grant Type: Authorization Code

   Authorization URL:
   https://www.tiktok.com/auth/authorize/

   Access Token URL:
   https://open-api.tiktok.com/oauth/access_token/

   Client ID: [Your Client Key]
   Client Secret: [Your Client Secret]

   Scope: user.info.basic,video.upload,video.publish

   Auth URI Query Parameters:
   - client_key: [Your Client Key]
   - response_type: code

   Authentication: Body

   Redirect URL: [Must match yang di TikTok Developer Console]
   ```

3. **Connect Account**
   - Click "Connect my account"
   - Login dengan TikTok
   - Authorize permissions
   - Save credential

---

## Step 7: Upload Video Workflow

### TikTok Upload Process (Multi-Step)

TikTok upload ada **3 tahap:**

1. **Initialize Upload** - Get upload URL
2. **Upload Video** - Upload file ke URL
3. **Publish Video** - Publish dengan metadata

### Node 1: Initialize Upload

```json
{
  "name": "TikTok - Initialize Upload",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "method": "POST",
    "url": "https://open.tiktokapis.com/v2/post/publish/video/init/",
    "authentication": "oAuth2",
    "sendHeaders": true,
    "headerParameters": {
      "parameters": [
        {
          "name": "Content-Type",
          "value": "application/json"
        }
      ]
    },
    "sendBody": true,
    "bodyParameters": {
      "parameters": [
        {
          "name": "post_info",
          "value": {
            "title": "Your Video Title",
            "privacy_level": "SELF_ONLY",
            "disable_duet": false,
            "disable_comment": false,
            "disable_stitch": false,
            "video_cover_timestamp_ms": 1000
          }
        },
        {
          "name": "source_info",
          "value": {
            "source": "FILE_UPLOAD",
            "video_size": 5242880,
            "chunk_size": 5242880,
            "total_chunk_count": 1
          }
        }
      ]
    }
  }
}
```

**Response:**
```json
{
  "data": {
    "publish_id": "v_pub_xxxxx",
    "upload_url": "https://open-upload.tiktokapis.com/..."
  }
}
```

### Node 2: Upload Video File

```json
{
  "name": "TikTok - Upload Video",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "method": "PUT",
    "url": "={{ $json.data.upload_url }}",
    "sendHeaders": true,
    "headerParameters": {
      "parameters": [
        {
          "name": "Content-Type",
          "value": "video/mp4"
        },
        {
          "name": "Content-Length",
          "value": "={{ $json.source_info.video_size }}"
        }
      ]
    },
    "sendBody": true,
    "contentType": "binaryData",
    "bodyParameters": {
      "parameters": []
    }
  }
}
```

### Node 3: Publish Video

```json
{
  "name": "TikTok - Publish Video",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "method": "POST",
    "url": "https://open.tiktokapis.com/v2/post/publish/status/fetch/",
    "authentication": "oAuth2",
    "sendBody": true,
    "bodyParameters": {
      "parameters": [
        {
          "name": "publish_id",
          "value": "={{ $('TikTok - Initialize Upload').item.json.data.publish_id }}"
        }
      ]
    }
  }
}
```

---

## Video Requirements

### Technical Specs
- **Format:** MP4, MOV, MPEG, AVI, WEBM
- **Resolution:** Minimal 720p, recommended 1080p
- **Aspect Ratio:** 9:16 (vertical), 16:9 (horizontal), 1:1 (square)
- **Duration:** 3 seconds - 10 minutes
- **File Size:** Max 4GB
- **Frame Rate:** 23-60 FPS
- **Bitrate:** Recommended 8-12 Mbps

### Content Guidelines
- No copyrighted content
- Follow TikTok Community Guidelines
- No excessive automation (space out posts)
- Include engaging captions dan hashtags

---

## Privacy Levels

```json
{
  "SELF_ONLY": "Private (only you)",
  "MUTUAL_FOLLOW_FRIENDS": "Friends only",
  "PUBLIC_TO_EVERYONE": "Public (everyone)"
}
```

**Recommendation:** Start dengan `SELF_ONLY` untuk testing, switch ke `PUBLIC_TO_EVERYONE` untuk production.

---

## Rate Limits

### Per User
- **Video Upload:** 10 videos/day per user
- **API Calls:** 1000 calls/day per user

### Per App
- **Total API Calls:** Tergantung agreement dengan TikTok
- Contact TikTok untuk increase limit

**Important:** Respect rate limits! Excessive calls bisa ban account.

---

## Common Issues & Solutions

### Error: "App not approved"
**Solution:**
- Tunggu approval dari TikTok
- Check email untuk update
- Bisa follow up via TikTok Developer support

### Error: "Insufficient permissions"
**Solution:**
- Request `video.upload` dan `video.publish` scopes
- Re-authorize credential

### Error: "Invalid video format"
**Solution:**
- Convert video to MP4
- Ensure aspect ratio 9:16
- Check file size < 4GB

### Error: "Upload URL expired"
**Solution:**
- Upload URL hanya valid 2 jam
- Retry dari step Initialize Upload

### Video stuck in "Processing"
**Solution:**
- TikTok processing bisa 5-30 menit
- Check status dengan Publish endpoint
- Tunggu atau contact support jika > 1 jam

---

## Testing

### Test Checklist
- [ ] App approved oleh TikTok
- [ ] Content Posting API access granted
- [ ] OAuth credential setup di n8n
- [ ] Initialize upload successful
- [ ] Video upload successful
- [ ] Publish successful
- [ ] Video muncul di TikTok account (private)
- [ ] Switch to public dan verify

---

## Production Best Practices

1. **Content Quality**
   - High resolution (1080p+)
   - Engaging thumbnail
   - Clear audio
   - Subtitles recommended

2. **Posting Strategy**
   - Space out posts (jangan 10 video sekaligus)
   - Optimal times: 6-9 PM local time
   - Max 3-5 posts per day

3. **Compliance**
   - Follow TikTok Community Guidelines
   - Respect copyright
   - Disclose automation jika diperlukan

4. **Monitoring**
   - Track video status
   - Monitor engagement
   - Log errors untuk debugging

---

## Alternative: TikTok Business API

Jika Anda business account dengan spending ads, consider **TikTok Business API**:
- Higher limits
- More features
- Better support
- Tapi butuh ad spending commitment

**Apply:** https://ads.tiktok.com/marketing_api/docs

---

## Helpful Links

- TikTok Developer Portal: https://developers.tiktok.com/
- Content Posting API Docs: https://developers.tiktok.com/doc/content-posting-api-get-started
- API Reference: https://developers.tiktok.com/doc/content-posting-api-reference-upload-video
- Community Forum: https://developers.tiktok.com/community
- Support: https://developers.tiktok.com/support

---

**Next:** [Instagram Reels API Setup](API-SETUP-INSTAGRAM.md)
