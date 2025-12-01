# Installation Guide - Tuyul Digital Web3

Panduan lengkap untuk setup workflow n8n "Tuyul Digital Web3" dari nol.

## Prerequisites

1. **n8n Instance** (pilih salah satu):
   - Self-hosted: Docker, npm, atau desktop app
   - Cloud: n8n Cloud (https://n8n.io/cloud/)

2. **API Keys** yang diperlukan:
   - OpenAI API Key
   - NewsAPI Key
   - HuggingFace Token
   - Telegram Bot Token
   - WordPress credentials (opsional)
   - Google Sheets credentials (opsional)

## Step 1: Setup n8n

### Opsi A: n8n Cloud (Termudah)
1. Daftar di https://n8n.io/cloud/
2. Pilih plan (starter bisa cukup untuk mulai)
3. Login ke dashboard n8n

### Opsi B: Self-Hosted dengan Docker (Recommended)
```bash
# Pull n8n image
docker pull n8nio/n8n

# Run n8n
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n

# Akses n8n di http://localhost:5678
```

### Opsi C: npm install
```bash
npm install n8n -g
n8n start
```

## Step 2: Setup Credentials

### 2.1 OpenAI Credential
1. Buka n8n > Credentials > Add Credential
2. Pilih "OpenAI API"
3. Masukkan API Key dari https://platform.openai.com/api-keys
4. Test connection
5. Save sebagai "OpenAI Credential"

### 2.2 NewsAPI Credential
1. Daftar di https://newsapi.org/register
2. Dapatkan API key (free tier)
3. Di n8n: Credentials > Add > "HTTP Query Auth"
4. Name: `apiKey`
5. Value: `YOUR_NEWSAPI_KEY`
6. Save sebagai "NewsAPI Credential"

### 2.3 HuggingFace Credential
1. Daftar di https://huggingface.co
2. Buat token di https://huggingface.co/settings/tokens
3. Di n8n: Credentials > Add > "HTTP Header Auth"
4. Name: `Authorization`
5. Value: `Bearer YOUR_TOKEN`
6. Save sebagai "HuggingFace Credential"

### 2.4 Telegram Bot Credential
1. Chat dengan @BotFather di Telegram
2. Command: `/newbot`
3. Ikuti instruksi, dapatkan bot token
4. Di n8n: Credentials > Add > "Telegram API"
5. Masukkan bot token
6. Save sebagai "Telegram Bot Credential"

**Dapatkan Chat ID:**
```bash
# Kirim pesan ke bot Anda, lalu akses:
https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates

# Cari "chat":{"id": XXXXXX} dan copy ID tersebut
```

### 2.5 WordPress Credential (Opsional)
1. Login ke WordPress admin
2. Users > Profile > Application Passwords
3. Generate new password
4. Encode credentials:
```bash
echo -n "username:application_password" | base64
```
5. Di n8n: Credentials > Add > "HTTP Header Auth"
6. Name: `Authorization`
7. Value: `Basic BASE64_RESULT`
8. Save sebagai "WordPress Credential"

### 2.6 Google Sheets Credential (Opsional)
1. Buka https://console.cloud.google.com/
2. Create project baru
3. Enable "Google Sheets API"
4. Create OAuth 2.0 credentials
5. Di n8n: Credentials > Add > "Google Sheets OAuth2 API"
6. Masukkan Client ID & Secret
7. Authorize dengan akun Google
8. Save sebagai "Google Sheets Credential"

## Step 3: Import Workflow

1. Download file `workflows/tuyul-digital-web3.json`
2. Di n8n: Workflows > Import from File
3. Pilih file JSON
4. Workflow akan ter-import dengan semua nodes

## Step 4: Configure Workflow

### 4.1 Update Settings Node
Buka file `config/settings.json` dan sesuaikan:

```json
{
  "social_media": {
    "telegram_chat_id": "YOUR_CHANNEL_ID",
    "admin_telegram_id": "YOUR_ADMIN_ID"
  },
  "storage": {
    "google_sheet_id": "YOUR_SHEET_ID"
  }
}
```

### 4.2 Update Node Parameters
Di setiap node yang memerlukan, ganti placeholder:
- `{{ wordpress_url }}` → URL WordPress Anda
- `{{ telegram_chat_id }}` → Chat ID channel Telegram
- `{{ admin_telegram_id }}` → Chat ID admin
- `{{ google_sheet_id }}` → ID Google Sheet

### 4.3 Create Google Sheet
1. Buat Google Sheet baru
2. Buat sheet bernama "Archive"
3. Header row:
   ```
   date | project_name | category | verdict | chain | ticker | article_url | original_news_url | risk_flags | opportunities
   ```
4. Copy Sheet ID dari URL
5. Paste ke config

## Step 5: Test Workflow

### Test Manual (Tanpa Schedule)
1. Disable "Schedule Trigger" node
2. Tambah "Manual Trigger" node di awal
3. Connect ke "Fetch Crypto News"
4. Execute workflow manual
5. Cek hasil di setiap node

### Test dengan Sample Data
```json
{
  "title": "New AI Project Launches $5M Presale",
  "description": "Revolutionary AI blockchain project announces presale",
  "content": "Full content here...",
  "url": "https://example.com/news"
}
```

## Step 6: Activate Workflow

1. Pastikan semua credentials valid
2. Test workflow dengan manual trigger
3. Cek output di WordPress, Telegram, Google Sheets
4. Enable "Schedule Trigger" node
5. Aktifkan workflow (toggle "Active" di kanan atas)

## Step 7: Monitoring

### Monitor Executions
1. Di n8n: Executions tab
2. Lihat log setiap eksekusi
3. Debug jika ada error

### Monitor Costs
- OpenAI usage: https://platform.openai.com/usage
- NewsAPI usage: https://newsapi.org/account
- HuggingFace: https://huggingface.co/settings/billing

## Troubleshooting

### Error: "Unauthorized"
- Cek credentials, pastikan API key valid
- Re-authenticate jika perlu

### Error: "Rate limit exceeded"
- NewsAPI free tier: 100 req/day
- OpenAI: tergantung tier Anda
- Kurangi frekuensi schedule atau upgrade plan

### Error: "No items returned"
- Berita mungkin tidak ada yang match filter
- Cek keyword di filter node
- Test dengan keyword lebih general

### Gambar tidak ter-generate
- HuggingFace Flux bisa lambat di free tier
- Alternatif: gunakan Stability AI atau DALL-E
- Atau disable image generation dulu

## Next Steps

1. Monitor hasil konten pertama
2. Adjust prompt AI berdasarkan hasil
3. Tweak filter keyword
4. Add more platforms (TikTok, YouTube)
5. Optimize posting schedule

## Support

Jika ada issue:
1. Cek dokumentasi n8n: https://docs.n8n.io
2. Forum komunitas: https://community.n8n.io
3. Review workflow logs untuk error detail

---

**Selamat! Workflow Anda sudah siap jalan otomatis! 🚀**
