# 🎬 Project Blueprint V2: YouTube Shorts Automasi

**Update Desember 2025: Menggunakan Veo 3 dan Gemini 2.5 Flash Image ("Nano Banana") untuk alur kerja yang lebih ringkas dan hemat biaya.**

## 🚀 Two Ways to Get Started

### Option 1: CometAPI (RECOMMENDED ⭐)
**Setup Time: 5 minutes | Single API Key | Simple Billing**

✅ Easiest setup - just one API key
✅ Access to Gemini 1.5 Pro, Gemini 2.5 Flash Image, Veo 3
✅ Pay-as-you-go dengan credit system
✅ No complex Google Cloud setup needed

**[→ Start with CometAPI](docs/COMETAPI-SETUP.md)**

### Option 2: Google Cloud Direct
**Setup Time: 30+ minutes | Multiple Credentials | Complex Billing**

For advanced users who need:
- Large scale (1000+ videos/month)
- Custom quotas
- Full infrastructure control

**[→ Google Cloud Setup](docs/GOOGLE-CLOUD-SETUP.md)**

---

## 📋 Daftar Isi

- [Two Ways to Get Started](#-two-ways-to-get-started) ⭐ **Start Here**
- [Perubahan Strategi Utama](#1-perubahan-strategi-utama)
- [Arsitektur Workflow Baru](#2-arsitektur-workflow-baru-n8n-flow)
- [Perbandingan Alat](#3-perbandingan-alat-tech-stack-update)
- [Langkah Taktis Memulai](#4-langkah-taktis-memulai-untuk-pemula-n8n)
- [Prompt Rahasia](#5-prompt-rahasia-untuk-nano-banana-gemini-flash-image)
- [Detail Implementasi Teknis](#6-detail-implementasi-teknis-prompts--json)
- [Quick Links](#quick-links)

---

## 1. Perubahan Strategi Utama

Dengan **Veo 3.1**, kita bisa memangkas kerumitan.

### Dulu
```
Text → Image → Video (Bisu) → Generate Suara Terpisah → Edit Gabung
```

### Sekarang (Veo 3.1)
```
Text/Image → Video + Audio (SFX & Ambience) sekaligus
```

**Keuntungan:**
- ✅ Lebih cepat (1 step vs 5 steps)
- ✅ Lebih murah (satu API call)
- ✅ Audio terintegrasi (sound effects + ambience)
- ✅ Konsistensi lebih baik

---

## 2. Arsitektur Workflow Baru (n8n Flow)

### Tahap 1: The Director (Tetap Sama)

**Trigger:** Topik Manual / Tren

**Node 1 (LLM - Gemini 1.5 Pro):**
- **Tugas:** Menulis naskah dengan struktur **Conflict Arc**
- **Penting:** Minta output prompt visual yang sangat spesifik untuk konsistensi karakter
  - Contoh: *"A young gamer boy with a red hoodie, anime style"*

### Tahap 2: The Visual Factory (Updated)

**Node 2: Image Gen (Karakter & Aset)**

- **Model:** Gemini 2.5 Flash Image ("Nano Banana")
- **Kenapa ini?**
  - ✅ Sangat cepat
  - ✅ Murah
  - ✅ Punya fitur **"Subject Consistency"**
- **Cara Kerja:**
  1. Generate 1 gambar karakter utama
  2. Pakai gambar itu sebagai referensi untuk shot selanjutnya
  3. Wajah karakter tidak berubah-ubah (masalah utama video AI)

**Alternatif Mudah:**
- Jika node ini sulit disetting manual, gunakan node **"Google AI Studio"** di n8n dan pilih model `gemini-2.5-flash-image`

**Node 3: Video Gen (The Engine)**

- **Model:** Google Veo 3.1 (via Vertex AI / Gemini API)

**Fitur Wajib Pakai:**
1. **Image-to-Video:** Masukkan gambar dari Node 2
2. **Native Audio Generation:** Centang opsi audio - Veo 3.1 otomatis membuat:
   - Suara langkah kaki
   - Ledakan
   - Angin
   - Sound effects lainnya sesuai video
3. **Prompting:** `"Cinematic shot, [Action], high quality audio"`

### Tahap 3: The Assembly (Penyatuan)

Meskipun Veo hebat, dia menghasilkan klip-klip terpisah (misal 5-10 detik per klip). Kita tetap butuh "Lem" untuk menyatukannya.

**Node 4 (Voiceover - Opsional):**
- Veo 3.1 bisa generate dialog, tapi kadang belum sempurna untuk naskah panjang
- **Saran:** Gunakan **Google Cloud Text-to-Speech (Journey Voice)** untuk Narator utama
- Biarkan Veo menangani sound effect (suara latar)

**Node 5 (Cloud Editor - Creatomate):**
- Menggabungkan klip Veo 3.1 + Narasi
- Menambahkan **Subtitle Otomatis** (wajib untuk Shorts)
- Menambahkan **musik latar** (background music) yang konsisten

---

## 3. Perbandingan Alat (Tech Stack Update)

| Fungsi | Alat Lama | Pilihan Baru (Lebih Mudah & Canggih) |
|--------|-----------|---------------------------------------|
| **Image Model** | Flux / Midjourney | **Gemini 2.5 Flash Image** ("Nano Banana")<br/>✅ Lebih mudah diintegrasikan di n8n karena native Google |
| **Video Model** | Runway Gen-3 | **Google Veo 3.1**<br/>✅ Menang di Audio & Konsistensi |
| **Konsistensi** | Latih LoRA (Rumit) | **Veo "Ingredients"**<br/>✅ Upload gambar karakter, Veo akan menjaganya tetap sama |
| **Biaya** | Mahal ($90+/bulan untuk berbagai sub) | **Pay-as-you-go**<br/>✅ Bayar per generate via Google Cloud, jauh lebih murah untuk pemula |

---

## 4. Langkah Taktis Memulai (Untuk Pemula n8n)

Jika Anda merasa script n8n rumit, lakukan ini bertahap:

### Level 1 (Semi-Manual)
1. Gunakan **Google AI Studio** (gratis/murah) di browser
2. Generate gambar pakai **Gemini 2.5 Flash** ("Nano Banana")
3. Generate video pakai **Veo 3.1**
4. Edit manual di **CapCut**

**Lakukan ini untuk 5 video pertama agar paham polanya.**

### Level 2 (Automasi Aset)
1. Buat workflow n8n sederhana:
   - Input Topik
   - Generate 5 Prompt Gambar
   - Generate 5 Gambar (Gemini Flash)
   - Save to Google Drive
2. Anda tinggal ambil gambar di Drive, lalu animasikan manual

### Level 3 (Full Auto)
- Baru sambungkan **Veo 3.1** dan **Creatomate** di n8n setelah Level 2 lancar

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

**Gunakan hasil "Character Sheet" ini sebagai Reference Image saat meminta Veo 3.1 membuat video.**

---

## 6. Detail Implementasi Teknis (Prompts & JSON)

Bagian ini bisa Anda copy-paste langsung ke dalam node n8n Anda.

### A. System Prompt untuk "The Director" (Node Gemini 1.5 Pro)

Isi ini di bagian **System Instruction** pada node Google Gemini Chat di n8n.

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
      "sound_effect_prompt": "Deskripsi suara latar untuk Veo 3.1 (misal: loud explosion, fast footsteps)"
    },
    ... (lanjutkan sampai selesai)
  ]
}
```

### B. Konfigurasi Node Veo 3.1 (HTTP Request)

Jika belum ada node resmi Veo, gunakan **HTTP Request** ke Vertex AI.

**Method:** `POST`

**URL:**
```
https://us-central1-aiplatform.googleapis.com/v1/projects/[YOUR_PROJECT_ID]/locations/us-central1/publishers/google/models/veo-003-1:predict
```

**Body (JSON):**
```json
{
  "instances": [
    {
      "prompt": "{{ $json.visual_prompt }}",
      "image_storage_uri": "gs://[bucket_name]/[character_ref_image.png]",
      "audio_prompt": "{{ $json.sound_effect_prompt }}"
    }
  ],
  "parameters": {
    "sampleCount": 1,
    "videoLength": "5s",
    "aspectRatio": "9:16"
  }
}
```

**Catatan:** Pastikan Anda mengganti variabel dengan expression n8n yang sesuai.

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

---

## 📊 Estimasi Biaya

### Google Cloud (Pay-as-you-go)

| Service | Cost per Unit | Monthly (30 videos) |
|---------|---------------|---------------------|
| Gemini 1.5 Pro (Script) | ~$0.001/request | ~$0.03 |
| Gemini 2.5 Flash Image | ~$0.0005/image | ~$0.075 (5 images/video) |
| Veo 3.1 Video | ~$0.10/video clip | ~$15 (5 clips/video) |
| Text-to-Speech | ~$0.000016/char | ~$0.48 |
| Cloud Storage | ~$0.02/GB | ~$0.60 |
| **TOTAL** | | **~$16.19/month** |

**Jauh lebih murah** dibanding subscription model ($90+/month)!

---

## 🚀 Quick Start

### Prerequisites
1. **CometAPI Account** (recommended) atau Google Cloud Account
2. **n8n Instance** (Cloud atau self-hosted)
3. **API Key** dari CometAPI atau Google Cloud credentials

### Setup Steps (CometAPI - Recommended)

```bash
# 1. Clone repository
git clone https://github.com/cupitebet/n8nVideoPendek.git
cd n8nVideoPendek

# 2. Get CometAPI Key
# Sign up at: https://www.cometapi.com
# Get API key from dashboard

# 3. Setup environment
cp config/environment-variables-cometapi.env.example .env
# Edit .env dan isi COMETAPI_KEY

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

**[→ Detailed CometAPI Setup Guide](docs/COMETAPI-SETUP.md)**

**[→ Google Cloud Setup (Advanced)](docs/GOOGLE-CLOUD-SETUP.md)**

### Test Your First Video

1. Open workflow in n8n
2. Input topic: "AI Revolution in Gaming"
3. Execute workflow
4. Check outputs:
   - ✅ Script generated
   - ✅ Character images created
   - ✅ Video clips generated
   - ✅ Final video assembled

---

## 🎯 Best Practices

### 1. Character Consistency
- Always create a "Character Sheet" first
- Use the same character reference for all clips
- Keep character descriptions detailed but consistent

### 2. Prompt Engineering
- Be specific with visual descriptions
- Include camera angles and lighting
- Mention mood and atmosphere
- Add sound effect descriptions for Veo 3.1

### 3. Video Structure
- Keep HOOK under 3 seconds
- Build tension progressively
- Include clear visual transitions
- End with strong CTA

### 4. Cost Optimization
- Start with Level 1 (manual) to learn the pattern
- Test prompts before full automation
- Use Gemini Flash Image (cheaper) instead of Pro
- Monitor Google Cloud billing alerts

---

## 📚 Additional Resources

### Documentation
- [Google Veo 3.1 Documentation](https://cloud.google.com/vertex-ai/docs/generative-ai/video/overview)
- [Gemini 2.5 Flash Image Guide](https://ai.google.dev/gemini-api/docs/vision)
- [n8n Workflow Best Practices](https://docs.n8n.io/workflows/best-practices/)

### Example Workflows
- `workflows/youtube-shorts-veo-v2.json` - Full automation workflow
- `workflows/youtube-shorts-manual.json` - Semi-manual workflow for beginners
- `examples/sample-scripts/` - Example video scripts

### Templates
- `prompts/director-prompt.txt` - The Director system prompt
- `prompts/character-sheet-template.txt` - Character consistency template
- `config/veo-settings.json` - Veo 3.1 configuration template

---

## 🐛 Troubleshooting

### Common Issues

**Error: "Quota exceeded" pada Veo 3.1**
- ✅ Check Google Cloud quota limits
- ✅ Request quota increase di console
- ✅ Reduce video generation frequency

**Karakter tidak konsisten antar klip**
- ✅ Pastikan menggunakan character reference image
- ✅ Gunakan prompt yang lebih spesifik
- ✅ Tambahkan "Subject Consistency" di Gemini Flash

**Audio tidak sinkron dengan video**
- ✅ Gunakan Veo 3.1 native audio untuk SFX
- ✅ Tambahkan voiceover terpisah via TTS
- ✅ Edit manual di Creatomate jika perlu

**Video quality rendah**
- ✅ Gunakan prompt yang lebih detail
- ✅ Specify "high quality, 4K, cinematic" di prompt
- ✅ Adjust Veo 3.1 quality settings

---

## 🗺️ Roadmap

### Phase 1: Foundation ✅
- [x] Blueprint V2 documentation
- [x] Basic workflow structure
- [x] Google Cloud integration guide
- [x] Cost estimation

### Phase 2: Automation 🚧
- [ ] Complete n8n workflow JSON
- [ ] Character consistency system
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
- ✨ New features (Veo 3.1 optimizations)
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
- [Google Veo 3.1](https://deepmind.google/technologies/veo/) - Video generation
- [Gemini 2.5 Flash](https://ai.google.dev/gemini-api) - Image & text generation
- [Creatomate](https://creatomate.com) - Video editing automation

**Created by:** Cupi & Claude AI

---

## 📞 Support

- 📖 **Documentation:** [docs/](docs/)
- 💬 **Issues:** [GitHub Issues](https://github.com/cupitebet/n8nVideoPendek/issues)
- 📧 **Email:** [Contact Support]
- 💬 **Community:** [Join Discord/Telegram]

---

## ⚡ Quick Links

- [Quick Start Guide](#quick-start) - Setup dalam 10 menit
- [Tech Stack Comparison](#3-perbandingan-alat-tech-stack-update) - Lihat perbandingan tools
- [Prompt Templates](#6-detail-implementasi-teknis-prompts--json) - Copy-paste prompts
- [Cost Calculator](#estimasi-biaya) - Hitung biaya operasional
- [Troubleshooting Guide](#troubleshooting) - Solusi masalah umum

---

**Happy Creating! 🎬**

*"From Idea to Viral Shorts in Minutes, Not Hours" - YouTube Shorts Automasi V2*
