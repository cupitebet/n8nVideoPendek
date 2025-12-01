# 🔧 Workflow Setup Guide - Blueprint V2

**Panduan lengkap setup dan konfigurasi n8n workflows**

---

## Overview

Blueprint V2 punya 2 workflows utama:

1. **youtube-shorts-veo-v2.json** - Full automation (Topic → Video)
2. **character-generator.json** - Generate character sheets

---

## Prerequisites

Sebelum import workflows, pastikan sudah:

- ✅ [Google Cloud setup complete](GOOGLE-CLOUD-SETUP.md)
- ✅ [n8n installed dan running](QUICKSTART.md#step-2-setup-n8n)
- ✅ Service account JSON key ready
- ✅ Cloud Storage bucket created

---

## Step 1: Setup Environment Variables

### 1.1 Copy Environment Template

```bash
cp config/environment-variables.env.example .env
```

### 1.2 Fill Values

Edit `.env` file:

```bash
# Required
GCP_PROJECT_ID=youtube-shorts-automation-xxxxx
GCS_BUCKET_NAME=youtube-shorts-assets-xxxxx

# Optional (for Creatomate integration)
CREATOMATE_API_KEY=your-key
CREATOMATE_TEMPLATE_ID=your-template-id
```

**Find your values:**
- **GCP_PROJECT_ID**: Google Cloud Console → Project dropdown
- **GCS_BUCKET_NAME**: Cloud Storage → Buckets list
- **CREATOMATE_API_KEY**: [Creatomate Dashboard](https://creatomate.com/dashboard)

### 1.3 Load in n8n

**Via Docker:**
```bash
docker run -d --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  --env-file .env \
  n8nio/n8n
```

**Via n8n Settings:**
1. Open n8n → Settings
2. Environment Variables
3. Add each variable manually

**Verify:**
```javascript
// Test di n8n Code node
console.log($env.GCP_PROJECT_ID);
// Should output your project ID
```

---

## Step 2: Import Workflows

### 2.1 Import Character Generator

1. Open n8n web interface
2. **Workflows** → **Import from File**
3. Select: `workflows/character-generator.json`
4. Click **Import**

### 2.2 Import Main Workflow

1. **Workflows** → **Import from File**
2. Select: `workflows/youtube-shorts-veo-v2.json`
3. Click **Import**

---

## Step 3: Configure Credentials

Setiap workflow butuh credentials untuk Google APIs.

### 3.1 Create Google Service Account Credential

1. Di workflow, click node yang punya **Google** icon
2. Click **Credential to connect with** dropdown
3. Select **Create New Credential**
4. Choose **Google Service Account**
5. Fill form:
   - **Name**: `Google Cloud - YouTube Shorts`
   - **Service Account Email**: (from your JSON key)
   - **Private Key**: Upload atau paste JSON key content
6. **Save**

### 3.2 Assign Credentials to Nodes

**Nodes yang butuh credentials:**

| Node Name | Credential Type | Notes |
|-----------|----------------|-------|
| The Director (Gemini 1.5 Pro) | Google Service Account | For Gemini API |
| Generate Scene Images | Google Service Account | For Gemini Flash Image |
| Upload to Cloud Storage | Google Cloud Storage OAuth2 | For GCS upload |
| Generate Video (Veo 3.1) | Google Service Account | For Vertex AI |
| Generate Voiceover | Google Cloud TTS OAuth2 | For Text-to-Speech |

**For each node:**
1. Click node
2. **Credential to connect with** dropdown
3. Select credential you created
4. Click **Save** (or **Update** jika edit mode)

---

## Step 4: Configure Workflow Settings

### 4.1 Update Project ID

**In nodes:**
- "Generate Video (Veo 3.1)"
- URL parameter uses `{{ $env.GCP_PROJECT_ID }}`
- Verify environment variable loaded

### 4.2 Update Bucket Name

**In nodes:**
- "Upload to Google Cloud Storage"
- Bucket parameter uses `{{ $env.GCS_BUCKET_NAME }}`

### 4.3 (Optional) Configure Creatomate

If using Creatomate for video assembly:

1. Sign up at [Creatomate](https://creatomate.com)
2. Create video template
3. Get API key dan template ID
4. Add to `.env`:
   ```
   CREATOMATE_API_KEY=your-key
   CREATOMATE_TEMPLATE_ID=template-id
   ```

**Alternative:** Manual video assembly dengan tools lain

---

## Step 5: Test Workflow

### 5.1 Test Character Generator First

**Why?** Simpler workflow, easier to debug.

1. Open **Character Generator** workflow
2. Click **Manual Trigger** node
3. Set test input:
   ```json
   {
     "character_name": "Test Character",
     "character_description": "young male with glasses and red hoodie"
   }
   ```
4. Click **Execute Workflow**
5. Check output:
   - ✅ Image generated?
   - ✅ Uploaded to GCS?
   - ✅ URI returned?

**Expected output:**
```json
{
  "status": "completed",
  "character_name": "Test Character",
  "storage_uri": "gs://your-bucket/characters/character_test_character_sheet.png",
  "public_url": "https://storage.googleapis.com/your-bucket/characters/...",
  "created_at": "2025-12-01T10:00:00.000Z"
}
```

### 5.2 Test Main Workflow

1. Open **YouTube Shorts - Blueprint V2** workflow
2. Click **Manual Trigger** node
3. Set test input:
   ```json
   {
     "topic": "AI in Gaming"
   }
   ```
4. Click **Execute Workflow**
5. Monitor progress:
   - ✅ Script generated (The Director)
   - ✅ Images generated (Gemini Flash)
   - ✅ Videos generated (Veo 3.1)
   - ✅ Final assembly (Creatomate)

**This will take 3-5 minutes!**

---

## Troubleshooting

### Error: "Environment variable not found"

**Solution:**
```bash
# Check if .env loaded
echo $GCP_PROJECT_ID

# Restart n8n
docker restart n8n

# Or reload environment
```

### Error: "Invalid credentials"

**Solution:**
1. Check JSON key valid
2. Re-create credential in n8n
3. Verify service account has correct roles
4. Check APIs enabled

### Error: "Bucket not found"

**Solution:**
1. Verify bucket exists: `gsutil ls`
2. Check bucket name spelling
3. Verify service account has Storage Admin role

### Error: "Quota exceeded"

**Solution:**
1. Check quota limits: Google Cloud Console → IAM → Quotas
2. Request quota increase
3. Or reduce frequency

### Workflow fails at specific node

**Debug steps:**
1. Click failed node
2. Check **Input** tab - what data received?
3. Check **Output** tab - what error message?
4. Check **Parameters** - are expressions evaluating correctly?
5. Use **Code** node to console.log debug info

---

## Optimization Tips

### 1. Batch Processing

For multiple videos:

1. Add **Loop** node before main workflow
2. Input array of topics
3. Process sequentially to avoid quota issues

### 2. Error Handling

Add **Error Trigger** nodes:

1. Catch failures
2. Retry logic
3. Send notifications (email, Slack)

### 3. Cost Control

Monitor costs:

1. Add **Code** node to track API calls
2. Log to Google Sheets
3. Set daily limits in `.env`

### 4. Parallel Processing

For faster execution:

1. Generate all images in parallel
2. Then process videos sequentially (quota limits)

---

## Workflow Diagram

```
INPUT: Topic
    ↓
┌──────────────────────────────────────┐
│ 1. THE DIRECTOR                      │
│    (Gemini 1.5 Pro)                  │
│    Generate script with scenes       │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│ 2. SPLIT SCENES                      │
│    Individual scene processing       │
└──────────────┬───────────────────────┘
               ↓
         ┌─────┴─────┐
         │ For Each  │
         │   Scene   │
         └─────┬─────┘
               ↓
┌──────────────────────────────────────┐
│ 3. GENERATE IMAGE                    │
│    (Gemini 2.5 Flash Image)          │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│ 4. UPLOAD TO GCS                     │
│    Store for Veo reference           │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│ 5. GENERATE VIDEO                    │
│    (Veo 3.1)                          │
│    - Image-to-video                  │
│    - Native audio                    │
└──────────────┬───────────────────────┘
               ↓
         ┌─────┴─────┐
         │ Aggregate │
         │All Scenes │
         └─────┬─────┘
               ↓
┌──────────────────────────────────────┐
│ 6. GENERATE VOICEOVER                │
│    (Cloud Text-to-Speech)            │
└──────────────┬───────────────────────┘
               ↓
┌──────────────────────────────────────┐
│ 7. ASSEMBLE FINAL VIDEO              │
│    (Creatomate)                      │
│    - Combine clips                   │
│    - Add subtitles                   │
│    - Add music                       │
└──────────────┬───────────────────────┘
               ↓
OUTPUT: Final Video URL
```

---

## Advanced Configuration

### Custom Veo 3.1 Parameters

Edit node "Generate Video (Veo 3.1)":

```json
{
  "parameters": {
    "sampleCount": 1,
    "videoLength": "8s",  // Change duration
    "aspectRatio": "1:1", // Change aspect ratio
    "imageControlMode": "character_reference",
    "guidanceScale": 7.5, // Add custom parameters
    "numInferenceSteps": 50
  }
}
```

### Custom Gemini Temperature

Edit node "The Director":

```json
{
  "generationConfig": {
    "temperature": 0.9,  // More creative
    "topK": 40,
    "topP": 0.95
  }
}
```

### Custom TTS Voice

Edit node "Generate Voiceover":

```javascript
{
  "voice": "en-US-Neural2-J",  // Different voice
  "speakingRate": 1.2,          // Faster
  "pitch": 0.5                  // Higher pitch
}
```

---

## Next Steps

After workflows are running:

1. ✅ [Master Character Consistency](CHARACTER-CONSISTENCY.md)
2. ✅ Optimize prompts untuk better output
3. ✅ Create character library
4. ✅ Set up monitoring & alerts
5. ✅ Scale to batch processing

---

## Resources

- 📖 [n8n Documentation](https://docs.n8n.io/)
- 📖 [Gemini API Docs](https://ai.google.dev/gemini-api/docs)
- 📖 [Veo 3.1 API Reference](https://cloud.google.com/vertex-ai/docs/generative-ai/video)
- 💬 [n8n Community](https://community.n8n.io/)

---

**Workflows ready! Time to create videos! 🎬**
