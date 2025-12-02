# 🎬 Project Blueprint V2: YouTube Shorts Automasi

**Update Desember 2025: Menggunakan CometAPI dengan Veo 3 dan Gemini 2.5 Flash Image ("Nano Banana") untuk alur kerja yang lebih ringkas dan hemat biaya.**

## 🚀 Quick Start

**Setup Time: 5 minutes | Single API Key | Simple Billing**

✅ Easiest setup - just one API key
✅ Access to Gemini 1.5 Pro, Gemini 2.5 Flash Image, Veo 3
✅ Pay-as-you-go dengan credit system
✅ No complex setup needed

**[→ Start Here: CometAPI Setup Guide](docs/COMETAPI-SETUP.md)** ⭐

---

## 📋 Daftar Isi

- [Quick Start](#-quick-start) ⭐ **Start Here**
- [Perubahan Strategi Utama](#1-perubahan-strategi-utama)
- [Arsitektur Workflow](#2-arsitektur-workflow-n8n-flow)
- [Perbandingan Alat](#3-perbandingan-alat-tech-stack-update)
- [Langkah Taktis Memulai](#4-langkah-taktis-memulai-untuk-pemula-n8n)
- [Prompt Rahasia](#5-prompt-rahasia-untuk-nano-banana-gemini-flash-image)
- [Detail Implementasi Teknis](#6-detail-implementasi-teknis-prompts--json)
- [Quick Links](#-quick-links)

---

## 1. Perubahan Strategi Utama

Dengan **Veo 3**, kita bisa memangkas kerumitan.

### Dulu
```
Text → Image → Video (Bisu) → Generate Suara Terpisah → Edit Gabung
```

### Sekarang (Veo 3)
```
Text/Image → Video + Audio (SFX & Ambience) sekaligus
```

**Keuntungan:**
- ✅ Lebih cepat (1 step vs 5 steps)
- ✅ Lebih murah (satu API call)
- ✅ Audio terintegrasi (sound effects + ambience)
- ✅ Konsistensi lebih baik

---

## 2. Arsitektur Workflow (n8n Flow)

### Tahap 1: The Director

**Trigger:** Topik Manual / Tren

**Node 1 (LLM - Gemini 1.5 Pro via CometAPI):**
- **Tugas:** Menulis naskah dengan struktur **Conflict Arc**
- **Penting:** Minta output prompt visual yang sangat spesifik untuk konsistensi karakter
  - Contoh: *"A young gamer boy with a red hoodie, anime style"*

### Tahap 2: The Visual Factory

**Node 2: Image Gen (Karakter & Aset)**

- **Model:** Gemini 2.5 Flash Image ("Nano Banana") via CometAPI
- **Kenapa ini?**
  - ✅ Sangat cepat
  - ✅ Murah (~$0.002 per image)
  - ✅ Punya fitur **"Subject Consistency"**
- **Cara Kerja:**
  1. Generate 1 gambar karakter utama
  2. Pakai gambar itu sebagai referensi untuk shot selanjutnya
  3. Wajah karakter tidak berubah-ubah (masalah utama video AI)

**Node 3: Video Gen (The Engine)**

- **Model:** Veo 3 via CometAPI

**Fitur Wajib Pakai:**
1. **Image-to-Video:** Masukkan gambar dari Node 2
2. **Native Audio Generation:** Veo 3 otomatis membuat:
   - Suara langkah kaki
   - Ledakan
   - Angin
   - Sound effects lainnya sesuai video
3. **Prompting:** `"Cinematic shot, [Action], high quality audio"`

### Tahap 3: The Assembly (Penyatuan)

Veo 3 menghasilkan klip-klip terpisah (5-10 detik per klip).

**Output Final:**
- Workflow menghasilkan video URLs untuk setiap scene
- Download dan combine manual di video editor (CapCut, DaVinci Resolve)
- Atau gunakan Creatomate untuk automated assembly (optional)

---

## 3. Perbandingan Alat (Tech Stack Update)

| Fungsi | Alat Lama | Pilihan Baru (CometAPI) |
|--------|-----------|-------------------------|
| **Image Model** | Flux / Midjourney | **Gemini 2.5 Flash Image** ("Nano Banana")<br/>✅ Single API, easy integration |
| **Video Model** | Runway Gen-3 | **Veo 3 via CometAPI**<br/>✅ Menang di Audio & Konsistensi |
| **Konsistensi** | Latih LoRA (Rumit) | **Character Reference Images**<br/>✅ Upload gambar karakter, Veo akan maintain consistency |
| **Biaya** | Mahal ($90+/bulan untuk berbagai sub) | **Pay-as-you-go via CometAPI**<br/>✅ ~$0.50-1.00 per video |
| **Setup** | Complex (multiple APIs) | **Single API key**<br/>✅ 5 minutes setup |

---

## 4. Langkah Taktis Memulai (Untuk Pemula n8n)

Jika Anda merasa workflow n8n rumit, lakukan ini bertahap:

### Level 1 (Semi-Manual)
1. Gunakan **CometAPI Playground** di browser untuk testing
2. Generate gambar pakai **Gemini 2.5 Flash** ("Nano Banana")
3. Generate video pakai **Veo 3**
4. Edit manual di **CapCut**

**Lakukan ini untuk 5 video pertama agar paham polanya.**

### Level 2 (Automasi Aset)
1. Buat workflow n8n sederhana:
   - Input Topik
   - Generate 5 Prompt Gambar
   - Generate 5 Gambar (Gemini Flash)
   - Save URLs
2. Anda tinggal download gambar, lalu animasikan dengan Veo 3

### Level 3 (Full Auto)
- Import workflow lengkap dari `workflows/youtube-shorts-cometapi.json`
- Tinggal input topic, klik Execute, tunggu hasil! 🎉

---

## 5. Prompt Rahasia untuk "Nano Banana" (Gemini Flash Image)

Agar gambar konsisten, gunakan struktur prompt ini di n8n:

```
Character Sheet of [Character Name], [Description], distinct facial features,
wearing [Clothing], front view, side view, back view, flat lighting, neutral background.
```

**Contoh:**
```
Character Sheet of Alex the Gamer, young Asian male with short black hair,
distinct round glasses, wearing red gaming hoodie with logo, front view, side view,
back view, flat lighting, neutral background.
```

**Gunakan hasil "Character Sheet" ini sebagai Reference Image saat meminta Veo 3 membuat video.**

---

## 6. Detail Implementasi Teknis (Prompts & JSON)

Bagian ini bisa Anda copy-paste langsung ke dalam node n8n Anda.

### A. System Prompt untuk "The Director" (Node Gemini 1.5 Pro)

Isi ini di bagian **System Instruction** pada node HTTP Request ke CometAPI.

```
ROLE: Anda adalah Sutradara YouTube Shorts Viral kelas dunia yang ahli dalam "Conflict Arc Storytelling".

TUGAS: Buat naskah video 60 detik berdasarkan TOPIK yang diberikan user.

STRUKTUR CERITA (WAJIB):
1. HOOK (0-3 detik): Visual mengejutkan atau pertanyaan provokatif. Jangan ada intro "Halo guys". Langsung ke aksi.
2. RISING ACTION (3-15 detik): Perkenalkan karakter dan tujuannya.
3. THE CONFLICT (15-30 detik): Masalah besar muncul (Hacker, Error, Musuh, Kehabisan waktu).
4. THE COMEBACK (30-50 detik): Solusi cerdas atau momen heroik.
5. THE PAYOFF (50-60 detik): Hasil akhir yang memuaskan + Call to Action (CTA) halus.

FORMAT OUTPUT (JSON ONLY):
Hanya berikan output JSON mentah tanpa markdown ```json.
{
  "title": "Judul Clickbait (Max 50 char)",
  "scenes": [
    {
      "scene_number": 1,
      "duration_seconds": 3,
      "voiceover_script": "Teks yang dibaca narator...",
      "visual_prompt": "Deskripsi visual SANGAT DETAIL untuk AI Image Generator. Jelaskan subjek, aksi, pencahayaan, dan sudut kamera (misal: low angle, cinematic lighting).",
      "sound_effect_prompt": "Deskripsi suara latar untuk Veo 3 (misal: loud explosion, fast footsteps)"
    },
    ... (lanjutkan sampai selesai)
  ]
}
```

**File lengkap:** `prompts/director-system-prompt.txt`

### B. Konfigurasi CometAPI Requests

**Untuk Script Generation (Gemini 1.5 Pro):**
```bash
POST https://api.cometapi.com/v1/chat/completions
Authorization: Bearer YOUR_COMETAPI_KEY

{
  "model": "gemini-1.5-pro",
  "messages": [...],
  "temperature": 0.7,
  "response_format": { "type": "json_object" }
}
```

**Untuk Image Generation (Gemini 2.5 Flash Image):**
```bash
POST https://api.cometapi.com/v1/images/generations
Authorization: Bearer YOUR_COMETAPI_KEY

{
  "model": "gemini-2.5-flash-image",
  "prompt": "your visual prompt here",
  "n": 1,
  "size": "1024x1024"
}
```

**Untuk Video Generation (Veo 3):**
```bash
POST https://api.cometapi.com/v1/video/veo3/video-create
Authorization: Bearer YOUR_COMETAPI_KEY

{
  "model": "veo3",
  "prompt": "your visual prompt",
  "image_url": "url from image generation",
  "aspect_ratio": "9:16",
  "duration": 5
}
```

**Check status:**
```bash
GET https://api.cometapi.com/v1/video/veo3/video-status?task_id=TASK_ID
Authorization: Bearer YOUR_COMETAPI_KEY
```

**Catatan:** Semua API calls sudah ter-implement di workflow JSON. Anda tinggal import!

### C. Contoh Workflow JSON Output

Hasil dari Node 1 (The Director) akan seperti ini:

```json
{
  "title": "Hacker Serang Server! Bisakah Kita Bertahan?",
  "scenes": [
    {
      "scene_number": 1,
      "duration_seconds": 3,
      "voiceover_script": "Server kita diserang hacker dengan 10,000 requests per detik!",
      "visual_prompt": "Close-up of computer screen showing red warning alerts and attack graphs, dramatic red lighting, screen reflection on worried face, cinematic angle",
      "sound_effect_prompt": "Intense alarm sounds, keyboard typing rapidly"
    },
    {
      "scene_number": 2,
      "duration_seconds": 12,
      "voiceover_script": "Tim DevOps harus segera bertindak. Firewall mulai overload.",
      "visual_prompt": "Young Asian developer in red hoodie frantically typing on mechanical keyboard, multiple monitors showing code and graphs, blue screen glow, over-shoulder shot",
      "sound_effect_prompt": "Mechanical keyboard clicks, computer fan whirring"
    }
  ]
}
```

**File contoh lengkap:** `examples/sample-script-output.json`

---

## 📊 Estimasi Biaya (CometAPI)

### Per Video (5 scenes, 60 seconds total)

| Service | Cost per Unit | Per Video |
|---------|---------------|-----------|
| Gemini 1.5 Pro (Script) | ~$0.001/request | ~$0.001 |
| Gemini 2.5 Flash Image (5 images) | ~$0.002/image | ~$0.010 |
| Veo 3 Video (5 clips × 5s) | ~$0.10-0.20/clip | ~$0.50-1.00 |
| **TOTAL per video** | | **~$0.51-1.01** |

### Monthly Cost Estimates

| Videos/Month | Total Cost |
|--------------|------------|
| 10 videos | ~$5-10 |
| 30 videos | ~$15-30 |
| 100 videos | ~$50-100 |

**Jauh lebih murah** dibanding subscription model ($90+/month)!

**Cost Optimization Tips:**
1. Reuse character sheets across multiple videos
2. Use shorter clips (3-5s instead of 8-10s)
3. Reduce scenes per video if possible
4. Monitor CometAPI dashboard for usage

---

## 🚀 Quick Start Guide

### Prerequisites
1. **CometAPI Account** - [Sign up here](https://www.cometapi.com)
2. **n8n Instance** (Cloud atau self-hosted)
3. **API Key** dari CometAPI dashboard

### Setup Steps (5 minutes) ⚡

```bash
# 1. Clone repository
git clone https://github.com/cupitebet/n8nVideoPendek.git
cd n8nVideoPendek

# 2. Get CometAPI Key
# Sign up at: https://www.cometapi.com
# Get API key from dashboard

# 3. Setup environment
cp config/environment-variables-cometapi.env.example .env
# Edit .env dan isi COMETAPI_KEY=your-key-here

# 4. Install n8n (via Docker)
docker run -d --name n8n -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  --env-file .env \
  n8nio/n8n

# 5. Access n8n
# URL: http://localhost:5678

# 6. Import workflow
# Upload file: workflows/youtube-shorts-cometapi.json

# 7. Test run!
# Just click Execute - API key loaded from .env
```

**[→ Detailed Setup Guide](docs/COMETAPI-SETUP.md)**

### Test Your First Video

1. Open workflow in n8n
2. Input topic: "AI Revolution in Gaming"
3. Execute workflow
4. Check outputs:
   - ✅ Script generated
   - ✅ Character images created
   - ✅ Video clips generated
   - ✅ Video URLs ready for download

---

## 🎯 Best Practices

### 1. Character Consistency
- Always create a "Character Sheet" first using `character-generator-cometapi.json`
- Use the same character reference for all clips
- Keep character descriptions detailed but consistent

### 2. Prompt Engineering
- Be specific with visual descriptions
- Include camera angles and lighting
- Mention mood and atmosphere
- Add sound effect descriptions for Veo 3

### 3. Video Structure
- Keep HOOK under 3 seconds
- Build tension progressively
- Include clear visual transitions
- End with strong CTA

### 4. Cost Optimization
- Start with Level 1 (manual) to learn the pattern
- Test prompts before full automation
- Reuse character sheets across videos
- Monitor CometAPI billing dashboard

---

## 📚 Additional Resources

### Documentation
- [CometAPI Setup Guide](docs/COMETAPI-SETUP.md) ⭐ **Start here**
- [Quick Start Guide](docs/QUICKSTART.md)
- [Character Consistency Guide](docs/CHARACTER-CONSISTENCY.md)
- [Workflows README](workflows/README.md)

### Example Files
- [Sample Script Output](examples/sample-script-output.json)
- [Sample Character Sheet](examples/sample-character-sheet.json)

### Templates
- [Director System Prompt](prompts/director-system-prompt.txt)
- [Character Sheet Template](prompts/character-sheet-template.txt)
- [Visual Prompt Template](prompts/visual-prompt-template.txt)

### External Links
- [CometAPI Documentation](https://apidoc.cometapi.com)
- [CometAPI Pricing](https://api.cometapi.com/pricing)
- [n8n Workflow Best Practices](https://docs.n8n.io/workflows/best-practices/)

---

## 🐛 Troubleshooting

### Common Issues

**Error: "Invalid API key"**
- ✅ Check `.env` file has correct `COMETAPI_KEY`
- ✅ Verify key in CometAPI dashboard
- ✅ Restart n8n: `docker restart n8n`

**Error: "Insufficient credits"**
- ✅ Top up credits at [CometAPI Dashboard](https://www.cometapi.com/dashboard)

**Karakter tidak konsisten antar klip**
- ✅ Gunakan character generator workflow first
- ✅ Gunakan prompt yang lebih spesifik
- ✅ Include detailed character descriptions in every prompt

**Video processing timeout**
- ✅ Increase wait time in workflow (currently 30s)
- ✅ Veo 3 typically takes 30-60 seconds per clip

**Video quality rendah**
- ✅ Gunakan prompt yang lebih detail
- ✅ Specify "high quality, cinematic" di prompt
- ✅ Use better lighting descriptions

---

## 🗺️ Roadmap

### Phase 1: Foundation ✅
- [x] Blueprint V2 documentation
- [x] CometAPI integration
- [x] Basic workflow structure
- [x] Cost estimation

### Phase 2: Enhancement 🚧
- [x] Complete n8n workflow JSON
- [x] Character consistency system
- [ ] Automated subtitle generation
- [ ] Music library integration

### Phase 3: Optimization 📋
- [ ] A/B testing framework
- [ ] Analytics integration
- [ ] Multi-platform publishing
- [ ] Template library

### Phase 4: Scale 💡
- [ ] Batch processing
- [ ] Queue management
- [ ] Multi-language support
- [ ] White-label solution

---

## 🤝 Contributing

Kontribusi sangat welcome!

### How to Contribute:
1. Fork repo ini
2. Create branch: `git checkout -b feature/amazing-feature`
3. Commit changes: `git commit -m 'Add amazing feature'`
4. Push: `git push origin feature/amazing-feature`
5. Open Pull Request

### Areas to Contribute:
- 🐛 Bug fixes
- ✨ New features (Veo 3 optimizations)
- 📝 Documentation improvements
- 🎨 Prompt template library
- 🔧 New integrations
- 🌐 Translations

---

## 📄 License

MIT License - feel free to use, modify, distribute!

---

## 🙏 Credits

**Built with:**
- [n8n](https://n8n.io) - Workflow automation
- [CometAPI](https://www.cometapi.com) - AI Models aggregator
- [Google Veo 3](https://deepmind.google/technologies/veo/) - Video generation
- [Gemini 2.5 Flash](https://ai.google.dev/gemini-api) - Image & text generation

**Created by:** Cupi & Claude AI

---

## 📞 Support

- 📖 **Documentation:** [docs/](docs/)
- 💬 **Issues:** [GitHub Issues](https://github.com/cupitebet/n8nVideoPendek/issues)
- 📧 **Email:** [Contact Support]
- 💬 **Community:** Discord/Telegram (Coming Soon)

---

## ⚡ Quick Links

- [Quick Start Guide](docs/COMETAPI-SETUP.md) ⭐ **Setup dalam 5 menit**
- [Tech Stack Comparison](#3-perbandingan-alat-tech-stack-update)
- [Prompt Templates](#6-detail-implementasi-teknis-prompts--json)
- [Cost Calculator](#-estimasi-biaya-cometapi)
- [Troubleshooting Guide](#-troubleshooting)
- [Workflows Documentation](workflows/README.md)

---

**Happy Creating! 🎬**

*"From Idea to Viral Shorts in Minutes, Not Hours" - YouTube Shorts Automasi V2 powered by CometAPI*
