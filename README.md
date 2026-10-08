# Lintang – Streaming Video

Situs streaming video ala YouTube dalam **satu file Cloudflare Worker** (`lintang-worker.mjs`).
Daftar videonya tidak ditulis di kode, melainkan dibaca langsung dari **Google Sheets**,
jadi menambah video cukup dengan mengedit Sheet — tanpa deploy ulang.

## Sumber data

Google Sheets (tab `Sheet1`):
https://docs.google.com/spreadsheets/d/1u2RDf6Ag8BJh7Top8UMVOc8wZlemSoCoC5KHwRkfUk4

Syaratnya, berbagi Sheet harus diatur ke **"Siapa saja yang memiliki link: Pelihat"**,
kalau tidak Worker tidak bisa membacanya dan situs akan menampilkan video contoh.

Kolom yang dibaca (urutan bebas, nama kolom fleksibel):

| Kolom | Isi |
|---|---|
| judul | Judul video |
| kanal | Nama kanal |
| tonton | Jumlah penonton, mis. `1,2 jt` |
| waktu | Waktu tayang, mis. `2 hari lalu` |
| durasi | Mis. `10:24`, atau `LIVE` untuk siaran langsung |
| kategori | Mis. Musik, Kuliner, Teknologi, Game |
| link_video | Link YouTube (akan diputar sebagai embed); kosongkan jika tidak ada |
| pelanggan | Jumlah pelanggan kanal, mis. `820 rb` |

Catatan: jangan memformat sel durasi sebagai waktu (time) di Sheets, biarkan sebagai teks
biasa — kalau tidak tampilannya bisa berubah, mis. `38:12` menjadi `38:12:00`.

## Cara online (Cloudflare Workers)

Cara termudah, tanpa install apa pun:

1. Buka file `lintang-worker.mjs`, salin seluruh isinya.
2. Masuk ke https://dash.cloudflare.com → **Workers & Pages**.
3. Buat Worker baru bernama `lintang` (atau buka yang sudah ada), klik **Deploy** sekali.
4. Klik **Edit code**, hapus isi editor, tempel kode tadi, lalu klik **Deploy**.
5. Buka alamat Worker (berakhiran `.workers.dev`). Berhasil jika di atas halaman tertulis
   *"Tersambung ke Google Sheets · 12 video"*.

Cara lain, dari komputer dengan Node.js:

```bash
npx wrangler deploy
```

(`wrangler.toml` di repo ini sudah mengarah ke `lintang-worker.mjs`.)

Setelah online, perubahan isi Sheet akan tampil otomatis dalam waktu sekitar 1 menit.
