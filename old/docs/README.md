# README: Workflow "Tuyul Digital Web3" untuk Crypto/Blockchain/Presale

**Versi:** 1.0
**Tanggal:** 2025-11-20
**Penulis:** Cupi

## 1. Tujuan

Membangun sistem otomatisasi yang:

* Secara periodik (terjadwal) mencari tren berita/presale di niche crypto, blockchain, Web3.
* Menganalisa data tersebut dengan AI.
* Membuat konten video pendek (TikTok, Reels, YouTube Shorts) + artikel Web yang siap publish.
* Mengurangi intervensi manual sebanyak mungkin.
* Memanfaatkan n8n sebagai engine automasi.

## 2. Scope

**Masuk dalam scope**

* Trigger terjadwal (cron) untuk fetch berita crypto/presale.
* Riset otomatis: narasi, tokenomics, ekosistem.
* Generasi konten: artikel panjang, short post, caption media sosial.
* Generasi media bantu: gambar futuristik (untuk thumbnail/video) atau klip pendek video.
* Publishing otomatis: ke WordPress (artikel) + Telegram/YouTube/TikTok (short).
* Penyimpanan data riset ke Google Sheets atau database ringan sebagai archive.

**Keluar dari scope**

* Pembuatan sistem monetisasi langsung (payment, wallet integrasi) kecuali sudah disepakati.
* Analisis on-chain yang memerlukan full node atau heavy data (kecuali disepakati tambahan).
* Integrasi kompleks dengan sistem eksternal yang belum tersedia.

## 3. Arsitektur Workflow

1. **Trigger Schedule Node** – misalnya cron tiap hari pukul 08:00 WIB.
2. **Fetch News/Trending Topics Node** – API HTTP request untuk berita crypto/presale.
3. **Filter Node** – menyaring berita yang relevan (kata kunci: "presale", "web3", "blockchain", "AI", "L2", "ecosystem").
4. **Research Node** – menggunakan AI (GPT) untuk analisis: project name, narrative, tokenomics, risiko.
5. **Content Generation Node** – AI menghasilkan artikel panjang, post singkat, caption.
6. **Media Generation Node** – gambar (Flux AI) atau video clip (opsional) untuk konten short.
7. **Publishing Node**
   * Upload gambar/media ke WordPress → posting artikel.
   * Push short post ke Telegram/YouTube/TikTok (via API atau integrasi).
8. **Archive/Database Node** – simpan hasil riset ke Google Sheets / database.
9. **Notification Node** – kirim pemberitahuan ke admin setelah publish.

## 4. Detail Node dan Konfigurasi

| Node                   | Tipe                   | Penjelasan / Parameter penting                                                        |
| ---------------------- | ---------------------- | ------------------------------------------------------------------------------------- |
| Scheduled Trend Fetch  | Trigger (Cron)         | Eksekusi otomatis; contoh: `0 8 * * *` (setiap hari pk 08:00 WIB)                     |
| Fetch Crypto News      | HTTP Request           | URL API; set method GET; output JSON.                                                 |
| Filter Presale Web3    | Function / If Node     | Hanya keluarkan berita yang mengandung kata kunci relevan.                            |
| Research Presale Web3  | OpenAI Node            | Model: GPT-4o-mini (atau yang sesuai); prompt analisis proyek crypto.                 |
| Generate Content       | OpenAI Node            | Prompt untuk artikel, short post, caption untuk video pendek.                         |
| Generate Image         | HTTP Request (Flux AI) | Prompt gambar futuristik untuk proyek.                                                |
| Upload to WordPress    | HTTP Request           | URL endpoint WP REST API; credentials WP; posting tipe "post" atau CPT "crypto-news". |
| Telegram/Short Publish | API Node               | Integrasi Telegram Bot / YouTube / TikTok (di-integrasikan developer).                |
| Archive to Sheet       | Google Sheets Node     | Simpan field seperti: project_name, date, verdict, risk, url.                         |
| Notification           | Telegram / Slack Node  | Kirim pesan "Konten untuk project X telah publish".                                   |

## 5. Kredensial & Integrasi

Developer harus menyiapkan:

* API Key untuk OpenAI.
* API Key untuk Flux AI (atau penyedia gambar/video).
* API Key untuk berita crypto (news API) atau scraping.
* WordPress: token atau user/password dengan hak REST API.
* Google Sheets: Client ID & Client Secret untuk OAuth.
* Telegram Bot atau integrasi YouTube/TikTok sesuai kebutuhan.

## 6. Alur Data & Format Output

* Input dari berita → JSON format array berita.
* Setelah filter → output JSON satu atau beberapa item berita terpilih.
* Research Node output JSON terstruktur seperti:

  ```json
  {
    "project_name": "...",
    "category": "...",
    "value_prop": "...",
    "use_case": "...",
    "tokenomics": {
      "ticker": "...",
      "chain": "...",
      "supply": "...",
      "presale_price": "...",
      "vesting": "..."
    },
    "roadmap": "...",
    "risk_flags": [...],
    "verdict": "..."
  }
  ```
* Generate Content Node: output JSON dengan artikel, short post, caption.
* Media Node: output URL atau media_id upload.
* WordPress Node: gunakan properti media_id dan konten.
* Archive Node: simpan minimal: date, project_name, verdict, publish_url.

## 7. Best Practices & Error Handling

* Gunakan If / Switch nodes untuk validasi input (pastikan JSON berisi field yang diharapkan).
* Tambahkan retry logic pada HTTP Request yang bisa gagal.
* Catat error ke log atau Google Sheets agar bisa di-review.
* Kredensial jangan hard-code di node, gunakan n8n Credentials.
* Uji workflow secara manual terlebih dahulu dengan sample data sebelum aktifkan scheduled.
* Pantau eksekusi dan resource (karena penggunaan AI dan gambar/video bisa berat).

## 8. Deployment & Aktivasi

* Pastikan n8n telah di-host (self-host atau cloud).
* Import JSON workflow (pastikan format valid JSON).
* Aktifkan workflow ("Active").
* Monitor hasil eksekusi (menu Executions) dan cek output apakah sesuai.
* Lakukan penyesuaian parameter (cron schedule, kata kunci filter, prompt AI) bila diperlukan.

## 9. Checklist untuk Developer

* [ ] Setup credentials: OpenAI, Flux AI, News API, WordPress, Google Sheets.
* [ ] Buat trigger cron node.
* [ ] Buat node fetch berita dan test dengan 1 pickup.
* [ ] Buat filter node dan verifikasi filter bekerja.
* [ ] Buat research node; test dengan sample berita.
* [ ] Buat content generation node + test output.
* [ ] Buat media generation node + test gambar/video.
* [ ] Buat upload ke WordPress node + test posting.
* [ ] Buat publish short post node (Telegram/YouTube/TikTok) + test.
* [ ] Buat archive node + test logging.
* [ ] Buat notification node.
* [ ] Aktifkan workflow dalam mode otomatis dan monitor eksekusi pertama swalayan.
* [ ] Siapkan dokumentasi kecil bagaimana user (Cupi) bisa mengganti prompt, kata kunci, schedule.

## 10. Pemeliharaan & Pengembangan

* Evaluasi performa (berapa konten yang terpublish, view, engagement).
* Update kata kunci filter secara berkala (tren berubah cepat di crypto).
* Update prompt AI agar tetap relevan (2025 → 2026 narrative bisa berbeda).
* Tambah integrasi baru apabila perlu: e.g., TikTok API, YouTube Shorts upload, analytics tracking.
