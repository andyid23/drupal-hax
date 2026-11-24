Audit tautan & gambar eksternal (\*.skilling.us) — python, vba, webapps, web
----------------------------------------------------------------------------

Sweep DB keempat situs untuk semua referensi `*.skilling.us` di body (tautan `<a href>`, gambar `<img src>`, URL teks). 250 node mengandung referensi. Tanggal 2026-11.

### Rekap per situs

Situs

Node

Img total

Img unik

Tautan lesson domain sendiri

learnweb

webappexamples

webexamples

python

16

67

55

1

0

0

0

vba

100

496

452

50

0

0

0

webapps

112

492

451

2

0

117

21

web

22

6

6

0

1

0

0

**Total**

250

1061

964

53

1

117

21

Referensi gambar menunjuk domain sumber masing-masing kursus (python→python.skilling.us, dst.); `learnweb.skilling.us` muncul hanya sebagai 1 tautan lesson (web nid 186) + 6 gambar di web (104, 247).

### Ketersediaan sumber

*   Probe acak 36 dari 964 URL gambar: **semua 200** — semuanya masih bisa diunduh.
*   17 path unik ke `webexamples.skilling.us` dari webapps: **15 dari 17 hilang di mirror lokal** (404 di `/examples/`) tapi **200 di server sumber** — zip arsip yang dipakai saat mirror tidak memuat file-file HTML contoh ini.
*   117 tautan ke `webappexamples.skilling.us` adalah **aplikasi PHP live** (form, query string, zip) — tidak bisa dijadikan file statis.

### Rencana (belum dieksekusi)

#### F1 — Gambar dipindah lokal (964 file)

*   Unduh semua 964 URL unik ke docroot bersama `/var/www/html2/drupal8/web/skilling/<domain>/sites/default/files/…` (path sumber dipertahankan; nama file di-decode: `%20`→spasi). Docroot di-share 4 multisite, nginx melayani file statis untuk Host apa pun.
*   Rewrite `<img src>` per situs: `https://<domain>.skilling.us/sites/…` → `/skilling/<domain>/sites/…` (relatif → bekerja di semua domain).
*   Verifikasi: 0 `<img src>` ber-URL skilling.us tersisa; sampling path lokal 200; render 0 error.

#### F2 — Tautan lesson diubah lokal (53 tautan)

*   vba (50) + python (1): map slug sumber → alias Indonesia yang sudah ada di DB masing-masing situs.
*   web 186 & webapps 440 → `styling-images` = lesson web nid 155 "Penataan Gaya di Gambar" → tautan lintas-situs lokal.

#### F3 — Extend mirror webexamples (15 file + aset)

*   Unduh 15 file hilang + aset yang mereka butuhkan dari server live ke `/examples/`; verifikasi 200.

#### F4 — Tautan webexamples di webapps (21 tautan, 17 path)

*   Rewrite ke `/examples/…` lokal (atau domain web-contoh bila sudah aktif) setelah F3 selesai.

#### F5 — webappexamples.skilling.us (117 tautan) — tidak diubah

*   PHP live (form interaktif untuk latihan webapps). Memindah lokal membutuhkan PHP server + DB contoh sendiri — proyek terpisah. Risiko: bila domain sumber mati, latihan hilang. Dicatat sebagai ketergantungan eksternal.

#### F6 — Cache & publikasi

*   TRUNCATE cache\_page + cache\_dynamic\_page\_cache setelah setiap batch rewrite; verifikasi render 0 error; laporan dipublikasi ke repo ini.

### Estimasi

F1 adalah bongkahan terbesar (964 unduhan + 4 rewrite DB ~250 node); F2–F4 kecil. Semua reversibel (backup body per situs sebelum rewrite).