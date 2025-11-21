# Scene-by-Scene Auto Video Generation

Guide lengkap untuk otomatis generate video dari script per scene (5-8 detik), lalu gabung jadi 1 video 60 detik.

---

## 🎯 Goal

**Input:**
```javascript
{
  "scenes": [
    { "duration": "3s", "narration": "Hook...", "visual": "..." },
    { "duration": "17s", "narration": "Content 1...", "visual": "..." },
    { "duration": "20s", "narration": "Content 2...", "visual": "..." },
    { "duration": "15s", "narration": "Content 3...", "visual": "..." },
    { "duration": "5s", "narration": "CTA...", "visual": "..." }
  ]
}
```

**Output:**
- 1 MP4 file, 60 detik, 1080x1920
- Each scene auto-generated dengan visual + voice
- Auto-combined into seamless video

---

## 🤖 Solution: Pictory.ai Scene-by-Scene

### How It Works

**Pictory Process:**
1. ✅ Takes script with scenes array
2. ✅ Generates video per scene (paralel/sequential)
3. ✅ Auto-selects stock footage based on visual cue
4. ✅ Adds AI voice-over per scene
5. ✅ Auto-combines all scenes into 1 video
6. ✅ Exports MP4 (1080x1920)

**Timeline:**
- Scene generation: 30-60 seconds per scene
- Total: 2-5 minutes untuk full 60-second video
- Fully automated, no manual intervention

---

## 📝 Step-by-Step Implementation

### Step 1: Prepare Scene Data

**n8n Function Node:**

```javascript
// Convert AI-generated script to Pictory scene format
const items = $input.all();
const processed = [];

for (const item of items) {
  const videoScript = item.json.generated_content.video_script;
  const research = item.json.research;

  // Build Pictory scenes array
  const pictoryScenes = [];

  // Hook scene
  pictoryScenes.push({
    sceneNumber: 1,
    text: videoScript.hook,
    duration: 3,  // seconds
    visualKeywords: "attention-grabbing, dynamic, modern",
    voiceSettings: {
      speed: 1.1,  // Slightly faster for hook
      emphasis: "high"
    }
  });

  // Content scenes
  videoScript.scenes.forEach((scene, index) => {
    // Parse duration "3-20s" → 17 seconds
    const durationMatch = scene.duration.match(/(\d+)-(\d+)s/);
    const duration = durationMatch ?
      parseInt(durationMatch[2]) - parseInt(durationMatch[1]) :
      10;

    pictoryScenes.push({
      sceneNumber: index + 2,
      text: scene.narration,
      duration: duration,
      visualKeywords: scene.visual_cue || scene.visual,
      voiceSettings: {
        speed: 1.0,
        emphasis: "medium"
      }
    });
  });

  // CTA scene
  pictoryScenes.push({
    sceneNumber: pictoryScenes.length + 1,
    text: videoScript.cta.narration || videoScript.cta,
    duration: 5,
    visualKeywords: "call to action, engagement, social media icons",
    voiceSettings: {
      speed: 1.0,
      emphasis: "high"
    }
  });

  // Calculate total duration
  const totalDuration = pictoryScenes.reduce((sum, scene) => sum + scene.duration, 0);

  processed.push({
    json: {
      ...item.json,
      pictory_payload: {
        title: research.project_name,
        totalDuration: totalDuration,
        scenes: pictoryScenes,
        globalSettings: {
          aspectRatio: "9:16",
          resolution: "1080x1920",
          voiceModel: "en-US-Neural2-D",  // Or Indonesian voice
          captionsEnabled: true,
          captionStyle: "modern_bold",
          musicTrack: "upbeat_tech",
          musicVolume: 0.25
        }
      }
    }
  });
}

return processed;
```

### Step 2: Call Pictory API

**HTTP Request Node:**

```json
{
  "method": "POST",
  "url": "https://api.pictory.ai/v1/video/script/scenes",
  "authentication": "httpHeaderAuth",
  "headers": {
    "Content-Type": "application/json"
  },
  "body": {
    "title": "={{ $json.pictory_payload.title }}",
    "scenes": "={{ $json.pictory_payload.scenes }}",
    "settings": "={{ $json.pictory_payload.globalSettings }}"
  }
}
```

**Request example:**
```json
{
  "title": "ChainGPT Analysis",
  "scenes": [
    {
      "sceneNumber": 1,
      "text": "Guys, ada AI yang bisa audit smart contract otomatis!",
      "duration": 3,
      "visualKeywords": "AI, technology, blockchain, attention-grabbing",
      "voiceSettings": { "speed": 1.1, "emphasis": "high" }
    },
    {
      "sceneNumber": 2,
      "text": "ChainGPT ini basically ChatGPT untuk blockchain...",
      "duration": 17,
      "visualKeywords": "ChainGPT logo, interface, smart contract code, AI visualization",
      "voiceSettings": { "speed": 1.0, "emphasis": "medium" }
    }
    // ... more scenes
  ],
  "settings": {
    "aspectRatio": "9:16",
    "voiceModel": "en-US-Neural2-D",
    "captionsEnabled": true
  }
}
```

**Response:**
```json
{
  "videoId": "vid_abc123",
  "status": "processing",
  "scenes": [
    {
      "sceneNumber": 1,
      "status": "processing",
      "estimatedTime": 30
    },
    {
      "sceneNumber": 2,
      "status": "queued",
      "estimatedTime": 45
    }
    // ...
  ],
  "estimatedTotalTime": 180  // 3 minutes
}
```

### Step 3: Poll Status (with Loop)

**Function Node - Check All Scenes Complete:**

```javascript
const videoId = $json.videoId;

// Check status
const statusResponse = await fetch(
  `https://api.pictory.ai/v1/video/${videoId}/status`,
  {
    headers: {
      'Authorization': 'Bearer YOUR_API_KEY'
    }
  }
);

const status = await statusResponse.json();

// Check if ALL scenes completed
const allComplete = status.scenes.every(scene => scene.status === 'completed');

if (allComplete) {
  return [{
    json: {
      ...$ json,
      status: 'ready_for_download',
      downloadUrl: status.downloadUrl
    }
  }];
} else {
  // Not ready yet, need to wait more
  return [{
    json: {
      ...$json,
      status: 'still_processing',
      completedScenes: status.scenes.filter(s => s.status === 'completed').length,
      totalScenes: status.scenes.length
    }
  }];
}
```

**If Node - Is Ready?**
- True → Go to download
- False → Wait 30s → Loop back to check status

### Step 4: Download Combined Video

**HTTP Request Node:**

```json
{
  "method": "GET",
  "url": "={{ $json.downloadUrl }}",
  "responseFormat": "file"
}
```

**Output:** Binary data (MP4 file, ~15-30MB)

---

## 🎨 Scene Visual Matching

### How Pictory Matches Stock Footage

**Your visual cue:**
```
"visual": "ChainGPT logo, blockchain visualization, smart contract code"
```

**Pictory AI:**
1. Parses keywords: ["ChainGPT", "blockchain", "smart contract", "code"]
2. Searches stock footage library
3. Matches relevant clips:
   - Blockchain network animation
   - Code on screen
   - Tech/AI visualizations
4. Selects best match per scene
5. Applies to scene timeline

**Pro tip - Be Specific:**

❌ Bad: `"visual": "technology"`
✅ Good: `"visual": "blockchain network nodes, animated, blue purple gradient, futuristic"`

❌ Bad: `"visual": "logos"`
✅ Good: `"visual": "DWF Labs logo, Polygon logo, Binance logo, professional corporate style"`

### Custom Footage (Advanced)

Upload your own clips:
```javascript
{
  "sceneNumber": 2,
  "text": "ChainGPT backed by DWF Labs...",
  "visualSource": "custom",
  "customVideoUrl": "https://your-cdn.com/dwf-labs-logo-animation.mp4",
  "duration": 5
}
```

---

## 🎙️ Voice-Over Settings

### Per-Scene Voice Control

```javascript
{
  "scenes": [
    {
      "text": "Guys, ada AI yang bisa audit smart contract!",
      "voiceSettings": {
        "speed": 1.1,        // Faster for excitement
        "pitch": 1.05,       // Slightly higher
        "emphasis": "high",  // Strong delivery
        "pause": {
          "afterComma": 0.3,
          "afterPeriod": 0.5
        }
      }
    },
    {
      "text": "ChainGPT ini basically ChatGPT untuk blockchain. Dia bisa bantu developer...",
      "voiceSettings": {
        "speed": 1.0,        // Normal speed
        "pitch": 1.0,        // Normal pitch
        "emphasis": "medium"
      }
    }
  ]
}
```

### Voice Model Options

**English:**
- `en-US-Neural2-D` (Male, natural)
- `en-US-Neural2-F` (Female, friendly)
- `en-US-Wavenet-A` (Male, professional)

**Indonesian (if available):**
- `id-ID-Wavenet-A` (Male)
- `id-ID-Wavenet-B` (Female)

**Or use your own audio:**
```javascript
{
  "sceneNumber": 1,
  "audioSource": "custom",
  "audioUrl": "https://your-cdn.com/voice-scene-1.mp3"
}
```

---

## 🎬 Complete n8n Workflow

### Full Flow Diagram

```
[Generate Script with Scenes]
    ↓
[Prepare Pictory Payload]
    ├─ Parse scenes array
    ├─ Extract duration per scene
    ├─ Format visual keywords
    └─ Set voice settings
    ↓
[Call Pictory API - Create Video]
    ├─ Upload scenes
    ├─ Start processing
    └─ Get video ID
    ↓
[Wait 30 seconds]
    ↓
[Check Processing Status] ←──┐
    ├─ All scenes done? ──────┘ (if not, loop back)
    └─ Yes? Continue
    ↓
[Download Combined MP4]
    ↓
[Upload to S3/Cloudinary]
    ↓
[Publish to Platforms]
```

### Node Count: ~12 nodes total

1. Parse Generated Content (existing)
2. **Prepare Pictory Scenes** (new Function node)
3. **Call Pictory API** (new HTTP Request)
4. **Wait 30s** (Wait node)
5. **Check Status** (HTTP Request + Function)
6. **Is Ready?** (IF node)
7. **Download Video** (HTTP Request)
8. Upload to Cloud (existing/new)
9-12. Platform Publishing (existing)

---

## 💰 Cost Breakdown

### Pictory Pricing

**Standard ($29/mo):**
- 30 videos/month
- Unlimited scenes per video
- All stock footage included
- AI voice included
- = $0.97 per video

**Premium ($59/mo):**
- 60 videos/month
- Priority processing
- = $0.98 per video

**Teams ($119/mo):**
- Unlimited videos
- Fastest processing
- Multiple users

### Per-Scene Costs

Pictory **doesn't charge per scene**, charges per final video!

So whether you have:
- 3 scenes → $0.97
- 5 scenes → $0.97
- 10 scenes → $0.97

Same price! ✅

---

## ⏱️ Processing Time

### Timeline Breakdown

**Single video (5 scenes):**
- Scene 1 (3s): ~30 seconds processing
- Scene 2 (17s): ~60 seconds processing
- Scene 3 (20s): ~70 seconds processing
- Scene 4 (15s): ~50 seconds processing
- Scene 5 (5s): ~20 seconds processing
- **Combining:** ~30 seconds
- **Total:** ~4-5 minutes

**Paralel processing** (if available):
- All scenes process simultaneously
- Total time: ~2-3 minutes

---

## 🔧 Error Handling

### Common Issues

**Scene too short (<3s):**
```javascript
// Auto-pad duration
const minDuration = 3;
scene.duration = Math.max(scene.duration, minDuration);
```

**Scene too long (>30s):**
```javascript
// Split into multiple scenes
if (scene.duration > 30) {
  const chunks = Math.ceil(scene.duration / 25);
  // Split narration, create multiple scenes
}
```

**Visual keywords too vague:**
```javascript
// Enhance keywords
const enhancedVisual = scene.visual + ", high quality, professional, modern, 4k";
```

**Voice-over doesn't match:**
```javascript
// Adjust speed to fit duration
const wordsPerSecond = scene.text.split(' ').length / scene.duration;
if (wordsPerSecond > 3) {
  scene.voiceSettings.speed = 1.2;  // Speed up
}
```

---

## 🚀 Ready to Implement?

**Next steps:**

1. **Read:** [Pictory API Integration Guide](VIDEO-GENERATION-PICTORY.md)
2. **Setup:** Pictory account + API key
3. **Copy:** Nodes above into your workflow
4. **Test:** With sample script
5. **Deploy:** Full automation!

**Expected result:**
- Input: Script dari AI (10 seconds workflow time)
- Processing: Pictory generates video (3-5 minutes)
- Output: MP4 file ready to publish
- **Total:** 5-10 minutes fully automated! 🎉

---

**Questions? Stuck? Need specific scenes help? Ask away!** 💬
