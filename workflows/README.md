# Workflows - Blueprint V2

## Available Workflows

### 1. youtube-shorts-veo-v2.json (COMING SOON)

**Full automation workflow** untuk YouTube Shorts menggunakan:
- Google Veo 3.1 (video generation)
- Gemini 2.5 Flash Image (character images)
- Gemini 1.5 Pro (script generation)
- Creatomate (video assembly)

**Status:** 🚧 In development

**What it does:**
1. Input: Topic (e.g., "AI Gaming Revolution")
2. Generate script with Conflict Arc structure
3. Create character reference images
4. Generate 5 video clips dengan Veo 3.1
5. Add voiceover dengan Cloud TTS
6. Assemble final video dengan subtitles
7. Output: 60-second YouTube Short ready to upload

---

### 2. youtube-shorts-manual.json (COMING SOON)

**Semi-manual workflow** untuk beginners.

**What it does:**
1. Input: Topic
2. Generate script
3. Generate character images
4. Save to Google Drive
5. **STOP** - You manually create video

Good for learning the process!

---

### 3. character-generator.json (COMING SOON)

**Standalone workflow** untuk membuat character sheets.

**What it does:**
1. Input: Character description
2. Generate character sheet dengan multiple angles
3. Upload to Cloud Storage
4. Output: Character reference URL

---

## How to Import

1. Download workflow JSON file
2. Open n8n
3. Click **Workflows** menu
4. Click **Import from File**
5. Select JSON file
6. Click **Import**

---

## How to Use

### First Time Setup:
1. Import workflow
2. Configure Google Cloud credentials (see `config/google-credentials-setup.md`)
3. Update project ID di semua HTTP Request nodes
4. Update bucket name di Cloud Storage nodes
5. Test run dengan simple topic

### Regular Use:
1. Open workflow
2. Input topic di Manual Trigger node
3. Click **Execute Workflow**
4. Wait 3-5 minutes
5. Download final video from output node

---

## Customization

Edit these nodes to customize:

**Script Generation (The Director):**
- Edit system prompt di Gemini 1.5 Pro node
- Adjust temperature untuk more/less creative output

**Visual Style:**
- Edit character sheet template di Image Gen node
- Change art style keywords

**Video Settings:**
- Edit Veo 3.1 parameters (videoLength, aspectRatio)
- Adjust quality settings

**Audio:**
- Change TTS voice di Cloud TTS node
- Add background music di Creatomate template

---

## Troubleshooting

**Workflow fails at Gemini node:**
- Check credential configured
- Check API enabled di Google Cloud
- Check quota limits

**Workflow fails at Veo 3.1 node:**
- Check project ID correct
- Check bucket exists
- Check character reference image URL valid

**Final video has issues:**
- Check all 5 clips generated successfully
- Check Creatomate template configured
- Check subtitle generation working

---

## Old Workflows (Archived)

Old workflows moved to `old/workflows/`:
- `majelis-digital-marketing.json` - Old Majelis workflow
- `tuyul-digital-web3.json` - Old crypto content workflow

These are **NOT compatible** with Blueprint V2!

---

## Next Steps

1. ✅ [Setup Google Cloud](../docs/GOOGLE-CLOUD-SETUP.md)
2. ✅ [Import workflow](../docs/QUICKSTART.md)
3. ✅ [Test run](../docs/QUICKSTART.md#step-4-test-run)

---

**Happy automating! 🎬**
