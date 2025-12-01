# Instagram Reels API Setup Guide

Panduan lengkap untuk setup Instagram Graph API untuk upload Reels otomatis.

---

## Overview

Instagram Reels di-upload menggunakan **Meta Graph API** (formerly Facebook Graph API). Instagram Graph API memerlukan:
- Facebook Developer account
- Instagram Business atau Creator account
- Facebook Page yang connected ke Instagram

**PENTING:** Instagram PERSONAL account tidak bisa! Harus Business/Creator.

---

## Prerequisites

1. **Instagram Business/Creator Account**
   - Convert dari Personal: Settings > Account > Switch to Professional Account
   - Pilih "Business" atau "Creator"

2. **Facebook Page**
   - Create Facebook Page jika belum ada
   - Connect Instagram account ke Facebook Page

3. **Facebook Developer Account**
   - Apply sebagai developer di Facebook

---

## Step 1: Convert Instagram to Business Account

1. **Open Instagram App**
   - Go to Profile > Menu (3 lines)

2. **Settings > Account**
   - Scroll to "Switch to Professional Account"
   - Tap "Continue"

3. **Choose Category**
   - Select relevant category (e.g., "Digital Creator", "Education")

4. **Choose Business/Creator**
   - Pilih "Business" (recommended) atau "Creator"

5. **Complete Setup**
   - Add contact info
   - Connect to Facebook Page (PENTING!)

---

## Step 2: Create/Connect Facebook Page

### If You Don't Have Facebook Page:

1. **Create Facebook Page**
   - Go to: https://www.facebook.com/pages/create
   - Choose "Business or Brand"
   - Page Name: [Your Brand Name]
   - Category: Content Creator / Education
   - Create Page

### Connect Instagram to Facebook Page:

1. **In Facebook Page Settings**
   - Go to your Facebook Page
   - Settings > Instagram
   - Click "Connect Account"
   - Login Instagram
   - Link account

2. **Verify Connection**
   - Di Instagram: Settings > Account > Linked Accounts
   - Should see Facebook Page connected

---

## Step 3: Create Facebook App

1. **Go to Meta for Developers**
   - URL: https://developers.facebook.com/
   - Click "Get Started" atau "My Apps"
   - Login dengan Facebook account

2. **Create App**
   - Click "Create App"
   - Use case: **Business** (atau "Other" jika tidak ada)
   - App Type: **Business**
   - Click "Next"

3. **App Details**
   ```
   App Name: n8n Instagram Automation
   App Contact Email: your-email@example.com
   Business Account: [Select yours atau create new]
   ```
   - Click "Create App"

4. **Complete Security Check**
   - Solve captcha/verification

---

## Step 4: Setup Instagram Graph API

1. **Add Instagram Product**
   - Di App Dashboard
   - Find "Instagram" product
   - Click "Set Up"

2. **Get Started**
   - Follow wizard
   - Link your Instagram Business Account
   - Link your Facebook Page

---

## Step 5: Get Access Tokens

### 5.1 Short-Lived User Access Token

1. **Go to Graph API Explorer**
   - Tools > Graph API Explorer
   - URL: https://developers.facebook.com/tools/explorer

2. **Select Your App**
   - Dropdown: Select your app
   - User or Page: Select "User Token"

3. **Add Permissions**
   - Click "Permissions"
   - Add:
     - `instagram_basic`
     - `instagram_content_publish`
     - `pages_read_engagement`
     - `pages_show_list`

4. **Generate Token**
   - Click "Generate Access Token"
   - Login dan authorize
   - Copy token (short-lived, 1 jam)

### 5.2 Long-Lived User Access Token

**Convert short-lived ke long-lived (60 hari):**

```bash
curl -X GET "https://graph.facebook.com/v18.0/oauth/access_token" \
  -d "grant_type=fb_exchange_token" \
  -d "client_id={app-id}" \
  -d "client_secret={app-secret}" \
  -d "fb_exchange_token={short-lived-token}"
```

**Response:**
```json
{
  "access_token": "EAAG...",
  "token_type": "bearer",
  "expires_in": 5183944
}
```

### 5.3 Never-Expiring Page Access Token (Recommended!)

1. **Get Pages**
   ```bash
   curl -X GET "https://graph.facebook.com/v18.0/me/accounts" \
     -d "access_token={long-lived-user-token}"
   ```

2. **Copy Page Access Token**
   - This token doesn't expire (selama app aktif)
   - Use this untuk production

3. **Get Instagram Business Account ID**
   ```bash
   curl -X GET "https://graph.facebook.com/v18.0/{page-id}" \
     -d "fields=instagram_business_account" \
     -d "access_token={page-access-token}"
   ```

   Response:
   ```json
   {
     "instagram_business_account": {
       "id": "17841400008460056"
     },
     "id": "123456789"
   }
   ```

---

## Step 6: Setup di n8n

### Create HTTP Header Auth Credential

1. **Buka n8n > Credentials**
   - Add Credential > "HTTP Header Auth"

2. **Configuration**
   ```
   Name: Instagram Graph API

   Header Name: Authorization
   Header Value: Bearer {page-access-token}
   ```

   Or simpler:
   ```
   Name: Instagram Graph API - Query Auth

   Type: HTTP Query Auth

   Name: access_token
   Value: {page-access-token}
   ```

3. **Save**

---

## Step 7: Upload Reels Workflow

### Instagram Reels Upload Process (2 Steps)

1. **Create Media Container** - Initialize upload
2. **Publish Container** - Make it live

### Node 1: Create Reels Container

```json
{
  "name": "Instagram - Create Reels Container",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "method": "POST",
    "url": "=https://graph.facebook.com/v18.0/{{ $json.instagram_business_account_id }}/media",
    "authentication": "genericCredentialType",
    "genericAuthType": "httpQueryAuth",
    "sendQuery": true,
    "queryParameters": {
      "parameters": [
        {
          "name": "media_type",
          "value": "REELS"
        },
        {
          "name": "video_url",
          "value": "={{ $json.video_url }}"
        },
        {
          "name": "caption",
          "value": "={{ $json.caption }}"
        },
        {
          "name": "share_to_feed",
          "value": "true"
        }
      ]
    }
  },
  "credentials": {
    "httpQueryAuth": {
      "id": "instagram-credential",
      "name": "Instagram Graph API"
    }
  }
}
```

**Important:** `video_url` harus publicly accessible URL!

**Response:**
```json
{
  "id": "17889455560051444"
}
```

### Node 2: Check Upload Status (Optional)

```json
{
  "name": "Instagram - Check Status",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "method": "GET",
    "url": "=https://graph.facebook.com/v18.0/{{ $json.id }}",
    "sendQuery": true,
    "queryParameters": {
      "parameters": [
        {
          "name": "fields",
          "value": "status_code"
        }
      ]
    }
  }
}
```

Wait until `status_code` = `FINISHED`

### Node 3: Publish Reels

```json
{
  "name": "Instagram - Publish Reels",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "method": "POST",
    "url": "=https://graph.facebook.com/v18.0/{{ $json.instagram_business_account_id }}/media_publish",
    "sendQuery": true,
    "queryParameters": {
      "parameters": [
        {
          "name": "creation_id",
          "value": "={{ $('Instagram - Create Reels Container').item.json.id }}"
        }
      ]
    }
  }
}
```

**Response:**
```json
{
  "id": "17846368321095476"
}
```

This is your published Reels ID!

---

## Video Requirements

### Technical Specs
- **Format:** MP4, MOV
- **Codec:** H.264 video, AAC audio
- **Resolution:** 540x960 minimum (9:16 aspect ratio)
- **Duration:** 3 seconds - 90 seconds (15-30 sec optimal)
- **File Size:** Max 1GB
- **Frame Rate:** 23-60 FPS
- **Aspect Ratio:** 9:16 (vertical)

### Video URL Requirements
- **Must be publicly accessible** (no authentication)
- **HTTPS only** (not HTTP)
- **Direct link** to MP4/MOV file
- Hosting options:
  - AWS S3 (public bucket)
  - Cloudinary
  - Your own server dengan public access

---

## Caption & Hashtags

### Best Practices
```
Your engaging caption here 🚀

3-5 poin utama dengan emoji

#Crypto #Web3 #Blockchain #Education #Reels
#CryptoIndonesia #BlockchainTechnology #Web3Education

Max 2200 characters
Max 30 hashtags
```

### Supported Features
- @mentions
- #hashtags
- Emojis ✅
- Line breaks
- Links (not clickable di caption, tapi bisa di bio)

---

## Rate Limits

### Per Instagram Business Account
- **Reels Upload:** ~25 posts/day
- **API Calls:** 200 calls/hour per user

### Content Publishing Limit
- Instagram limit: Max 25 posts per 24 hours
- Includes: Feed posts, Stories, Reels

**Recommendation:** Max 5-10 Reels/day, spaced out.

---

## Common Issues & Solutions

### Error: "Video must be publicly accessible"
**Solution:**
- Host video di public URL (S3, Cloudinary, etc)
- Ensure URL returns video file directly
- Test URL di browser - should download/play video

### Error: "Invalid media type"
**Solution:**
- Ensure `media_type=REELS`
- Check video meets requirements (duration, aspect ratio)

### Error: "Permissions error"
**Solution:**
- Verify permissions: `instagram_content_publish`
- Re-generate access token
- Check Instagram account is Business/Creator

### Error: "Media container is not ready"
**Solution:**
- Wait 10-30 seconds after create container
- Check status before publish
- Retry publish if status = FINISHED

### Reels tidak muncul
**Solution:**
- Check Instagram account tidak di-shadowban
- Ensure video meets all requirements
- Video processing bisa 5-15 menit
- Check di Instagram app, bukan web

---

## Testing Workflow

### Test Checklist
- [ ] Instagram Business account setup
- [ ] Connected to Facebook Page
- [ ] Facebook App created
- [ ] Permissions granted (instagram_content_publish)
- [ ] Access token generated (Page token)
- [ ] Instagram Business Account ID obtained
- [ ] Video hosted di public URL
- [ ] Test upload successful
- [ ] Reels published dan visible

---

## Production Setup

### 1. Video Hosting Strategy

**Option A: AWS S3**
```javascript
// n8n node: Upload to S3
// Then use S3 public URL for Instagram
```

**Option B: Cloudinary**
```javascript
// Upload to Cloudinary
// Get public URL
// Use for Instagram upload
```

**Option C: Self-hosted**
- Ensure server has high bandwidth
- Use CDN untuk better performance

### 2. Monitoring

Track:
- Upload success rate
- Publishing status
- API errors
- Engagement metrics (via separate API calls)

### 3. Error Handling

- Retry logic untuk failed uploads
- Notification untuk errors
- Log all API responses

---

## Advanced: Multiple Instagram Accounts

Untuk manage multiple Instagram accounts:

1. Create separate credentials per account
2. Store Instagram Business Account ID per account
3. Use switch node untuk route ke correct account
4. Monitor quota per account

---

## App Review (Optional but Recommended)

Untuk production use dengan banyak users, submit app untuk review:

1. **Go to App Review**
   - Dashboard > App Review

2. **Request Permissions**
   - `instagram_content_publish`
   - `instagram_basic`

3. **Provide Screencast**
   - Show how your app uses permissions
   - Demo complete user flow

4. **Submit**
   - Review time: 3-7 hari

**Benefits:**
- Remove limitations
- Higher rate limits
- Better support

---

## Helpful Links

- Meta for Developers: https://developers.facebook.com/
- Instagram Graph API Docs: https://developers.facebook.com/docs/instagram-api
- Content Publishing: https://developers.facebook.com/docs/instagram-api/guides/content-publishing
- Graph API Explorer: https://developers.facebook.com/tools/explorer
- Access Token Debugger: https://developers.facebook.com/tools/debug/accesstoken/

---

**Next:** [Facebook Reels API Setup](API-SETUP-FACEBOOK.md)
