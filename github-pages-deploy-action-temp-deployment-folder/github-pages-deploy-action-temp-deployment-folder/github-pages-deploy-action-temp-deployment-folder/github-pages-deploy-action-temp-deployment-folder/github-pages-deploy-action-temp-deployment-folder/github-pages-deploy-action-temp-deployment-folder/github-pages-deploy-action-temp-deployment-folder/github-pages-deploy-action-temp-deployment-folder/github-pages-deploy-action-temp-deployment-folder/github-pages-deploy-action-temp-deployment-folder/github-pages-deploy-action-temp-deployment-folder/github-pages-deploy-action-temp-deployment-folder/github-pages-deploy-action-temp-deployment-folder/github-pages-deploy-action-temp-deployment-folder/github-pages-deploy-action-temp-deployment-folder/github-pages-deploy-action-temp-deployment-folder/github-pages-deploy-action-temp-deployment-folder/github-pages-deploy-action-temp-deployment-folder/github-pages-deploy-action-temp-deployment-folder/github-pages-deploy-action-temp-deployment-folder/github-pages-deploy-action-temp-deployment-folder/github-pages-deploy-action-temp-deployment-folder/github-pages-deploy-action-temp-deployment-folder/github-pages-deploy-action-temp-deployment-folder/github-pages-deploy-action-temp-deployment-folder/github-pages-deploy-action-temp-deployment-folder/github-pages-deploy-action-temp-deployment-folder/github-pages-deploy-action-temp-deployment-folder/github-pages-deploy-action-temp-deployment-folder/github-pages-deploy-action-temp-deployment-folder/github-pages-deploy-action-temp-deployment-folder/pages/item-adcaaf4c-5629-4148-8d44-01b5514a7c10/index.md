Ringkasan
---------

Laporan pengguna: halaman `http://webapps.pesanweb.my.id/lesson/griddy-template` "banyak tidak rapi tidak lurus atas jadi mepet ke pinggir", dengan potongan teks sampah seperti `row">`, `kol-12">`, `Dunia Doggo` di luar kotak kode. Penyelidikan menemukan akar masalahnya bukan satu, melainkan dua lapis yang saling menguatkan.

Akar masalah 1: kode di dalam kontainer kode ikut diterjemahkan
---------------------------------------------------------------

Sesi kloning paralel menerjemahkan isi kode contoh HTML/CSS/PHP ke Indonesia:

*   `<body>` menjadi `<tubuh>`, `<head>` menjadi `<kepala>`
*   `class="container"` menjadi `kelas="wadah"`, `row` menjadi `baris`, `col` menjadi `kol`
*   Bahkan urutan atribut tertukar (`<kelas div="…>`) dan variabel PHP ikut diterjemahkan (`$dog` menjadi `$anjing`, `$now` menjadi `$sekarang`, `$_GET['dog']` menjadi `$_GET['anjing']`)
*   Node MCQ juga: opsi `mcq_bold_text` menjadi `font-tebal: berat;` (sumber: `font-bold: heavy;`)

Akar masalah 2: template code-container meng-decode entity satu kali
--------------------------------------------------------------------

Konvensi penyimpanan situs sumber menyimpan isi `<li>` kode secara ter-escape (`&lt;div class=&quot;row&quot;&gt;`). Template code-container di webapps ini meng-decode entity **satu kali** sebelum output, sehingga:

*   Kode HTML single-escape dirender sebagai elemen nyata — `<div class="row">` menjadi div sungguhan yang tak terlihat, bukan teks kode
*   span penekanan yang menyertai tanda kutip tak ter-escape bocor ke halaman sebagai teks `row">` dan `kol-12">` — persis sampah yang dilaporkan pengguna
*   Kode VBA/PHP tanpa kurung sudut "terlihat baik" hanya karena kebetulan

**Konvensi perbaikan: kode contoh HTML harus disimpan double-escape** (`&amp;lt;body&amp;gt;`) dengan `&nbsp;` dan span penekanan tetap nyata. HTML yang dikirim lalu menampilkan `&lt;body&gt;`, dan browser menampilkan kode literal dengan penekanan bergaya — persis seperti situs sumber.

Jebakan tambahan: Textile merusak `--`
--------------------------------------

Pass tipografi Textile menulis ulang literal `--` (komentar HTML `<!-- … -->`, komentar SQL) menjadi dash berjarak, jadi komentar tampil rusak (`<! - Tajuk - >`). Solusi: simpan tanda hubung sebagai `&amp;#45;&amp;#45;` — tampilan identik (`--`) dan tak terlihat oleh Textile.

Perbaikan yang dieksekusi
-------------------------

*   **27 lesson** diperbaiki: front-end-magic (get-bootstrapy, bootstrap-styles, overriding-bs-styles, griddy-template, whats-responsive, being-responsive, responsive-images, navbars, forms), html (few-tags, phpstorm, simple-styling, classes-and-ids, divs-and-spans, broken-styles, images, links, page-components, code-standards), user-accounts (log-inout), validation-library (multifield-validation), capturing-data (add-data, beginning-forms, dropping-down, editing-relationships) — 63+ kontainer kode direstorasi 1:1 dari webapps.skilling.us
*   **2 node MCQ**: `mcq_css_p_tags` (nid 381, pertanyaan + 4 opsi) dan `mcq_bold_text` (nid 392, 4 opsi) — kode opsi direstorasi dari widget sumber
*   Komentar HTML di kode diterjemahkan (Header→Tajuk, dsb.) via endpoint MT; semua alias yang terkena save dipin ulang `pathauto=0`, dedupe, cache rebuild
*   Prosedur MT harus _stash_ kontainer kode dulu — menerjemahkan seluruh blok membuat MT menerjemahkan isi kode (`&lt;body&gt;` berubah jadi `<tubuh>` karena MT melihat versi ter-decode)

Verifikasi
----------

*   Rescan 26 lesson: 0 kontainer rusak tersisa (sisa flag hanya 8 lesson PHP yang komentarnya sengaja diterjemahkan)
*   Render ulang griddy-template, classes-and-ids, broken-styles, whats-responsive, forms: 0 sampah teks, 0 parser error, kode tampil sebagai teks literal dengan penekanan bergaya
*   20 URL dicek: semua HTTP 200

Yang tersisa
------------

*   8 lesson PHP (enter-php, getting-data, conditional-styles, no-not-there, old-school-debugging, optional-phpstorm-and-xdebug, more-conditions, appending-file, log-record-format) masih membawa nama variabel hasil terjemahan (`$anjing`, `$cutestDog`, `$sekarang`, `$Namapengguna`) — konsisten dengan prosenya, tapi tidak sama dengan sumber; memulihkan ke Inggris menuntut rombakan prosa juga

Fold ke skill
-------------

Skill `drupal-lesson-authoring` v1.4.0: konvensi double-escape, jebakan Textile `--`, stash-kontainer-sebelum-MT, dan prosedur perbaikan batch (scanner flag, restore posisional dari sumber, apply + pin alias + dedupe + rebuild, verifikasi render).