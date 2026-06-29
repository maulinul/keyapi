# API Key Vault

Aplikasi web untuk menyimpan, mengelola, dan memvalidasi API key dari berbagai provider AI — semuanya berjalan di browser tanpa backend.

## Fitur

- **Simpan API Key** — tambah key dengan label kustom, disimpan di `localStorage` browser
- **Test Validasi** — cek apakah key aktif langsung dari browser; mendeteksi error CORS vs key tidak valid
- **Multi-provider** — mendukung 9 provider AI sekaligus
- **Sync GitHub Gist** — backup vault ke private Gist menggunakan GitHub/Gist Token
- **Cloud Scheduler** — generate workflow GitHub Actions untuk test otomatis terjadwal (tanpa perlu buka browser)
- **Local Scheduler** — jadwalkan test berkala langsung dari browser
- **Dark Mode** — toggle tema terang/gelap
- **Statistik** — ringkasan total key, valid, gagal, CORS blocked, dan jumlah provider aktif

## Provider yang Didukung

| Provider | Kecepatan | CORS Browser |
|---|---|---|
| Gemini Flash | Sedang | Aman |
| Groq | Ultra-cepat | Aman |
| Cerebras | Ultra-cepat | Mungkin diblokir |
| Mistral | Sedang | Mungkin diblokir |
| OpenRouter | Varies | Aman |
| GitHub Models | Low | Aman |
| DeepSeek | Cepat | Mungkin diblokir |
| Qwen | Sedang | Mungkin diblokir |
| Claude | Sedang | Mungkin diblokir |

## Cara Pakai

1. Buka `index.html` di browser (tidak perlu server)
2. Set **GitHub Token** di bagian *Pengaturan Akses* (scope: `gist`)
3. Pilih provider, masukkan label dan API key, klik **+ Tambah**
4. Klik ⚡ untuk test validasi key secara langsung
5. Klik **💾 Save via GitHub Token** untuk backup ke Gist

## Sync & Cloud Scheduler

- **Sync ke Gist** — vault disimpan sebagai private Gist (`apivault.json`)
- **Load dari Gist** — muat status terbaru dari cloud
- **GitHub Actions** — download workflow YAML, taruh di `.github/workflows/`, tambahkan secret `VAULT_GH_TOKEN` dan `VAULT_GIST_ID` di repo — test berjalan otomatis sesuai jadwal

## Status Key

| Status | Arti |
|---|---|
| ✅ Valid | Key aktif dan diterima provider |
| ❌ Gagal | Key tidak valid atau ditolak |
| ⚠️ Browser Blocked | CORS diblokir browser — gunakan Cloud Scheduler untuk test akurat |
| — Belum dicek | Key belum pernah ditest |

## Teknologi

- Vanilla HTML + CSS + JavaScript (zero dependency)
- `localStorage` untuk penyimpanan lokal
- GitHub Gist API untuk sinkronisasi cloud
- GitHub Actions (Python stdlib) untuk health check otomatis
