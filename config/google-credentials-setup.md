# Google Credentials Setup untuk n8n

## Overview

File ini menjelaskan cara setup Google Cloud credentials di n8n untuk Blueprint V2.

---

## Step 1: Prepare Service Account JSON

1. Download JSON key dari Google Cloud Console (sudah dijelaskan di `docs/GOOGLE-CLOUD-SETUP.md`)
2. Simpan file dengan nama: `google-service-account.json`
3. **JANGAN commit file ini ke Git!**

---

## Step 2: Add to n8n Credentials

### Via n8n UI

1. Buka n8n web interface
2. Click **Settings** (gear icon)
3. Select **Credentials**
4. Click **Add Credential**
5. Search dan pilih: **Google Service Account**
6. Isi form:
   - **Credential Name**: `Google Cloud - YouTube Shorts`
   - **Service Account Email**: (auto-filled from JSON)
   - **Private Key**: Upload atau paste JSON content

### Via n8n File System (Advanced)

Jika menggunakan n8n self-hosted:

```bash
# Navigate to n8n data directory
cd ~/.n8n

# Create credentials directory if not exists
mkdir -p credentials

# Copy JSON key
cp /path/to/google-service-account.json credentials/

# Set permissions
chmod 600 credentials/google-service-account.json
```

---

## Step 3: Configure di Workflow Nodes

Setelah credential dibuat, assign ke nodes:

### A. Gemini 1.5 Pro (The Director)
- Node type: **Google Gemini Chat**
- Credential: `Google Cloud - YouTube Shorts`
- Model: `gemini-1.5-pro`

### B. Gemini 2.5 Flash Image
- Node type: **HTTP Request**
- Authentication: **Google Service Account**
- Credential: `Google Cloud - YouTube Shorts`

### C. Veo 3.1
- Node type: **HTTP Request**
- Authentication: **Google Service Account**
- Credential: `Google Cloud - YouTube Shorts`

### D. Cloud Text-to-Speech
- Node type: **Google Cloud Text-to-Speech**
- Credential: `Google Cloud - YouTube Shorts`

---

## Step 4: Test Credentials

### Quick Test via n8n

1. Create new workflow
2. Add **Google Gemini Chat** node
3. Select credential
4. Send test message: "Say hello"
5. Execute

**Expected:** Response dari Gemini dengan greeting

### Test Veo 3.1 Access

```bash
# Via curl (outside n8n)
curl -X POST \
  -H "Authorization: Bearer $(gcloud auth print-access-token)" \
  -H "Content-Type: application/json" \
  https://us-central1-aiplatform.googleapis.com/v1/projects/YOUR_PROJECT_ID/locations/us-central1/publishers/google/models/veo-003-1:predict
```

---

## Troubleshooting

### Error: "Invalid credentials"
```
Solution:
1. Re-download JSON key dari Google Cloud Console
2. Pastikan service account ACTIVE (not disabled)
3. Check JSON format valid (buka di text editor)
4. Re-create credential di n8n
```

### Error: "Permission denied"
```
Solution:
1. Check service account roles di Google Cloud Console
2. Harus punya: Vertex AI User, Generative AI User
3. Re-assign roles if needed
4. Tunggu 1-2 menit for propagation
```

### Error: "Project not found"
```
Solution:
1. Check project ID di JSON key matches dengan project di Google Cloud
2. Pastikan project ACTIVE (not deleted/suspended)
3. Verify billing enabled
```

---

## Security Checklist

- [ ] JSON key file NOT committed to Git
- [ ] JSON key added to `.gitignore`
- [ ] File permissions set to 600 (read/write owner only)
- [ ] Credential stored in n8n encrypted storage
- [ ] Service account has least-privilege roles (no Owner/Editor)
- [ ] Billing alerts configured
- [ ] Key rotation schedule planned (every 90 days)

---

## Next Steps

After credentials setup:
1. ✅ [Import workflow](../docs/QUICKSTART.md#step-3-import-workflow)
2. ✅ [Test run](../docs/QUICKSTART.md#step-4-test-run)

---

**Credentials ready! Let's create some videos! 🚀**
