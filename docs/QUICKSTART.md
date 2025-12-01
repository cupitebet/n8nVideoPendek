# ⚡ Quick Start - Blueprint V2

**Target: Dari nol ke video pertama dalam 30 menit**

---

## Prerequisites

Sebelum mulai, pastikan Anda punya:

1. ✅ **Google Cloud Account** dengan billing enabled
2. ✅ **n8n** (Cloud atau self-hosted)
3. ✅ Akses ke:
   - Vertex AI API
   - Gemini API
   - Cloud Text-to-Speech API (optional)

---

## Step 1: Setup Google Cloud (15 menit)

### 1.1 Enable APIs

```bash
# Login ke Google Cloud Console
# https://console.cloud.google.com

# Enable APIs yang dibutuhkan:
# - Vertex AI API
# - Generative Language API (Gemini)
# - Cloud Text-to-Speech API
```

Di Console:
1. Pergi ke **APIs & Services** > **Enable APIs**
2. Search dan enable:
   - ✅ Vertex AI API
   - ✅ Generative Language API
   - ✅ Cloud Text-to-Speech API

### 1.2 Create Service Account

```bash
# Di Google Cloud Console:
# IAM & Admin > Service Accounts > Create Service Account

# Berikan permissions:
# - Vertex AI User
# - Cloud Storage Admin (untuk menyimpan video)
# - Service Account Token Creator
```

### 1.3 Download JSON Key

1. Klik service account yang baru dibuat
2. **Keys** tab > **Add Key** > **Create New Key**
3. Pilih **JSON**
4. Download file (simpan dengan aman!)

---

## Step 2: Setup n8n (5 menit)

### 2.1 Install n8n

**Via Docker (Recommended):**
```bash
docker run -d --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

**Via npm:**
```bash
npm install n8n -g
n8n start
```

### 2.2 Access n8n

Buka browser: `http://localhost:5678`

---

## Step 3: Import Workflow (5 menit)

### 3.1 Download Workflow

File workflow ada di: `workflows/youtube-shorts-veo-v2.json`

### 3.2 Import ke n8n

1. Di n8n, klik **Workflows** menu
2. Klik **Import from File**
3. Upload `youtube-shorts-veo-v2.json`
4. Workflow akan terbuka otomatis

### 3.3 Setup Credentials

**Google Cloud Credential:**
1. Klik node "Gemini 1.5 Pro" atau "Veo 3.1"
2. Di credential dropdown, pilih **Create New**
3. Pilih **Google Service Account**
4. Upload JSON key yang sudah didownload di Step 1.3

**Ulangi untuk semua node Google:**
- ✅ Gemini 1.5 Pro (The Director)
- ✅ Gemini 2.5 Flash Image (Image Generator)
- ✅ Veo 3.1 (Video Generator)
- ✅ Cloud TTS (Text-to-Speech) - optional

---

## Step 4: Test Run (5 menit)

### 4.1 Set Input Topic

1. Cari node **"Manual Trigger"** di awal workflow
2. Klik node tersebut
3. Di bagian **Parameters**, isi:
   - **topic**: `"AI Revolution in Gaming"`

### 4.2 Execute Workflow

1. Klik tombol **Execute Workflow** (pojok kanan bawah)
2. Tunggu proses berjalan (3-5 menit)
3. Lihat output di setiap node

### 4.3 Check Results

Hasil yang diharapkan:

**Node 1 (The Director):**
```json
{
  "title": "AI Mengubah Gaming Selamanya!",
  "scenes": [
    {
      "scene_number": 1,
      "duration_seconds": 3,
      "voiceover_script": "...",
      "visual_prompt": "...",
      "sound_effect_prompt": "..."
    }
  ]
}
```

**Node 2 (Gemini Flash Image):**
- ✅ Character reference images generated
- ✅ Scene images created

**Node 3 (Veo 3.1):**
- ✅ Video clips generated (with audio!)
- ✅ URLs to download videos

**Node 5 (Final Assembly):**
- ✅ Complete 60-second video
- ✅ With subtitles and background music

---

## Troubleshooting

### Error: "API not enabled"
- ✅ Pastikan semua APIs sudah di-enable di Google Cloud Console
- ✅ Tunggu 1-2 menit setelah enable

### Error: "Quota exceeded"
- ✅ Check quota limits di Google Cloud Console
- ✅ Request quota increase jika perlu

### Error: "Permission denied"
- ✅ Pastikan Service Account punya permissions yang benar
- ✅ Re-create credential di n8n dengan JSON key yang baru

### Video tidak generate
- ✅ Check output Node 1 - pastikan JSON valid
- ✅ Check Node 2 - pastikan images ter-generate
- ✅ Check Veo 3.1 node configuration

---

## Next Steps

Setelah berhasil test run pertama:

1. 📖 **Customize Prompts** - Lihat [Prompt Guide](PROMPT-GUIDE.md)
2. 🎨 **Character Consistency** - Lihat [Character Guide](CHARACTER-CONSISTENCY.md)
3. ⚙️ **Optimize Settings** - Lihat [Configuration Guide](CONFIGURATION.md)
4. 🚀 **Production Setup** - Lihat [Production Guide](PRODUCTION-SETUP.md)

---

## Cost Estimate untuk Test Run

- Gemini 1.5 Pro (1 request): ~$0.001
- Gemini 2.5 Flash Image (5 images): ~$0.0025
- Veo 3.1 (5 video clips): ~$0.50
- Cloud TTS (500 chars): ~$0.008
- **Total per test**: ~$0.51

**Sangat murah untuk test!** 🎉

---

## Support

Butuh bantuan?
- 📖 [Full Documentation](README.md)
- 🐛 [Troubleshooting Guide](TROUBLESHOOTING.md)
- 💬 [GitHub Issues](https://github.com/cupitebet/n8nVideoPendek/issues)

---

**Selamat! Video pertama Anda siap! 🎬**
