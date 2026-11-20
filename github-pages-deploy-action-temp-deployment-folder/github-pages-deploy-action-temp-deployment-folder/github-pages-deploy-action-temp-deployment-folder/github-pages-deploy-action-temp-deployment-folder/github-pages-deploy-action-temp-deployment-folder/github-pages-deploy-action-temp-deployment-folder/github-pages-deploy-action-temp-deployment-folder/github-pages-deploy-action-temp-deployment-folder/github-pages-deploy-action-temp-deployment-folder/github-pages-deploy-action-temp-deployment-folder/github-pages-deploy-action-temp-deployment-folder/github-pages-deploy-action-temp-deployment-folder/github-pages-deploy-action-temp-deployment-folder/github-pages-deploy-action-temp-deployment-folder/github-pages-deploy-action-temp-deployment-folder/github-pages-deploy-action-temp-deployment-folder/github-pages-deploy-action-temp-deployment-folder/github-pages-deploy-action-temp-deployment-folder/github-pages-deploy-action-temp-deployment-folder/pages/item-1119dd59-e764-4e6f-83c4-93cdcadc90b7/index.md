Mirror webexamples.skilling.us ke web.pesanweb.my.id/examples
-------------------------------------------------------------

Konteks: pelajaran learnweb di `web.pesanweb.my.id` menautkan contoh situs dari `webexamples.skilling.us`. Sumber ternyata bukan situs Skilling — melainkan indeks direktori file statis (Apache autoindex) berisi contoh situs: basic-sites, cryptid, professional-sites, pupperware, restaurant, twine-examples + zip. Ketergantungan target kecil: ±12 tautan di 6 lesson + 1 exercise node.

### Temuan sumber

*   `pupperware/final/` di server live **rusak**: index.html 200 tetapi semua aset lokal (library/bootstrap.min.css, top-menu.html, images/slideshow/\*, footer.html) 404.
*   Zip arsip di root (`webapps examples april 17 2021.zip`, 14 MB, 151 entri) berisi situs lengkap — termasuk semua aset yang hilang dari server live.

### Yang dieksekusi

*   **Mirror 45 file** (14 MB) ke `/var/www/html2/drupal8/web/examples/`: basic-sites/hiding-images, professional-sites/bootstrap-styles, pupperware/final (lengkap dengan library + images), restaurant — diekstrak dari zip (server live tidak lengkap), plus `index.html` daftar buatan sendiri untuk /examples/.
*   **Rewrite tautan**: body lesson 87, 186, 187, 158, 157, 104 + exercise node 88 "Proyek: proyek akhir". Tautan direktori diarahkan ke `index.html` persis (nginx melayani file, bukan listing). Tautan teks pelajaran subdomains (nid 104) diarahkan ke `https://web.pesanweb.my.id/examples/`.
*   Alias ter-pin, dedupe global, cache page + dynamic page cache di-truncate (render basi tanpa itu).

### Verifikasi

*   `webexamples.skilling.us`: **0 sisa** di seluruh DB (lesson, exercise, pattern, principle, page) dan di render lesson yang diubah.
*   7 path utama /examples/ semuanya 200 (index, hiding-images.html, basic-tags.html, moze.png, pupperware/final/index.html, restaurant/index.html, library/bootstrap.min.css).
*   Semua 6 lesson + exercise 88 tetap 200.

### Catatan

*   Sumber aslinya sendiri rusak untuk pupperware/final — mirror dari zip justru _lebih lengkap_ daripada server live.
*   canary.txt dihapus. File zip tidak ditaruh di docroot (hanya salinan kerja di scratch).