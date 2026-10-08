# ABG VIRALL – Streaming Video

Situs streaming video bernama **ABG VIRALL** dalam **satu file Cloudflare Worker**
(`lintang-worker.mjs`). Daftar videonya dibaca langsung dari **API Lulustream**
(akun Luluvid milik pemilik situs), jadi menambah video cukup dengan mengunggah
video ke Lulustream — video otomatis tampil di situs, tanpa edit kode dan tanpa
deploy ulang.

## Sumber data

API Lulustream: `https://api.lulustream.com/api/file/list`

- Worker memanggil API itu dari sisi server (endpoint situs: `/api/videos`),
  memetakan hasilnya (judul, jumlah ditonton asli, waktu unggah relatif,
  durasi dari detik, thumbnail otomatis), dan menyajikannya ke halaman.
- Hanya video yang bisa diputar (`canplay = 1`) yang ditampilkan, urut dari
  yang terbaru diunggah. Cache 60 detik.
- Kategori untuk semua video saat ini: `Video`.

## Kunci API (PENTING)

Kunci API Lulustream **tidak ditulis di kode** dan tidak disimpan di repo ini,
karena repo & situs bersifat publik — siapa pun yang memegang kunci bisa
mengendalikan akun.

Kunci disimpan sebagai **Secret** di Cloudflare Worker dengan nama:

```
LULU_KEY
```

Tanpa secret ini, `/api/videos` mengembalikan daftar kosong dan situs
menampilkan pesan "Belum ada video yang tersedia saat ini."

## Cara online (Cloudflare Workers)

1. Masuk ke https://dash.cloudflare.com → **Workers & Pages**.
2. **Create** → **Import a repository** → pilih repo `ABGVIRALL` → **Deploy**
   (nama worker: `abgvirall`, sesuai `wrangler.toml`).
3. Buka worker-nya → **Settings** → **Variables and Secrets** → **Add** →
   pilih tipe **Secret**, nama `LULU_KEY`, isi dengan kunci API Lulustream →
   **Deploy**.
4. Buka alamat Worker (berakhiran `.workers.dev`). Berhasil jika video dari
   akun Lulustream langsung tampil di beranda.

## Versi GitHub Pages

`index.html` di repo adalah varian statis (GitHub Pages). Setelah Worker aktif,
varian ini diarahkan membaca data dari Worker (`/api/videos`, CORS terbuka),
sehingga kedua alamat menampilkan isi yang sama.
