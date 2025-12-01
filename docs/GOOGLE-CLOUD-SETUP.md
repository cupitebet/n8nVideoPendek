# 🌩️ Google Cloud Setup Guide - Blueprint V2

**Panduan lengkap setup Google Cloud untuk YouTube Shorts Automation**

---

## Overview

Untuk menjalankan Blueprint V2, Anda memerlukan:
- ✅ Vertex AI API (untuk Veo 3.1)
- ✅ Generative Language API (untuk Gemini)
- ✅ Cloud Text-to-Speech API
- ✅ Cloud Storage (untuk menyimpan assets)

**Estimasi waktu setup: 20-30 menit**

---

## Step 1: Create Google Cloud Project

### 1.1 Buat Project Baru

1. Buka [Google Cloud Console](https://console.cloud.google.com)
2. Klik dropdown project (pojok kiri atas)
3. Klik **New Project**
4. Isi detail:
   - **Project Name**: `youtube-shorts-automation`
   - **Organization**: (pilih jika ada)
5. Klik **Create**

### 1.2 Verify Project Created

```bash
# Pastikan project sudah dibuat
# Di console, cek project ID yang muncul
# Format: youtube-shorts-automation-xxxxx
```

---

## Step 2: Enable Billing

**PENTING:** Google Cloud requires billing account untuk menggunakan AI APIs.

### 2.1 Create Billing Account

1. Pergi ke **Billing** menu (kiri sidebar)
2. Klik **Link a billing account**
3. Jika belum punya:
   - Klik **Create billing account**
   - Isi informasi kartu kredit
   - Aktivasi

### 2.2 Set Budget Alerts (RECOMMENDED)

Agar tidak overspend:

1. **Billing** > **Budgets & alerts**
2. **Create Budget**
3. Set budget: `$50/month` (untuk mulai)
4. Set alerts at:
   - 50% ($25)
   - 90% ($45)
   - 100% ($50)

---

## Step 3: Enable Required APIs

### 3.1 Via Console (GUI)

1. Pergi ke **APIs & Services** > **Library**
2. Search dan enable satu per satu:

**A. Vertex AI API**
- Search: "Vertex AI API"
- Klik **Enable**
- Tunggu 1-2 menit

**B. Generative Language API**
- Search: "Generative Language API"
- Klik **Enable**

**C. Cloud Text-to-Speech API**
- Search: "Cloud Text-to-Speech API"
- Klik **Enable**

**D. Cloud Storage API**
- Search: "Cloud Storage API"
- Klik **Enable**

### 3.2 Via gcloud CLI (Advanced)

```bash
# Install gcloud CLI first
# https://cloud.google.com/sdk/docs/install

# Set project
gcloud config set project YOUR_PROJECT_ID

# Enable APIs
gcloud services enable aiplatform.googleapis.com
gcloud services enable generativelanguage.googleapis.com
gcloud services enable texttospeech.googleapis.com
gcloud services enable storage.googleapis.com

# Verify
gcloud services list --enabled
```

---

## Step 4: Create Service Account

Service Account adalah "robot" yang akan mengakses APIs dari n8n.

### 4.1 Create Service Account

1. **IAM & Admin** > **Service Accounts**
2. Klik **Create Service Account**
3. Isi detail:
   - **Name**: `n8n-automation`
   - **ID**: `n8n-automation` (auto-filled)
   - **Description**: `Service account for n8n YouTube Shorts automation`
4. Klik **Create and Continue**

### 4.2 Grant Permissions

Pilih roles berikut (klik **Add Another Role** untuk tambah):

1. **Vertex AI User** (untuk akses Veo 3.1)
2. **Generative AI User** (untuk akses Gemini)
3. **Cloud Text-to-Speech Editor** (untuk TTS)
4. **Storage Object Admin** (untuk upload/download files)

Klik **Continue** > **Done**

### 4.3 Create JSON Key

1. Klik service account `n8n-automation` yang baru dibuat
2. Tab **Keys**
3. **Add Key** > **Create new key**
4. Pilih **JSON**
5. Klik **Create**
6. File JSON akan ter-download

**⚠️ PENTING: Simpan file ini dengan AMAN! Jangan commit ke Git!**

---

## Step 5: Setup Cloud Storage Bucket

Untuk menyimpan images dan videos.

### 5.1 Create Bucket

1. **Cloud Storage** > **Buckets**
2. Klik **Create Bucket**
3. Isi detail:
   - **Name**: `youtube-shorts-assets-YOUR_PROJECT_ID`
   - **Location type**: `Region`
   - **Location**: `us-central1` (sama dengan Veo 3.1)
   - **Storage class**: `Standard`
   - **Access control**: `Uniform`
4. Klik **Create**

### 5.2 Set Public Access (Optional)

Jika ingin assets bisa diakses public:

1. Klik bucket yang baru dibuat
2. **Permissions** tab
3. **Grant Access**
4. **New principals**: `allUsers`
5. **Role**: `Storage Object Viewer`
6. **Save**

**⚠️ HATI-HATI: Ini akan membuat semua file di bucket bisa diakses public!**

---

## Step 6: Request Quota Increase (Important!)

Veo 3.1 punya quota limits. Untuk production, perlu increase quota.

### 6.1 Check Current Quota

1. **IAM & Admin** > **Quotas**
2. Filter by:
   - **Service**: `Vertex AI API`
   - **Dimensions**: `Region: us-central1`
3. Cari: `Generate video requests per minute`

Default: **5 requests/minute**

### 6.2 Request Increase

1. Checklist quota yang mau di-increase
2. Klik **Edit Quotas**
3. Isi form:
   - **New limit**: `30` (untuk mulai)
   - **Reason**: `YouTube Shorts automation for content creation`
4. Submit

**Approval time: 2-3 hari kerja**

---

## Step 7: Test API Access

Sebelum lanjut ke n8n, test dulu API access.

### 7.1 Test via gcloud CLI

```bash
# Set auth
gcloud auth activate-service-account --key-file=PATH_TO_YOUR_JSON_KEY.json

# Test Gemini API
curl -X POST \
  -H "Authorization: Bearer $(gcloud auth print-access-token)" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [{"text": "Say hello!"}]
    }]
  }' \
  "https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-pro:generateContent"
```

Expected: JSON response dengan "hello" message.

### 7.2 Test via Google AI Studio (Easier)

1. Buka [Google AI Studio](https://aistudio.google.com)
2. Login dengan akun yang sama
3. Test Gemini dan image generation
4. Jika berhasil, berarti setup sudah benar!

---

## Step 8: Cost Management

### 8.1 Setup Cost Alerts

Sudah dijelaskan di Step 2.2 - pastikan sudah di-set!

### 8.2 Enable Detailed Billing Export

1. **Billing** > **Billing export**
2. **Enable** for both:
   - File export to BigQuery
   - Cost table export
3. Ini akan membantu track biaya per-service

### 8.3 Monitor Daily Costs

Check dashboard setiap hari:
- **Billing** > **Reports**
- Filter by **Service** untuk lihat breakdown

---

## Security Best Practices

### ✅ DO:
- Simpan JSON key di tempat aman (1Password, encrypted drive)
- Add JSON key ke `.gitignore`
- Rotate keys setiap 90 hari
- Use least-privilege permissions
- Enable billing alerts

### ❌ DON'T:
- Commit JSON key ke Git
- Share key via email/chat
- Use owner/editor roles (terlalu permissive)
- Leave unused service accounts active

---

## Troubleshooting

### Error: "API not enabled"
```
Solution:
1. Check APIs & Services > Enabled APIs
2. Pastikan semua APIs sudah enabled
3. Tunggu 1-2 menit setelah enable
```

### Error: "Permission denied"
```
Solution:
1. Check service account roles
2. Pastikan punya: Vertex AI User, Generative AI User
3. Re-create key jika perlu
```

### Error: "Quota exceeded"
```
Solution:
1. Check IAM & Admin > Quotas
2. Request quota increase
3. Atau kurangi frequency request
```

### JSON key tidak work
```
Solution:
1. Pastikan file tidak corrupt
2. Re-download key dari console
3. Check JSON format valid
4. Pastikan service account active
```

---

## Estimasi Biaya Setup

**One-time costs:**
- Setup: **$0** (gratis!)

**Monthly costs (untuk testing):**
- Gemini 1.5 Pro: ~$0.03-0.10
- Gemini 2.5 Flash Image: ~$0.08
- Veo 3.1: ~$15-30 (depends on usage)
- Cloud Storage: ~$0.60
- Text-to-Speech: ~$0.50
- **Total: ~$16-31/month**

**Production scale (100 videos/month):**
- Estimasi: ~$50-100/month

---

## Next Steps

Setelah Google Cloud setup selesai:

1. ✅ [Setup n8n](QUICKSTART.md#step-2-setup-n8n)
2. ✅ [Import workflow](QUICKSTART.md#step-3-import-workflow)
3. ✅ [Configure credentials](QUICKSTART.md#setup-credentials)
4. ✅ [Test run](QUICKSTART.md#step-4-test-run)

---

## Resources

- 📖 [Google Cloud Documentation](https://cloud.google.com/docs)
- 💰 [Pricing Calculator](https://cloud.google.com/products/calculator)
- 🎓 [Google Cloud Free Tier](https://cloud.google.com/free)
- 💬 [Support](https://cloud.google.com/support)

---

**Setup complete! Lanjut ke n8n! 🚀**
