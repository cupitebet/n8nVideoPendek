# Sistem Digital Marketing Majelis.info (CometAPI + n8n)

Catatan konseptual dari perspektif **Marketing Manager + AI Engineer** untuk mengorkestrasi sistem digital marketing terintegrasi (research → konten → distribusi → chatbot CTA) bagi jaringan/circle **Majelis.info** dengan API **CometAPI**.

## 1) Sasaran & KPI
- **North Star**: pertumbuhan komunitas & leads yang terkonversi (registrasi/DM/WhatsApp click-to-chat) via Majelis.info.
- **KPI utama**: CTR konten → chat CTA, CPL (cost per lead), retensi chat (jawaban >3 balasan), dan konversi event/produk digital.
- **Segmentasi**: jamaah majelis, komunitas kajian, donatur, serta mitra komunitas (pengurus/pembicara).

## 2) Alur Orkestrasi (ringkas)
1) **Riset topik**: keyword trending (spiritual, sedekah, event kajian) + entri dari Google Sheet.
2) **Prompting UGC**: bangun prompt foto/video (Veo/Sora) yang konsisten dengan brand.
3) **Generasi aset**: gambar/video UGC via CometAPI (Nano/Veo/Sora).
4) **Deskripsi & caption**: auto-generate hook + CTA ke chatbot/WhatsApp/DM.
5) **Distribusi**: arsip di Sheet, lalu push ke Instagram/TikTok/YouTube Shorts/TG Channel (bisa reuse modul di workflow lain).
6) **Chatbot CTA**: endpoint webhook untuk DM/WA yang memicu generative reply + routing human handoff.
7) **Looping feedback**: skor performa (CTR/response) → masuk Sheet untuk memperbaiki prompt & topik.

## 3) Modul Teknis (n8n + CometAPI)
- **Input**: Google Sheet (produk/layanan/kampanye, ICP, fitur/USP, setting video, foto produk).
- **Branching model**: Switch node memilih **Veo 3.1**, **Nano+Veo**, atau **Sora 2**.
- **Prompt builder**: LangChain Agent menyusun prompt UGC selfie (foto & video) berdasar ICP + fitur + setting.
- **Media generation**: HTTP Request ke `https://api.kie.ai` dengan kredensial **CometAPI** untuk Nano (image), Veo (video), atau Sora (video i2v).
- **Review & QA**: node OpenAI Vision menganalisis output untuk validasi konten (opsional filter brand safety).
- **Delivery**: update status & URL hasil di Sheet → siap diambil modul posting sosial / chatbot.

## 4) Arsitektur Chatbot CTA (ringkas)
- **Entry point**: DM/WA klik link dari konten → webhook n8n → context builder (niat, kampanye, sumber konten).
- **Brain**: LLM via CometAPI/OpenRouter; prompt diarahkan ke intent (informasi event, donasi, konsultasi, merch).
- **Action layer**: koneksi ke Notion/Sheet/DB untuk slot event, formulir pendaftaran, atau pengingat acara.
- **Guardrails**: profanity & off-topic filter; throttle; fallback human handoff.

## 5) Rencana Distribusi Konten
- **Format**: 9:16 selfie UGC, durasi 8–10 detik, CTA ucapan untuk klik chat atau daftar.
- **Channel**: IG Reels, TikTok, YouTube Shorts, Telegram Channel/Group.
- **Cadence**: 2–3 konten/hari; jadwalkan via cron + queue agar tidak spam.
- **CTA**: shortlink ke chatbot (WA.me atau DM template), plus QR statis untuk offline event.

## 6) Governance & Brand Safety
- Simpan semua hasil di Sheet beserta **model, prompt, ICP, link aset**.
- Tambah kolom QA (human review status) bila perlu moderasi manual.
- Rate limit CometAPI + retry (sudah ada pola wait/polling di workflow).
- Logging di n8n (Execution data) + backup di S3/Cloudinary untuk versi aset.

## 7) Langkah Pakai Cepat
1) Duplikat Google Sheet template (lihat catatan di Sticky Note workflow). Hubungkan kredensial Sheets di n8n.
2) Isi kolom: `Product`, `ICP`, `Product Features`, `Video Setting`, `Product Photo`, `Model` (pilih: Veo 3.1 / Nano + Veo 3.1 / Sora 2).
3) Import workflow `workflows/majelis-digital-marketing.json` ke n8n → set kredensial **CometAPI** + **OpenAI**.
4) Jalankan manual untuk 1 baris test → cek kolom `Finished Video` terisi.
5) Sambungkan modul posting sosial & webhook chatbot untuk CTA.

## 8) Ide Iterasi Lanjut
- Tambah **topic research** otomatis (Google Trends/SerpAPI) untuk Majelis.info.
- Buat **persona bank** (jamaah, donatur, volunteer) dan mapping ke ICP.
- Integrasi **analytics** (UTM builder + Sheets) untuk tracking CTA per konten.
- Tambah **A/B prompt** untuk hook & CTA suara (via TTS) jika ingin voice-over ringan.

> Catatan ini dapat langsung dipakai sebagai peta jalan taktis antara tim marketing dan tim teknis untuk eksekusi cepat berbasis CometAPI.
