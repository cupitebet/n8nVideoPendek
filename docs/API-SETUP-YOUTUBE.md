# YouTube Shorts API Setup Guide

Panduan lengkap untuk setup YouTube Data API v3 untuk upload Shorts otomatis.

---

## Overview

YouTube Shorts dapat di-upload menggunakan YouTube Data API v3. Video dengan durasi <60 detik dan aspect ratio vertical (9:16) otomatis akan masuk kategori Shorts.

---

## Step 1: Create Google Cloud Project

1. **Buka Google Cloud Console**
   - URL: https://console.cloud.google.com/
   - Login dengan akun Google Anda

2. **Create New Project**
   - Klik dropdown project di header
   - Klik "New Project"
   - Project Name: `n8n-youtube-automation` (atau nama lain)
   - Click "Create"

3. **Wait untuk project creation**
   - Biasanya 10-30 detik

---

## Step 2: Enable YouTube Data API v3

1. **Go to API Library**
   - Di sidebar: APIs & Services > Library
   - Atau URL: https://console.cloud.google.com/apis/library

2. **Search YouTube API**
   - Ketik: "YouTube Data API v3"
   - Klik hasil pertama

3. **Enable API**
   - Klik tombol "Enable"
   - Tunggu sampai enabled (15-30 detik)

---

## Step 3: Create OAuth 2.0 Credentials

### 3.1 Configure OAuth Consent Screen

1. **Go to OAuth consent screen**
   - Sidebar: APIs & Services > OAuth consent screen
   - URL: https://console.cloud.google.com/apis/credentials/consent

2. **Choose User Type**
   - Pilih: **External** (kecuali Anda punya Google Workspace)
   - Klik "Create"

3. **App Information**
   ```
   App name: n8n YouTube Automation
   User support email: [your-email@gmail.com]
   Developer contact: [your-email@gmail.com]
   ```
   - Klik "Save and Continue"

4. **Scopes**
   - Klik "Add or Remove Scopes"
   - Search dan pilih:
     - `https://www.googleapis.com/auth/youtube.upload`
     - `https://www.googleapis.com/auth/youtube.force-ssl`
   - Klik "Update"
   - Klik "Save and Continue"

5. **Test Users** (Important untuk External app)
   - Klik "Add Users"
   - Masukkan email akun YouTube yang akan dipakai upload
   - Klik "Add"
   - Klik "Save and Continue"

6. **Summary**
   - Review dan klik "Back to Dashboard"

### 3.2 Create OAuth Client ID

1. **Go to Credentials**
   - Sidebar: APIs & Services > Credentials
   - URL: https://console.cloud.google.com/apis/credentials

2. **Create Credentials**
   - Klik "Create Credentials" > "OAuth client ID"

3. **Application Type**
   - Application type: **Web application**
   - Name: `n8n YouTube OAuth`

4. **Authorized redirect URIs** (PENTING!)

   **Jika n8n self-hosted:**
   ```
   http://localhost:5678/rest/oauth2-credential/callback
   ```

   **Jika n8n Cloud:**
   ```
   https://your-instance.n8n.cloud/rest/oauth2-credential/callback
   ```

   **Tambahkan semua yang relevan:**
   - Local development: `http://localhost:5678/rest/oauth2-credential/callback`
   - Production: `https://your-domain.com/rest/oauth2-credential/callback`

5. **Create**
   - Klik "Create"
   - **SIMPAN:**
     - Client ID: `XXXXXXX.apps.googleusercontent.com`
     - Client Secret: `GOCSPX-XXXXXXXXXXXXX`

---

## Step 4: Setup di n8n

### 4.1 Create Credential

1. **Buka n8n**
   - Go to: Credentials > Add Credential

2. **Search Google**
   - Type: "Google OAuth2 API"
   - Pilih: **Google OAuth2 API**

3. **Fill Credentials**
   ```
   Name: YouTube Upload OAuth2

   Client ID: [paste dari Google Cloud Console]
   Client Secret: [paste dari Google Cloud Console]

   Scope (select from dropdown):
   - https://www.googleapis.com/auth/youtube.upload
   - https://www.googleapis.com/auth/youtube.force-ssl
   ```

4. **Connect Account**
   - Klik "Connect my account"
   - Login dengan akun YouTube yang sudah di-add sebagai Test User
   - Accept permissions
   - Jika berhasil, status jadi "Connected"

5. **Save**

---

## Step 5: Test Upload

### Simple Test Node

```json
{
  "name": "YouTube Upload Test",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "method": "POST",
    "url": "https://www.googleapis.com/upload/youtube/v3/videos",
    "authentication": "oAuth2",
    "queryParameters": {
      "parameters": [
        {
          "name": "part",
          "value": "snippet,status"
        }
      ]
    },
    "sendBody": true,
    "contentType": "multipart-form-data",
    "bodyParameters": {
      "parameters": [
        {
          "name": "snippet",
          "value": {
            "title": "Test Short #Shorts",
            "description": "Test upload from n8n",
            "categoryId": "22"
          }
        },
        {
          "name": "status",
          "value": {
            "privacyStatus": "private"
          }
        }
      ]
    }
  },
  "credentials": {
    "googleOAuth2Api": {
      "id": "your-credential-id",
      "name": "YouTube Upload OAuth2"
    }
  }
}
```

---

## Upload Short Format Requirements

### Video Specifications
- **Duration:** < 60 seconds
- **Aspect Ratio:** 9:16 (vertical)
- **Resolution:** Minimal 1080x1920
- **Format:** MP4, MOV, AVI, FLV, WMV
- **Max Size:** 256GB (untuk upload API)

### Metadata Requirements
```json
{
  "snippet": {
    "title": "Your Title #Shorts",
    "description": "Description\n\n#Shorts #Crypto #Web3",
    "tags": ["Shorts", "Crypto", "Web3"],
    "categoryId": "22"
  },
  "status": {
    "privacyStatus": "public",
    "selfDeclaredMadeForKids": false
  }
}
```

**PENTING:** Title atau description HARUS include `#Shorts` hashtag!

---

## Quota & Limits

### Free Tier Limits
- **Daily Quota:** 10,000 units/day
- **Upload cost:** 1,600 units per video
- **Max uploads/day:** ~6 videos

### Request Quota Increase
1. Go to: https://console.cloud.google.com/apis/api/youtube.googleapis.com/quotas
2. Klik nama quota yang mau di-increase
3. Klik "Edit Quotas"
4. Fill form dan submit request
5. Google review 2-5 hari kerja

---

## Common Issues & Solutions

### Error: "Access Not Configured"
**Solution:**
- Pastikan YouTube Data API v3 sudah enabled
- Tunggu 5-10 menit setelah enable

### Error: "The user has not granted the app..."
**Solution:**
- Tambahkan scope yang benar di OAuth consent
- Re-authorize credential di n8n

### Error: "The app is in test mode"
**Solution:**
- Tambahkan email user ke Test Users di OAuth consent screen
- Atau publish app ke production (requires verification)

### Error: "Quota exceeded"
**Solution:**
- Tunggu 24 jam (quota reset midnight PST)
- Request quota increase

### Video tidak muncul sebagai Short
**Solution:**
- Pastikan duration < 60 detik
- Aspect ratio harus 9:16
- Include #Shorts di title atau description

---

## Production Checklist

- [ ] Google Cloud project created
- [ ] YouTube Data API v3 enabled
- [ ] OAuth consent screen configured
- [ ] Test users added (untuk External app)
- [ ] OAuth client ID created
- [ ] Redirect URIs configured correctly
- [ ] n8n credential created dan connected
- [ ] Test upload berhasil
- [ ] Video format memenuhi Shorts requirements
- [ ] Quota monitoring setup

---

## Security Best Practices

1. **Never commit credentials** ke git
2. **Use environment variables** untuk sensitive data
3. **Rotate secrets** secara berkala
4. **Monitor API usage** di Google Cloud Console
5. **Set up quota alerts** untuk prevent overuse

---

## Helpful Links

- Google Cloud Console: https://console.cloud.google.com/
- YouTube API Docs: https://developers.google.com/youtube/v3
- OAuth 2.0 Guide: https://developers.google.com/youtube/v3/guides/auth/server-side-web-apps
- Quota Calculator: https://developers.google.com/youtube/v3/determine_quota_cost
- Shorts Guidelines: https://support.google.com/youtube/answer/10059070

---

**Next:** [TikTok API Setup](API-SETUP-TIKTOK.md)
