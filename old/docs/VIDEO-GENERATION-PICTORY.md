# Pictory.ai Integration for Automated Video Generation

Panduan lengkap integrate Pictory.ai dengan n8n untuk auto-generate videos dari script.

---

## Overview

**Pictory.ai** adalah text-to-video platform yang bisa:
- Convert script menjadi video otomatis
- Auto-select stock footage yang relevan
- Add AI voice-over
- Add captions/subtitles
- Export ke MP4

**Perfect untuk:** Full automation dari text → video

---

## Step 1: Sign Up Pictory.ai

1. **Go to Pictory.ai**
   - URL: https://pictory.ai/
   - Click "Get Started"

2. **Choose Plan**
   - **Standard:** $29/month (30 videos)
   - **Premium:** $59/month (60 videos)
   - **Teams:** $119/month (unlimited)

3. **Get API Access**
   - Login to Pictory dashboard
   - Go to Settings > API
   - Generate API Key
   - Copy dan save

---

## Step 2: Understand Pictory API Workflow

Pictory menggunakan **multi-step process:**

### Step 1: Create Project (from script)
```bash
POST https://api.pictory.ai/v1/video/script
```

### Step 2: Check Status
```bash
GET https://api.pictory.ai/v1/video/{video_id}/status
```

### Step 3: Download Video
```bash
GET https://api.pictory.ai/v1/video/{video_id}/download
```

---

## Step 3: Setup in n8n

### Credential Setup

1. **n8n > Credentials > Add**
   - Type: "HTTP Header Auth"
   - Name: `Authorization`
   - Value: `Bearer YOUR_PICTORY_API_KEY`

---

## Step 4: n8n Workflow Nodes

### Node 1: Prepare Pictory Script

Add this AFTER "Parse Generated Content" node:

```json
{
  "parameters": {
    "jsCode": "// Convert AI-generated script to Pictory format\nconst items = $input.all();\nconst processed = [];\n\nfor (const item of items) {\n  const videoScript = item.json.generated_content.video_script;\n  \n  // Build Pictory scenes from our script\n  const scenes = videoScript.scenes.map((scene, index) => ({\n    sceneNumber: index + 1,\n    text: scene.narration,\n    duration: parseInt(scene.duration.replace('s', '')),\n    visualCue: scene.visual\n  }));\n  \n  // Pictory API format\n  const pictoryPayload = {\n    title: item.json.research.project_name,\n    script: [\n      {\n        text: videoScript.hook,\n        duration: 3\n      },\n      ...scenes,\n      {\n        text: videoScript.cta.narration,\n        duration: 5\n      }\n    ],\n    settings: {\n      aspectRatio: \"9:16\",\n      voiceOver: {\n        enabled: true,\n        voice: \"en-US-Neural2-D\",\n        speed: 1.0\n      },\n      captions: {\n        enabled: true,\n        style: \"modern\",\n        position: \"center\"\n      },\n      background: {\n        type: \"stock\",\n        theme: \"tech\",\n        keyword: item.json.research.category\n      },\n      music: {\n        enabled: true,\n        genre: \"upbeat\",\n        volume: 0.3\n      }\n    }\n  };\n  \n  processed.push({\n    json: {\n      ...item.json,\n      pictory_payload: pictoryPayload\n    }\n  });\n}\n\nreturn processed;"
  },
  "name": "Prepare Pictory Script",
  "type": "n8n-nodes-base.code",
  "typeVersion": 2,
  "position": [2100, 300],
  "id": "prepare-pictory-script"
}
```

### Node 2: Create Pictory Video

```json
{
  "parameters": {
    "method": "POST",
    "url": "https://api.pictory.ai/v1/video/script",
    "authentication": "genericCredentialType",
    "genericAuthType": "httpHeaderAuth",
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
          "name": "title",
          "value": "={{ $json.pictory_payload.title }}"
        },
        {
          "name": "script",
          "value": "={{ $json.pictory_payload.script }}"
        },
        {
          "name": "settings",
          "value": "={{ $json.pictory_payload.settings }}"
        }
      ]
    },
    "options": {}
  },
  "name": "Create Pictory Video",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "position": [2300, 300],
  "id": "create-pictory-video",
  "credentials": {
    "httpHeaderAuth": {
      "id": "pictory-credential",
      "name": "Pictory API"
    }
  }
}
```

**Response:**
```json
{
  "videoId": "vid_abc123xyz",
  "status": "processing",
  "estimatedTime": 120
}
```

### Node 3: Wait for Processing

```json
{
  "parameters": {
    "amount": 2,
    "unit": "minutes"
  },
  "name": "Wait for Pictory Processing",
  "type": "n8n-nodes-base.wait",
  "typeVersion": 1,
  "position": [2450, 300],
  "id": "wait-pictory"
}
```

### Node 4: Check Video Status (Loop)

```json
{
  "parameters": {
    "method": "GET",
    "url": "=https://api.pictory.ai/v1/video/{{ $('Create Pictory Video').item.json.videoId }}/status",
    "authentication": "genericCredentialType",
    "genericAuthType": "httpHeaderAuth"
  },
  "name": "Check Pictory Status",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "position": [2600, 300],
  "id": "check-pictory-status"
}
```

### Node 5: Conditional - Is Ready?

```json
{
  "parameters": {
    "conditions": {
      "string": [
        {
          "value1": "={{ $json.status }}",
          "operation": "equals",
          "value2": "completed"
        }
      ]
    }
  },
  "name": "Is Video Ready?",
  "type": "n8n-nodes-base.if",
  "typeVersion": 2,
  "position": [2750, 300],
  "id": "check-video-ready"
}
```

- **True output** → Continue to download
- **False output** → Loop back to "Wait" node (with counter to prevent infinite loop)

### Node 6: Download Video

```json
{
  "parameters": {
    "method": "GET",
    "url": "=https://api.pictory.ai/v1/video/{{ $('Create Pictory Video').item.json.videoId }}/download",
    "authentication": "genericCredentialType",
    "genericAuthType": "httpHeaderAuth",
    "options": {
      "response": {
        "response": {
          "responseFormat": "file"
        }
      }
    }
  },
  "name": "Download Pictory Video",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "position": [2900, 300],
  "id": "download-pictory-video"
}
```

**Output:** Binary data (MP4 file)

### Node 7: Upload to Cloud Storage

Now upload the video file ke S3/Cloudinary (sudah ada di previous guides)

---

## Alternative: Pictory Webhook (Better!)

Instead of polling status, use webhook:

### Setup Webhook in Pictory

1. Pictory Dashboard > Settings > Webhooks
2. Add webhook URL: `https://your-n8n.com/webhook/pictory-complete`
3. Select event: `video.completed`

### n8n Webhook Node

```json
{
  "parameters": {
    "httpMethod": "POST",
    "path": "pictory-complete",
    "responseMode": "responseNode",
    "options": {}
  },
  "name": "Pictory Webhook - Video Complete",
  "type": "n8n-nodes-base.webhook",
  "typeVersion": 1,
  "position": [2900, 300],
  "id": "pictory-webhook"
}
```

**Webhook receives:**
```json
{
  "videoId": "vid_abc123xyz",
  "status": "completed",
  "downloadUrl": "https://cdn.pictory.ai/videos/vid_abc123xyz.mp4",
  "duration": 58,
  "size": 15728640
}
```

Then directly download dan upload ke platforms!

---

## Complete Workflow Flow

```
[Existing workflow...]
    ↓
[Parse Generated Content]
    ↓
[Prepare Pictory Script]
    ↓
[Create Pictory Video] → Returns videoId
    ↓
[Wait 2 minutes]
    ↓
[Check Status] → Loop until completed
    ↓
[Download Video]
    ↓
[Upload to S3/Cloudinary]
    ↓
[Multi-Platform Upload]
    ↓
[Archive & Notify]
```

---

## Script Format Example

Your AI generates this:
```javascript
{
  "hook": "Guys, ada AI yang bisa audit smart contract otomatis!",
  "scenes": [
    {
      "duration": "3-20s",
      "narration": "ChainGPT ini basically ChatGPT untuk blockchain...",
      "visual": "Show ChainGPT interface"
    }
  ]
}
```

Pictory needs this:
```javascript
{
  "script": [
    {
      "text": "Guys, ada AI yang bisa audit smart contract otomatis!",
      "duration": 3
    },
    {
      "text": "ChainGPT ini basically ChatGPT untuk blockchain...",
      "duration": 17
    }
  ],
  "settings": {
    "aspectRatio": "9:16",
    "voiceOver": { "enabled": true }
  }
}
```

---

## Cost Calculation

**Standard Plan ($29/month):**
- 30 videos/month
- ~$1 per video
- Perfect untuk daily posting (1 video/day)

**Premium Plan ($59/month):**
- 60 videos/month
- ~$1 per video
- Good untuk 2-3 videos/day

**Teams Plan ($119/month):**
- Unlimited videos
- Best untuk high volume

---

## Limitations & Workarounds

### Issue: Processing Time
**Problem:** Pictory takes 2-5 minutes per video
**Solution:**
- Use webhook instead of polling
- Process overnight untuk next day posts
- Queue system

### Issue: Cost
**Problem:** $29-119/month ongoing cost
**Solution:**
- Start dengan Standard ($29)
- Optimize script quality untuk reduce retries
- Only generate for top-performing content

### Issue: Stock Footage Quality
**Problem:** Sometimes footage doesn't match perfectly
**Solution:**
- Provide clear visual cues di script
- Use `visualCue` field effectively
- Manual review before publishing (optional)

---

## Testing Workflow

### Test dengan Sample Script

1. **Disable semua nodes kecuali Pictory**
2. **Create manual trigger node**
3. **Add Set node dengan sample data:**

```json
{
  "generated_content": {
    "video_script": {
      "hook": "Test video generation with Pictory!",
      "scenes": [
        {
          "duration": "5s",
          "narration": "This is a test of automated video creation.",
          "visual": "Technology, AI, automation"
        }
      ],
      "cta": {
        "narration": "Like and subscribe for more!",
        "duration": "3s"
      }
    }
  },
  "research": {
    "project_name": "Test Video",
    "category": "Technology"
  }
}
```

4. **Execute dan wait untuk hasil**
5. **Check downloaded video**

---

## Production Checklist

- [ ] Pictory account created
- [ ] API key obtained
- [ ] Credential setup di n8n
- [ ] Test video generated successfully
- [ ] Webhook configured (recommended)
- [ ] Cloud storage integration working
- [ ] Error handling implemented
- [ ] Monitoring setup

---

## Alternatives to Pictory

Jika Pictory tidak cocok:

### Steve.ai
- Similar features
- $20-60/month
- API: https://steve.ai/api

### Synthesia
- AI Avatar videos
- More expensive ($30-90/month)
- Best untuk talking head style

### Lumen5
- Template-based
- $29-149/month
- Good social media templates

---

## Support

**Pictory Documentation:** https://docs.pictory.ai/
**Pictory Support:** support@pictory.ai
**API Status:** https://status.pictory.ai/

---

**Next:** [Add Video Nodes to Your Workflow](WORKFLOW-VIDEO-INTEGRATION.md)
