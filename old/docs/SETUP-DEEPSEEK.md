# Setup DeepSeek AI untuk Tuyul Digital Web3

Untuk menggunakan AI yang "Lebih Murah dan Keren" (DeepSeek V3 & R1) di workflow ini, ikuti langkah-langkah berikut.

## 1. Dapatkan DeepSeek API Key

1.  Buka [DeepSeek Platform](https://platform.deepseek.com/).
2.  Login atau daftar akun.
3.  Masuk ke menu **API Keys**.
4.  Klik **Create new API key**.
5.  Salin API Key Anda (mulai dengan `sk-...`).

## 2. Konfigurasi di n8n

DeepSeek API kompatibel dengan format OpenAI, jadi kita akan menggunakan node OpenAI di n8n tetapi diarahkan ke server DeepSeek.

1.  Buka dashboard n8n Anda.
2.  Klik menu **Credentials** di sidebar kiri.
3.  Cari atau buat credential baru tipe **OpenAI API**.
4.  Isi form sebagai berikut:
    *   **User / Email**: (Kosongkan atau isi bebas)
    *   **API Key**: Paste API Key DeepSeek Anda di sini.
    *   **Base URL**: `https://api.deepseek.com` (PENTING! Jangan lewatkan ini).
5.  Simpan credential.

## 3. Verify Workflow Configuration

Workflow sudah dikonfigurasi untuk menggunakan model DeepSeek, tapi pastikan node menggunakan credential yang benar.

1.  Buka workflow `Tuyul Digital Web3`.
2.  Klik node **Research Presale with AI**.
    *   Pastikan credential yang dipilih adalah credential yang baru Anda buat.
    *   Model name harusnya sudah `deepseek-reasoner` (R1) - ini untuk riset mendalam.
3.  Klik node **Generate Content with AI**.
    *   Pastikan credential yang dipilih adalah credential yang sama.
    *   Model name harusnya sudah `deepseek-chat` (V3) - ini untuk generate konten kreatif.

## 4. Keunggulan Menggunakan DeepSeek

*   **Jauh Lebih Murah**: Biaya per token DeepSeek V3 jauh lebih rendah dibanding GPT-4o-mini, apalagi GPT-4o.
*   **Performance Tinggi**: DeepSeek V3 setara dengan GPT-4 dalam banyak benchmark.
*   **Reasoning Capability**: DeepSeek R1 (Reasoner) memiliki kemampuan berpikir langkah-demi-langkah (Chain of Thought) yang sangat baik untuk analisis project crypto.

Selamat menikmati otomasi hemat biaya! 🚀
