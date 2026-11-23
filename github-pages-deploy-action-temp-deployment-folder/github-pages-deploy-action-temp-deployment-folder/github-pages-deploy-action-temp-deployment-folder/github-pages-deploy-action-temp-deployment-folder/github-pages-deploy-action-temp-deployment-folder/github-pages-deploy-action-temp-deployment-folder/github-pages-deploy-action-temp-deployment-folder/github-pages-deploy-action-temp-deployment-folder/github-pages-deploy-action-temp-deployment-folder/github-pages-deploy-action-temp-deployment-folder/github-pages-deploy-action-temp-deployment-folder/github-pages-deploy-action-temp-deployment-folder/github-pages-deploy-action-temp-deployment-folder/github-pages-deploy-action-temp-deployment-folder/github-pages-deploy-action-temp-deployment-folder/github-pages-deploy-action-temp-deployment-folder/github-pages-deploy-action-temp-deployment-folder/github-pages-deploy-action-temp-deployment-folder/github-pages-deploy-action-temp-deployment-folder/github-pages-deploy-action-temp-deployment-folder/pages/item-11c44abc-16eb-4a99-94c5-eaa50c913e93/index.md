Laporan: migrasi aset skilling.us ke lokal — selesai (F1–F6)
------------------------------------------------------------

Eksekusi rencana F1–F6 (laporan sebelumnya: "audit-tautan-gambar-skilling-us"). Semua aset gambar, media, lampiran, contoh web, dan aplikasi PHP contoh kini dilayani dari server lokal. Tanggal 2026-11.

### Hasil eksekusi

Step

Isi

Hasil

F1 — gambar & media

Unduh 1089 file (964 gambar + karakter + video + audio) ke `/skilling/<domain>/sites/…`; rewrite `<img src>` + `<source>` di 4 DB (multi-langcode), iterasi 3 ronde

**0 hotlink src tersisa** di python, vba, webapps; web menyisakan 6 referensi di dalam contoh kode escaped (dibiarkan — konten ajar)

F2 — tautan lesson & lampiran

12 tautan halaman → alias lokal (5 vba, 2 webapps, 2 styling-images lintas situs, 1 filtering); 43 lampiran (.xlsm/.csv/.txt/.mp3) → `/skilling/…`

Semua tautan halaman lokal; 2 tautan vba yang mati (404/403 di sumber sendiri) di-de-link (teks tetap); `/patterns` dan `highlight-questions` dibiarkan (halaman katalog sumber masih hidup)

F3 — extend mirror

15 halaman webexamples hilang + asetnya (19 file)

Semua path yang dilink webapps kini 200 di `/examples/`

F4 — tautan webexamples

Rewrite 21 tautan di webapps → `/examples/…`

0 tautan webexamples tersisa (1 teks domain dalam `<code>` — konten ajar, dibiarkan)

F5 — webappexamples lokal

Crawl 94 direktori, 200 file PHP/HTML/aset (2,7 MB) → `/phpexamples/`; nginx `location /phpexamples/` + autoindex (setia ke sumber Apache); 3 file hilang dilengkapi (dishes, nobody-db.sql); 2 kasus kapitalisasi path diperbaiki

**45/45 path yang dilink = 200**; PHP dieksekusi via php-fpm eksisting (tanpa container baru)

F6 — cache & verifikasi

TRUNCATE cache\_page + cache\_dynamic\_page\_cache + **cache\_render** tiap batch

Render sampel 4 situs: 0 hotlink, 0 custom-tag-error

### Catatan penting

*   **File .php webappexamples adalah output yang sudah di-render** (Apache sumber mengeksekusi sebelum unduhan) — cocok untuk keperluan baca; app ber-DB tampil dengan data demo ("Changes will not be saved" memang bawaan sumber). Kode sumber asli tidak dapat diunduh publik.
*   **cache\_render wajib di-truncate** setelah rewrite body — ditemukan lagi saat verifikasi akhir (render basi menampilkan hotlink yang DB-nya sudah bersih).
*   Tiga video lesson navbars (too-narrow, too-wide, just-right) + 1 audio (kieran.mp3) ikut dimigrasi — ditemukan di pemeriksaan per-node.
*   Satu gambar sumber 404 (`scatter-goats.png`, lesson python "Bonus: Membuat Grafik") — dibiarkan hotlink; dicatat sebagai pengecualian.
*   Total tambahan disk: ~204 MB dari 180 GB bebas.

### Struktur akhir di docroot bersama

*   `/skilling/<domain>/sites/…` — 1089 file (186 MB): gambar, karakter, video, audio, lampiran .xlsm/.csv/.txt
*   `/phpexamples/…` — 200 file (2,7 MB): aplikasi PHP contoh (autoindex seperti sumber)
*   `/examples/…` — 65 file (15 MB): situs contoh statis (mirror webexamples)

Di host: `/data/compose/34/drupal8-src/web/{skilling,phpexamples,examples}` — bisa dimodifikasi langsung, efek langsung live.

### Sisa ketergantungan eksternal

*   `vba.skilling.us/patterns` (katalog), `skilling.us/highlight-questions/` — halaman sumber non-lesson, masih hidup.
*   1 gambar 404 di sumber (scatter-goats.png).
*   6 referensi di contoh kode escaped (web).