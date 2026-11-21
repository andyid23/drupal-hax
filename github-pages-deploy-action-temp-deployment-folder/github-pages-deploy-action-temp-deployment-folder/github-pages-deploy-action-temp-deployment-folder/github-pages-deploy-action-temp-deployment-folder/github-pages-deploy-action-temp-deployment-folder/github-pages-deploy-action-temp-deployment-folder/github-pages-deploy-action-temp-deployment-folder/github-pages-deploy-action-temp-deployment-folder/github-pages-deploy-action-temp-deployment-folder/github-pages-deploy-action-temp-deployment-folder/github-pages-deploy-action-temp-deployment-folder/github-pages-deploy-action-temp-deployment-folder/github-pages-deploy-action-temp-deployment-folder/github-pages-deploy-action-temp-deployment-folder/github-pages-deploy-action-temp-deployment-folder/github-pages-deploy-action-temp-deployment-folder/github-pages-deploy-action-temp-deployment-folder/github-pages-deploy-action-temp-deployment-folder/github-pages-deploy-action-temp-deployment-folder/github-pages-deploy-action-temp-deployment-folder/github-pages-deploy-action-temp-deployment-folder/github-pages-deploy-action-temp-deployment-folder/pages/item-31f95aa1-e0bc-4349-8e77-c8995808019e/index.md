Laporan: Audit webapps.pesanweb.my.id vs webapps.skilling.us
------------------------------------------------------------

Audit penuh situs keempat (kursus PHP/webapps): tree sumber 125 lesson dalam 10 bab + 2 standalone, 126 node lesson target diambil render-nya, 125 halaman sumber diambil untuk census. Tanggal 2026-11.

### Hasil

*   **125/125 lesson sumber terpetakan** — tidak ada node yang hilang (satu-satunya situs tanpa gap).
*   **Urutan 10 bab 100% sesuai sumber** (getting-ready 9, urls-and-files 8, making-html 24, validation-library 10, database-apps 14, capturing-data 15, front-end-magic 12, user-accounts 6, log-files 3, tips-and-tricks 13, + what-now standalone) — semua anak di book dengan bobot berurutan.
*   **Semua di book, semua published, 0 node di luar book, 0 baris book yatim, 0 node uji**.
*   **Render: 125 halaman, 0 custom-tag-error/parser-error**.
*   Alias sehat: mayoritas node punya pasangan slug Indonesia + slug sumber EN.

### Temuan ringan (3)

*   **547 many-records**: worksheet-column.png ada tapi tanpa wrapper `figure-indent` (satu-satunya temuan gambar).
*   **Duplikat baris `path_alias`**: MCQ 381 (mcqcssptags ×6), MCQ 392 (mcqboldtext ×6), lesson 417, 427, 543, 546 (×2).
*   **Alias sampah**: /lesson/templat-halaman-error (nid 410), /lesson/id-get2 (nid 543).

### Rencana (belum dieksekusi)

*   **T1** — Bungkus worksheet-column.png di 547 dengan `figure-indent`.
*   **M1** — Dedupe 6 pasang baris alias.
*   **M2** — Hapus 2 alias sampah, pin alias manual utama.
*   **R1** — Publikasi laporan ini (halaman ini) — selesai bersamaan dengan audit.

### Perbandingan empat situs

Situs

Lesson hilang

Urutan bab

Render error

Junk

vba

3 (sudah di-clone)

diperbaiki

0

bersih

python

0 (dari awal)

diperbaiki

0

907/1014 di-unpublish; 448 dibiarakan

web

1 (dibuat)

diperbaiki

0

node uji dikeluarkan dari book; 187/212 di-unpublish

**webapps**

**0**

**sudah benar**

**0**

**3 temuan ringan saja**