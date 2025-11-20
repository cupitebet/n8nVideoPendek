# Adding Social Media Upload Nodes to Workflow

Panduan untuk menambahkan nodes upload video ke YouTube Shorts, TikTok, Instagram Reels, dan Facebook Reels ke workflow yang sudah ada.

---

## Overview

Dokumen ini menjelaskan cara extend workflow `tuyul-digital-web3.json` dengan menambahkan nodes untuk upload video ke semua platform social media.

**Flow baru:**
```
[Existing: Content Generation]
    ↓
[NEW: Video Creation from Script]
    ↓
[NEW: Upload Video to Cloud Storage]
    ↓
[NEW: Multi-Platform Upload (Parallel)]
    ├→ YouTube Shorts
    ├→ TikTok
    ├→ Instagram Reels
    ├→ Facebook Reels
    └→ Telegram Channel
    ↓
[Archive & Notification]
```

---

## Prerequisites

Sebelum menambahkan nodes, pastikan sudah setup:
- [ ] Semua API credentials (lihat docs/SOCIAL-MEDIA-COMPLETE-GUIDE.md)
- [ ] Cloud storage untuk host video (S3, Cloudinary, etc)
- [ ] Video generation solution (atau manual upload untuk testing)

---

## Node 1: Create Video Metadata

**Position:** Setelah "Parse Generated Content"

```json
{
  "parameters": {
    "jsCode": "// Prepare video metadata for all platforms\nconst items = $input.all();\nconst processed = [];\n\nfor (const item of items) {\n  const content = item.json.generated_content;\n  const research = item.json.research;\n  \n  // Generate video filename\n  const projectSlug = research.project_name\n    .toLowerCase()\n    .replace(/[^a-z0-9]/g, '-');\n  const timestamp = Date.now();\n  const filename = `${projectSlug}-${timestamp}.mp4`;\n  \n  // Prepare platform-specific metadata\n  processed.push({\n    json: {\n      ...item.json,\n      video_metadata: {\n        filename: filename,\n        title: content.article.title.substring(0, 100),\n        duration: 60, // seconds\n        \n        // YouTube Shorts\n        youtube: {\n          title: `${research.project_name} #Shorts`,\n          description: `${content.article.meta_description}\\n\\n#Shorts ${content.social_caption.hashtags.slice(0, 10).join(' ')}`,\n          tags: content.article.tags,\n          categoryId: \"22\"\n        },\n        \n        // TikTok\n        tiktok: {\n          caption: content.social_caption.caption.substring(0, 2200),\n          privacy: \"PUBLIC_TO_EVERYONE\",\n          disable_comment: false,\n          disable_duet: false\n        },\n        \n        // Instagram Reels\n        instagram: {\n          caption: content.social_caption.caption,\n          share_to_feed: true\n        },\n        \n        // Facebook Reels\n        facebook: {\n          title: research.project_name,\n          description: content.social_caption.caption\n        },\n        \n        // Telegram\n        telegram: {\n          caption: `🚀 *${research.project_name}*\\n\\n${content.social_caption.caption}`,\n          parse_mode: \"Markdown\"\n        }\n      }\n    }\n  });\n}\n\nreturn processed;"
  },
  "name": "Prepare Video Metadata",
  "type": "n8n-nodes-base.code",
  "typeVersion": 2,
  "position": [2100, 300],
  "id": "prepare-video-metadata"
}
```

---

## Node 2: Upload Video to Cloud Storage

**NOTE:** Untuk testing, Anda bisa skip node ini dan gunakan pre-uploaded video URL.

### Option A: AWS S3

```json
{
  "parameters": {
    "resource": "file",
    "operation": "upload",
    "bucketName": "my-shorts-bucket",
    "fileName": "={{ $json.video_metadata.filename }}",
    "binaryData": true,
    "additionalFields": {
      "acl": "public-read",
      "contentType": "video/mp4"
    }
  },
  "name": "Upload Video to S3",
  "type": "n8n-nodes-base.aws",
  "typeVersion": 1,
  "position": [2300, 300],
  "id": "upload-video-s3",
  "credentials": {
    "aws": {
      "id": "aws-credential",
      "name": "AWS S3 Credentials"
    }
  }
}
```

### Option B: Cloudinary (Simpler)

```json
{
  "parameters": {
    "method": "POST",
    "url": "=https://api.cloudinary.com/v1_1/{{ $credentials.cloudinaryApi.cloudName }}/video/upload",
    "sendBody": true,
    "contentType": "multipart-form-data",
    "bodyParameters": {
      "parameters": [
        {
          "name": "file",
          "value": "={{ $binary.data }}"
        },
        {
          "name": "public_id",
          "value": "={{ $json.video_metadata.filename.replace('.mp4', '') }}"
        },
        {
          "name": "folder",
          "value": "shorts"
        }
      ]
    }
  },
  "name": "Upload to Cloudinary",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "position": [2300, 300],
  "id": "upload-cloudinary",
  "credentials": {
    "cloudinaryApi": {
      "id": "cloudinary-credential",
      "name": "Cloudinary API"
    }
  }
}
```

---

## Node 3: Get Video URL

```json
{
  "parameters": {
    "jsCode": "// Extract video URL from upload response\nconst items = $input.all();\nconst processed = [];\n\nfor (const item of items) {\n  let videoUrl;\n  \n  // Check if S3 or Cloudinary response\n  if (item.json.secure_url) {\n    // Cloudinary response\n    videoUrl = item.json.secure_url;\n  } else if (item.json.Location) {\n    // S3 response\n    videoUrl = item.json.Location;\n  } else {\n    // Fallback - construct URL manually\n    videoUrl = `https://your-bucket.s3.amazonaws.com/${item.json.video_metadata.filename}`;\n  }\n  \n  processed.push({\n    json: {\n      ...item.json,\n      video_url: videoUrl,\n      ready_for_upload: true\n    }\n  });\n}\n\nreturn processed;"
  },
  "name": "Extract Video URL",
  "type": "n8n-nodes-base.code",
  "typeVersion": 2,
  "position": [2500, 300],
  "id": "extract-video-url"
}
```

---

## Nodes 4-8: Platform Upload (Parallel)

### Node 4: YouTube Shorts Upload

```json
{
  "parameters": {
    "method": "POST",
    "url": "https://www.googleapis.com/upload/youtube/v3/videos",
    "authentication": "oAuth2",
    "sendQuery": true,
    "queryParameters": {
      "parameters": [
        {
          "name": "part",
          "value": "snippet,status"
        },
        {
          "name": "uploadType",
          "value": "resumable"
        }
      ]
    },
    "sendHeaders": true,
    "headerParameters": {
      "parameters": [
        {
          "name": "Content-Type",
          "value": "application/json"
        },
        {
          "name": "X-Upload-Content-Type",
          "value": "video/mp4"
        }
      ]
    },
    "sendBody": true,
    "bodyParameters": {
      "parameters": [
        {
          "name": "snippet",
          "value": "={{ $json.video_metadata.youtube }}"
        },
        {
          "name": "status",
          "value": {
            "privacyStatus": "public",
            "selfDeclaredMadeForKids": false
          }
        }
      ]
    }
  },
  "name": "Upload to YouTube Shorts",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "position": [2700, 100],
  "id": "upload-youtube",
  "credentials": {
    "googleOAuth2Api": {
      "id": "youtube-oauth",
      "name": "YouTube OAuth2"
    }
  }
}
```

**NOTE:** YouTube upload requires 2 steps for resumable upload. Ini simplified version.

### Node 5: TikTok Upload (3-step process)

**Step 5a: Initialize TikTok Upload**

```json
{
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
          "value": "={{ $json.video_metadata.tiktok }}"
        },
        {
          "name": "source_info",
          "value": {
            "source": "PULL_FROM_URL",
            "video_url": "={{ $json.video_url }}"
          }
        }
      ]
    }
  },
  "name": "TikTok - Initialize Upload",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "position": [2700, 250],
  "id": "tiktok-init",
  "credentials": {
    "oAuth2Api": {
      "id": "tiktok-oauth",
      "name": "TikTok OAuth2"
    }
  }
}
```

**Step 5b: Publish TikTok Video**

```json
{
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
  },
  "name": "TikTok - Publish",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "position": [2900, 250],
  "id": "tiktok-publish",
  "credentials": {
    "oAuth2Api": {
      "id": "tiktok-oauth",
      "name": "TikTok OAuth2"
    }
  }
}
```

### Node 6: Instagram Reels Upload

**Step 6a: Create Instagram Container**

```json
{
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
          "value": "={{ $json.video_metadata.instagram.caption }}"
        },
        {
          "name": "share_to_feed",
          "value": "true"
        }
      ]
    }
  },
  "name": "Instagram - Create Container",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "position": [2700, 400],
  "id": "instagram-create",
  "credentials": {
    "httpQueryAuth": {
      "id": "instagram-credential",
      "name": "Instagram Graph API"
    }
  }
}
```

**Step 6b: Wait & Check Status**

```json
{
  "parameters": {
    "amount": 15,
    "unit": "seconds"
  },
  "name": "Wait for Instagram Processing",
  "type": "n8n-nodes-base.wait",
  "typeVersion": 1,
  "position": [2850, 400],
  "id": "instagram-wait"
}
```

**Step 6c: Publish Instagram Reel**

```json
{
  "parameters": {
    "method": "POST",
    "url": "=https://graph.facebook.com/v18.0/{{ $json.instagram_business_account_id }}/media_publish",
    "sendQuery": true,
    "queryParameters": {
      "parameters": [
        {
          "name": "creation_id",
          "value": "={{ $('Instagram - Create Container').item.json.id }}"
        }
      ]
    }
  },
  "name": "Instagram - Publish Reel",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "position": [3000, 400],
  "id": "instagram-publish"
}
```

### Node 7: Facebook Reels Upload

```json
{
  "parameters": {
    "method": "POST",
    "url": "=https://graph.facebook.com/v18.0/{{ $json.facebook_page_id }}/videos",
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
          "value": "={{ $json.video_metadata.facebook.description }}"
        },
        {
          "name": "title",
          "value": "={{ $json.video_metadata.facebook.title }}"
        },
        {
          "name": "is_crosspost_video",
          "value": "true"
        }
      ]
    }
  },
  "name": "Upload to Facebook Reels",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "position": [2700, 550],
  "id": "upload-facebook",
  "credentials": {
    "httpQueryAuth": {
      "id": "facebook-credential",
      "name": "Facebook Page API"
    }
  }
}
```

### Node 8: Telegram Channel Upload

```json
{
  "parameters": {
    "resource": "message",
    "operation": "sendVideo",
    "chatId": "={{ $json.telegram_channel_id }}",
    "binaryData": false,
    "videoId": "={{ $json.video_url }}",
    "additionalFields": {
      "caption": "={{ $json.video_metadata.telegram.caption }}",
      "parse_mode": "Markdown",
      "supports_streaming": true
    }
  },
  "name": "Post to Telegram Channel",
  "type": "n8n-nodes-base.telegram",
  "typeVersion": 1.2,
  "position": [2700, 700],
  "id": "upload-telegram",
  "credentials": {
    "telegramApi": {
      "id": "telegram-credential",
      "name": "Telegram Bot"
    }
  }
}
```

---

## Node 9: Merge Upload Results

```json
{
  "parameters": {
    "mode": "mergeByPosition"
  },
  "name": "Merge Platform Results",
  "type": "n8n-nodes-base.merge",
  "typeVersion": 2.1,
  "position": [3100, 400],
  "id": "merge-results"
}
```

Connect all upload nodes output to this merge node.

---

## Node 10: Archive Social Media URLs

```json
{
  "parameters": {
    "jsCode": "// Extract and format all platform URLs\nconst items = $input.all();\nconst processed = [];\n\nfor (const item of items) {\n  const urls = {\n    youtube: null,\n    tiktok: null,\n    instagram: null,\n    facebook: null,\n    telegram: null\n  };\n  \n  // Extract URLs from각 platform response\n  // YouTube\n  if (item.json.id && item.json.kind === 'youtube#video') {\n    urls.youtube = `https://youtube.com/shorts/${item.json.id}`;\n  }\n  \n  // TikTok\n  if (item.json.data?.share_url) {\n    urls.tiktok = item.json.data.share_url;\n  }\n  \n  // Instagram\n  if (item.json.id && item.json.instagram_business_account_id) {\n    urls.instagram = `https://www.instagram.com/reel/${item.json.id}`;\n  }\n  \n  // Facebook\n  if (item.json.id && !item.json.instagram_business_account_id) {\n    urls.facebook = `https://www.facebook.com/watch/?v=${item.json.id}`;\n  }\n  \n  // Telegram\n  if (item.json.message_id) {\n    urls.telegram = `https://t.me/${item.json.chat.username}/${item.json.message_id}`;\n  }\n  \n  processed.push({\n    json: {\n      ...item.json,\n      platform_urls: urls,\n      upload_timestamp: new Date().toISOString()\n    }\n  });\n}\n\nreturn processed;"
  },
  "name": "Format Platform URLs",
  "type": "n8n-nodes-base.code",
  "typeVersion": 2,
  "position": [3300, 400],
  "id": "format-urls"
}
```

---

## Node 11: Update Google Sheets with URLs

```json
{
  "parameters": {
    "operation": "append",
    "documentId": {
      "__rl": true,
      "mode": "list",
      "value": "={{ $json.google_sheet_id }}"
    },
    "sheetName": {
      "__rl": true,
      "mode": "list",
      "value": "Social Media Archive"
    },
    "columns": {
      "mappingMode": "defineBelow",
      "value": {
        "date": "={{ new Date().toISOString().split('T')[0] }}",
        "project_name": "={{ $json.research.project_name }}",
        "youtube_url": "={{ $json.platform_urls.youtube }}",
        "tiktok_url": "={{ $json.platform_urls.tiktok }}",
        "instagram_url": "={{ $json.platform_urls.instagram }}",
        "facebook_url": "={{ $json.platform_urls.facebook }}",
        "telegram_url": "={{ $json.platform_urls.telegram }}",
        "video_url": "={{ $json.video_url }}",
        "verdict": "={{ $json.research.verdict }}"
      }
    }
  },
  "name": "Archive Social URLs to Sheets",
  "type": "n8n-nodes-base.googleSheets",
  "typeVersion": 4.4,
  "position": [3500, 400],
  "id": "archive-social-sheets"
}
```

---

## Node 12: Send Success Notification

```json
{
  "parameters": {
    "chatId": "={{ $json.admin_telegram_id }}",
    "text": "=✅ *Multi-Platform Upload Success!*\n\n📋 Project: *{{ $json.research.project_name }}*\n📊 Verdict: {{ $json.research.verdict }}\n\n📱 *Platform Links:*\n{{#if $json.platform_urls.youtube}}\n▪️ YouTube: {{ $json.platform_urls.youtube }}\n{{/if}}\n{{#if $json.platform_urls.tiktok}}\n▪️ TikTok: {{ $json.platform_urls.tiktok }}\n{{/if}}\n{{#if $json.platform_urls.instagram}}\n▪️ Instagram: {{ $json.platform_urls.instagram }}\n{{/if}}\n{{#if $json.platform_urls.facebook}}\n▪️ Facebook: {{ $json.platform_urls.facebook }}\n{{/if}}\n{{#if $json.platform_urls.telegram}}\n▪️ Telegram: {{ $json.platform_urls.telegram }}\n{{/if}}\n\n⏰ {{ new Date().toLocaleString('id-ID', { timeZone: 'Asia/Jakarta' }) }} WIB",
    "additionalFields": {
      "parse_mode": "Markdown"
    }
  },
  "name": "Send Multi-Platform Success Notification",
  "type": "n8n-nodes-base.telegram",
  "typeVersion": 1.2,
  "position": [3700, 400],
  "id": "notification-multi-platform"
}
```

---

## Configuration Variables

Add these to your workflow atau buat Set node:

```json
{
  "parameters": {
    "values": {
      "string": [
        {
          "name": "instagram_business_account_id",
          "value": "17841400008460056"
        },
        {
          "name": "facebook_page_id",
          "value": "123456789012345"
        },
        {
          "name": "telegram_channel_id",
          "value": "@your_channel"
        },
        {
          "name": "admin_telegram_id",
          "value": "123456789"
        },
        {
          "name": "google_sheet_id",
          "value": "your-sheet-id"
        }
      ]
    },
    "options": {}
  },
  "name": "Platform Config",
  "type": "n8n-nodes-base.set",
  "typeVersion": 3,
  "position": [2250, 300],
  "id": "platform-config"
}
```

---

## Testing Strategy

### Phase 1: Test Individual Platforms

1. **Disable semua upload nodes kecuali satu**
2. **Test dengan manual trigger**
3. **Verify upload successful**
4. **Repeat untuk semua platform**

### Phase 2: Test Parallel Upload

1. **Enable 2-3 platforms**
2. **Test parallel execution**
3. **Check untuk race conditions**
4. **Verify all uploads successful**

### Phase 3: Full Integration

1. **Enable semua platforms**
2. **Test complete workflow end-to-end**
3. **Monitor errors dan performance**
4. **Optimize sebagaimana diperlukan**

---

## Error Handling

Add error handling nodes setelah setiap upload:

```json
{
  "parameters": {
    "conditions": {
      "boolean": [
        {
          "value1": "={{ $json.error }}",
          "operation": "exists"
        }
      ]
    }
  },
  "name": "Check Upload Success",
  "type": "n8n-nodes-base.if",
  "typeVersion": 2,
  "position": [2900, 100]
}
```

Connect "false" output to continue, "true" output to error notification.

---

## Performance Optimization

### 1. Parallel vs Sequential

**Parallel** (recommended untuk independent uploads):
```
All platforms upload simultaneously
Faster total time
```

**Sequential** (jika quota concerns):
```
Upload one by one
Safer for rate limits
Slower total time
```

### 2. Retry Logic

Add retry pada setiap upload node:

```json
{
  "continueOnFail": true,
  "retryOnFail": true,
  "maxTries": 3,
  "waitBetweenTries": 5000
}
```

### 3. Conditional Execution

Only upload to platforms yang enabled:

```json
{
  "parameters": {
    "conditions": {
      "boolean": [
        {
          "value1": "={{ $json.enable_youtube }}",
          "value2": true
        }
      ]
    }
  },
  "name": "Check YouTube Enabled"
}
```

---

## Next Steps

1. **Choose Video Generation Method**
   - Manual upload untuk testing
   - Automated dengan Pictory/Synthesia
   - Template-based dengan Canva

2. **Setup Cloud Storage**
   - AWS S3 (recommended)
   - Cloudinary (simpler)
   - Self-hosted

3. **Test Each Platform**
   - Follow individual API setup guides
   - Test upload dengan sample video
   - Verify posts visible

4. **Integrate to Main Workflow**
   - Import nodes above
   - Connect to existing workflow
   - Test end-to-end

5. **Monitor & Optimize**
   - Track success rates
   - Monitor API quotas
   - Adjust based on analytics

---

**Ready to integrate? Start with [YouTube Shorts](API-SETUP-YOUTUBE.md)!**
