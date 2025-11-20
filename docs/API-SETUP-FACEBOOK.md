# Facebook Reels API Setup Guide

Panduan lengkap untuk setup Facebook Graph API untuk upload Reels otomatis ke Facebook.

---

## Overview

Facebook Reels menggunakan **Meta Graph API** yang sama dengan Instagram. Prosesnya sangat mirip, tapi upload ke Facebook Page instead of Instagram account.

**PENTING:** Anda harus punya Facebook Page (bukan personal profile) untuk upload Reels via API.

---

## Prerequisites

1. **Facebook Page** (required!)
   - Business Page atau Creator Page
   - Cannot use personal profile untuk API upload

2. **Facebook Developer Account**
   - Apply sebagai developer

3. **Page Admin Access**
   - Anda harus admin dari Page tersebut

---

## Step 1: Create Facebook Page

**Jika sudah punya Page, skip ke Step 2.**

1. **Go to Pages**
   - URL: https://www.facebook.com/pages/create
   - Or: Facebook > Menu > Pages > Create New Page

2. **Page Details**
   ```
   Page Name: Tuyul Digital Web3
   Category: Education / Content Creator / Media/News
   Bio: Educational content about Crypto, Blockchain & Web3
   ```

3. **Add Profile Picture & Cover**
   - Size: Profile 180x180, Cover 820x312
   - Use branded images

4. **Complete Setup**
   - Add CTA button (optional)
   - Add contact info
   - Publish Page

---

## Step 2: Create Facebook App

**Jika sudah punya App dari Instagram setup, bisa pakai yang sama!**

1. **Go to Meta for Developers**
   - URL: https://developers.facebook.com/
   - Login dengan Facebook account

2. **Create App** (or use existing)
   - Click "Create App"
   - Type: **Business**
   - App Name: `n8n Social Media Automation`
   - Contact Email: your-email@example.com
   - Create App

3. **Add Products**
   - Di Dashboard, add product "Facebook Login"
   - Juga ensure "Pages API" enabled

---

## Step 3: Get Page Access Token

### Method 1: Graph API Explorer (Quick)

1. **Open Graph API Explorer**
   - URL: https://developers.facebook.com/tools/explorer
   - Select your App dari dropdown

2. **Generate User Token**
   - User or Page: "User Token"
   - Click "Permissions"
   - Add permissions:
     - `pages_manage_posts`
     - `pages_read_engagement`
     - `pages_show_list`
     - `publish_video`

3. **Generate Access Token**
   - Click "Generate Access Token"
   - Login dan authorize
   - This is short-lived token (1-2 hours)

4. **Get Page Token**
   - In explorer, run GET request:
     ```
     /me/accounts
     ```
   - Response shows all Pages you manage:
     ```json
     {
       "data": [
         {
           "access_token": "EAAG...",
           "category": "Education",
           "name": "Tuyul Digital Web3",
           "id": "123456789012345",
           "tasks": ["ANALYZE", "ADVERTISE", "MODERATE", "CREATE_CONTENT"]
         }
       ]
     }
     ```

5. **Copy Page Access Token**
   - This `access_token` doesn't expire!
   - Save it securely

### Method 2: Long-Lived Token (Recommended for Production)

**Convert User Token to Long-Lived:**

```bash
curl -X GET "https://graph.facebook.com/v18.0/oauth/access_token" \
  -d "grant_type=fb_exchange_token" \
  -d "client_id={app-id}" \
  -d "client_secret={app-secret}" \
  -d "fb_exchange_token={short-lived-user-token}"
```

**Then Get Page Token:**

```bash
curl -X GET "https://graph.facebook.com/v18.0/me/accounts" \
  -d "access_token={long-lived-user-token}"
```

Page token = never expires (as long as app is active).

---

## Step 4: Setup di n8n

### Create Credential

1. **Buka n8n > Credentials**
   - Add Credential > "HTTP Query Auth"

2. **Configuration**
   ```
   Credential Name: Facebook Page API

   Authentication Method: Query Auth

   Name: access_token
   Value: {page-access-token}
   ```

3. **Save**

---

## Step 5: Upload Reels to Facebook

### Upload Process (2 Steps)

1. **Upload Video** - Get video ID
2. **Publish as Reel** - Make it live

### Node 1: Upload Video to Facebook

```json
{
  "name": "Facebook - Upload Video",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "method": "POST",
    "url": "=https://graph.facebook.com/v18.0/{{ $json.page_id }}/videos",
    "authentication": "genericCredentialType",
    "genericAuthType": "httpQueryAuth",
    "sendQuery": true,
    "queryParameters": {
      "parameters": [
        {
          "name": "file_url",
          "value": "={{ $json.video_url }}"
        },
        {
          "name": "description",
          "value": "={{ $json.caption }}"
        },
        {
          "name": "title",
          "value": "={{ $json.title }}"
        }
      ]
    }
  },
  "credentials": {
    "httpQueryAuth": {
      "id": "facebook-page-credential",
      "name": "Facebook Page API"
    }
  }
}
```

**Response:**
```json
{
  "id": "987654321098765"
}
```

### Node 2: Convert to Reel (Optional but Recommended)

**Mark video as Reel for better reach:**

```json
{
  "name": "Facebook - Mark as Reel",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "method": "POST",
    "url": "=https://graph.facebook.com/v18.0/{{ $json.id }}",
    "sendQuery": true,
    "queryParameters": {
      "parameters": [
        {
          "name": "fields",
          "value": "is_crosspost_video"
        },
        {
          "name": "is_crosspost_video",
          "value": "true"
        }
      ]
    }
  }
}
```

---

## Alternative: Resumable Upload (for Large Files)

Untuk video > 100MB, gunakan resumable upload:

### Step 1: Start Upload Session

```bash
curl -X POST "https://graph.facebook.com/v18.0/{page-id}/videos" \
  -F "upload_phase=start" \
  -F "file_size={file-size-in-bytes}" \
  -F "access_token={page-access-token}"
```

Response:
```json
{
  "video_id": "1234567890",
  "start_offset": "0",
  "end_offset": "52428800",
  "upload_session_id": "upload_session_id_value"
}
```

### Step 2: Transfer Video Data

```bash
curl -X POST "https://graph.facebook.com/v18.0/{page-id}/videos" \
  -F "upload_phase=transfer" \
  -F "start_offset=0" \
  -F "upload_session_id={upload-session-id}" \
  -F "video_file_chunk=@video_chunk_1.mp4" \
  -F "access_token={page-access-token}"
```

### Step 3: Finish Upload

```bash
curl -X POST "https://graph.facebook.com/v18.0/{page-id}/videos" \
  -F "upload_phase=finish" \
  -F "upload_session_id={upload-session-id}" \
  -F "title=My Reel Title" \
  -F "description=My caption with #hashtags" \
  -F "access_token={page-access-token}"
```

---

## Video Requirements

### Technical Specs
- **Format:** MP4, MOV
- **Codec:** H.264, AAC audio
- **Resolution:** Minimum 540x960 (9:16 aspect ratio)
- **Duration:** 3 seconds - 90 seconds (optimal: 15-30 sec)
- **File Size:** Max 4GB
- **Aspect Ratio:** 9:16 (vertical) for Reels
- **Frame Rate:** 23-60 FPS

### Video URL Requirements
- **Publicly accessible URL** (HTTPS only)
- Direct link to video file
- Or use resumable upload untuk large files

---

## Caption Best Practices

```
🚀 Engaging opening line

Key points:
• Point 1 with emoji
• Point 2 with emoji
• Point 3 with emoji

Call to action here!

#Crypto #Web3 #Blockchain #Reels #Education
#FacebookReels #CryptoNews #BlockchainTechnology

Max 63,206 characters (very generous!)
No limit on hashtags (but 5-10 optimal)
```

---

## Scheduling Posts (Optional)

Facebook API supports scheduled publishing:

```json
{
  "queryParameters": {
    "parameters": [
      {
        "name": "scheduled_publish_time",
        "value": "1672531200"
      },
      {
        "name": "published",
        "value": "false"
      }
    ]
  }
}
```

**Note:** Time in Unix timestamp, must be 10-75 days in future.

---

## Rate Limits

### Per Page
- **Video Upload:** 1500 videos per day
- **API Calls:** 200 calls per hour per user
- **Content Publishing:** No explicit limit, but don't spam

### Best Practices
- Max 10-15 posts per day
- Space out posts (tidak sekaligus)
- Monitor for any rate limit errors

---

## Common Issues & Solutions

### Error: "Invalid OAuth access token"
**Solution:**
- Regenerate Page access token
- Ensure using Page token, not User token
- Check token tidak expired

### Error: "Permissions error"
**Solution:**
- Verify permissions: `pages_manage_posts`, `publish_video`
- Re-authorize app dengan Page
- Ensure you're admin of the Page

### Error: "Video upload failed"
**Solution:**
- Check video meets requirements
- Ensure video URL is publicly accessible
- Try resumable upload untuk large files

### Video tidak muncul sebagai Reel
**Solution:**
- Ensure aspect ratio 9:16 (vertical)
- Duration < 90 seconds
- Mark as crosspost video (is_crosspost_video=true)

### Reels tidak dapat reach
**Solution:**
- Use engaging thumbnail
- Add relevant hashtags
- Post at optimal times (6-9 PM)
- Consistent posting schedule
- Engage dengan audience di comments

---

## Cross-Posting to Instagram

Jika Anda punya Instagram Business account connected ke Facebook Page, bisa auto cross-post:

```json
{
  "queryParameters": {
    "parameters": [
      {
        "name": "crosspost_to_instagram",
        "value": "true"
      }
    ]
  }
}
```

**Requirements:**
- Instagram Business account
- Connected to same Facebook Page
- Both accounts managed by you

---

## Analytics & Insights

Get video performance data:

```bash
curl -X GET "https://graph.facebook.com/v18.0/{video-id}/video_insights" \
  -d "metric=total_video_views,total_video_reactions" \
  -d "access_token={page-access-token}"
```

Useful metrics:
- `total_video_views`
- `total_video_reactions`
- `total_video_comments`
- `total_video_shares`
- `total_video_view_time`

---

## Testing Workflow

### Test Checklist
- [ ] Facebook Page created
- [ ] Page admin access verified
- [ ] Facebook App created
- [ ] Permissions granted (pages_manage_posts, publish_video)
- [ ] Page access token obtained
- [ ] Video hosted at public URL
- [ ] Test upload successful
- [ ] Video published as Reel
- [ ] Reel visible on Facebook Page

---

## Production Setup

### 1. Automation Workflow

```
Generate Content
    → Upload to Cloud Storage (get public URL)
    → Upload to Facebook
    → Mark as Reel
    → Archive metadata
    → Send notification
```

### 2. Multi-Platform Publishing

Upload to all platforms in parallel:
- Facebook Reels
- Instagram Reels
- YouTube Shorts
- TikTok

Use n8n's parallel execution for efficiency.

### 3. Error Handling

- Retry failed uploads (max 3 times)
- Log errors to database/sheet
- Send admin notification on failures
- Fallback to manual publishing jika auto gagal

---

## App Verification (Optional)

For production apps with many users:

1. **Submit App for Review**
   - Dashboard > App Review
   - Request `pages_manage_posts`, `publish_video`

2. **Provide Use Case**
   - Explain how app uses permissions
   - Screencast demo

3. **Benefits**
   - Remove test mode restrictions
   - Higher rate limits
   - Public app listing

---

## Helpful Links

- Meta for Developers: https://developers.facebook.com/
- Pages API Documentation: https://developers.facebook.com/docs/pages-api
- Video API Reference: https://developers.facebook.com/docs/video-api
- Graph API Explorer: https://developers.facebook.com/tools/explorer
- Access Token Debugger: https://developers.facebook.com/tools/debug/accesstoken/
- Facebook Reels Best Practices: https://www.facebook.com/business/help/reels

---

## Summary: Facebook vs Instagram Reels

| Feature | Facebook | Instagram |
|---------|----------|-----------|
| **API** | Graph API | Graph API (same) |
| **Account Type** | Facebook Page | Business/Creator |
| **Upload Method** | Direct URL or Resumable | Container → Publish |
| **Max Duration** | 90 seconds | 90 seconds |
| **Caption Length** | 63,206 chars | 2,200 chars |
| **Scheduling** | ✅ Yes | ❌ No (via API) |
| **Cross-posting** | To Instagram | To Facebook |
| **Rate Limit** | 1500/day | 25/day |

**Recommendation:** Set up both! Use same video, customize captions per platform.

---

**Next:** [Complete Workflow Integration](SOCIAL-MEDIA-WORKFLOW.md)
