# Quick Start Guide - 10 Menit Setup

Panduan singkat untuk mulai menggunakan workflow dalam 10 menit.

## 🚀 Setup Cepat (Minimum Viable Product)

Untuk mulai dengan cepat, Anda hanya butuh **2 API keys**:
1. ✅ OpenAI API Key
2. ✅ NewsAPI Key

Yang lain (WordPress, Telegram, Google Sheets) bisa ditambahkan nanti.

---

## Step 1: Install n8n (2 menit)

### Cara Tercepat - Docker:
```bash
docker run -d --restart unless-stopped --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

Akses: http://localhost:5678

---

## Step 2: Dapatkan API Keys (3 menit)

### OpenAI:
1. Buka https://platform.openai.com/api-keys
2. Create new secret key
3. Copy dan simpan

### NewsAPI:
1. Buka https://newsapi.org/register
2. Daftar dengan email
3. Dapatkan API key (langsung muncul)

---

## Step 3: Setup Credentials di n8n (2 menit)

1. Di n8n: **Credentials** > **Add Credential**

2. **OpenAI:**
   - Type: "OpenAI API"
   - Paste API key
   - Save

3. **NewsAPI:**
   - Type: "HTTP Query Auth"
   - Name: `apiKey`
   - Value: `YOUR_NEWSAPI_KEY`
   - Save

---

## Step 4: Import Workflow (1 menit)

1. Download: `workflows/tuyul-digital-web3.json`
2. Di n8n: **Workflows** > **Import from File**
3. Upload file JSON
4. Workflow imported!

---

## Step 5: Test Run (2 menit)

### Quick Test (tanpa Telegram/WordPress):

1. **Disable nodes yang belum di-setup:**
   - Upload Image to WordPress
   - Publish Article to WordPress
   - Publish to Telegram
   - Archive to Google Sheets
   - Send Admin Notification

2. **Add Manual Trigger:**
   - Drag "Manual Trigger" node
   - Connect ke "Fetch Crypto News"
   - Disable "Schedule Trigger"

3. **Run Test:**
   - Click "Execute Workflow"
   - Lihat hasil di node "Parse Generated Content"

4. **Cek Output:**
   - Buka node "Parse Generated Content"
   - Lihat `generated_content` → artikel, video script, caption

---

## ✅ Selesai! Workflow Dasar Jalan

**Apa yang sudah bisa dilakukan:**
- ✅ Fetch berita crypto terbaru
- ✅ Filter berita relevan (presale, web3, dll)
- ✅ Research otomatis dengan AI
- ✅ Generate artikel + script video + caption

**Apa yang belum:**
- ⏸️ Auto-publish ke WordPress
- ⏸️ Kirim ke Telegram
- ⏸️ Archive ke Google Sheets
- ⏸️ Generate gambar

---

## 🎯 Next Steps (Setup Lengkap)

### Level 2: Tambah Telegram (5 menit)
1. Chat @BotFather di Telegram
2. `/newbot` → ikuti instruksi
3. Dapatkan bot token
4. Setup credential di n8n
5. Enable node "Publish to Telegram"

### Level 3: Tambah WordPress (10 menit)
1. Install WordPress (atau pakai existing)
2. Generate Application Password
3. Setup credential di n8n
4. Update WordPress URL di nodes
5. Enable publishing nodes

### Level 4: Tambah Google Sheets (10 menit)
1. Create Google Cloud project
2. Enable Sheets API
3. Create OAuth credentials
4. Setup di n8n
5. Enable archive node

### Level 5: Tambah Image Generation (5 menit)
1. Daftar HuggingFace
2. Generate token
3. Setup credential
4. Enable image generation node

### Level 6: Production (Auto-Pilot)
1. Verify semua nodes aktif
2. Set schedule (default: 8AM daily)
3. Activate workflow
4. Monitor executions

---

## 💡 Tips Optimasi

### Hemat Cost OpenAI:
```json
{
  "max_items_per_run": 1,  // Mulai dari 1 artikel/hari
  "model": "gpt-4o-mini"   // Model paling murah
}
```

### Improve Content Quality:
1. Edit prompt di `prompts/content-generation-prompt.txt`
2. Tambah contoh style yang Anda mau
3. Test dengan manual trigger
4. Iterate sampai puas

### Filter Lebih Baik:
Edit keywords di `config/settings.json`:
```json
{
  "filter_keywords": [
    "presale",
    "IDO",
    "your custom keywords"
  ]
}
```

---

## 🐛 Troubleshooting Cepat

**Error: "Missing credentials"**
→ Setup credential untuk node tersebut atau disable node

**Error: "Rate limit"**
→ NewsAPI free: max 100/day. Kurangi frequency.

**Konten tidak bagus:**
→ Edit prompt di folder `prompts/`

**Berita tidak relevan:**
→ Adjust keywords di filter node

---

## 📊 Monitoring

### Cek Executions:
- n8n > Executions
- Lihat success/failed runs
- Debug error dari log

### Cek API Usage:
- OpenAI: https://platform.openai.com/usage
- NewsAPI: https://newsapi.org/account

---

## 🎉 Congratulations!

Workflow Anda sudah jalan! Mulai dari sini, Anda bisa:
1. Monitor konten yang di-generate
2. Tweak prompt untuk hasil lebih baik
3. Add platform baru (TikTok, YouTube)
4. Scale up (lebih banyak artikel/hari)

**Happy Automating! 🚀**
