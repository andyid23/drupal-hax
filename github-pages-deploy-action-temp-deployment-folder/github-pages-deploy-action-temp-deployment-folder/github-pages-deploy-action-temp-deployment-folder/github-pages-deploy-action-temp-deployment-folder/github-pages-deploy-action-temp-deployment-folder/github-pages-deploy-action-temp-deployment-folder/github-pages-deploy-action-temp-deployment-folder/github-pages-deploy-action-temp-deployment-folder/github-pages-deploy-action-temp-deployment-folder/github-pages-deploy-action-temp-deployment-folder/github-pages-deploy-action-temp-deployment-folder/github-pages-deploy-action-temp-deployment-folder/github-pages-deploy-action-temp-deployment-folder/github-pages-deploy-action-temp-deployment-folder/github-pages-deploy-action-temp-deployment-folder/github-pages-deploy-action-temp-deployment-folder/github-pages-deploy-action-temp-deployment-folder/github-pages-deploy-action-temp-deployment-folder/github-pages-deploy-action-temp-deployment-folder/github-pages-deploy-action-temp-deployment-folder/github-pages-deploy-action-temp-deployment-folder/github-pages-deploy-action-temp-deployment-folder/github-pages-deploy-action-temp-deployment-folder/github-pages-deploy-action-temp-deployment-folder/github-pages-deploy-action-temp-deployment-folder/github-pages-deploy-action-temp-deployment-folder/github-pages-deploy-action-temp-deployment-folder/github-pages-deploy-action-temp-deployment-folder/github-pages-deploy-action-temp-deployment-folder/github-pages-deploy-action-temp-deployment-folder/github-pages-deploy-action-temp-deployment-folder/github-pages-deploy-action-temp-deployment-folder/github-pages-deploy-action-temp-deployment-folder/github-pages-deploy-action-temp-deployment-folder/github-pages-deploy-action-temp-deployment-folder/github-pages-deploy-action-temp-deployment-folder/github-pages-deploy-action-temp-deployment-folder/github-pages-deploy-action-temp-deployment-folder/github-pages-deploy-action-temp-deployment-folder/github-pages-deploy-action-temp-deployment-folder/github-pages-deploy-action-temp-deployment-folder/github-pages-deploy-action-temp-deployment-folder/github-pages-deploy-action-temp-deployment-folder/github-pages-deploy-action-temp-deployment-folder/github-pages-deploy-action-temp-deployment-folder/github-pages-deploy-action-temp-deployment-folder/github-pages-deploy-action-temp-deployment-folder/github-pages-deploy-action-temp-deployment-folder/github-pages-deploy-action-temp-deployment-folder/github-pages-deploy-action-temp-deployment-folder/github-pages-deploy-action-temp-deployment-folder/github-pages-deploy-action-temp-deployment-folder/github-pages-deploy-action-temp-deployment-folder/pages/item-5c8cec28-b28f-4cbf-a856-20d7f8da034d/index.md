Ringkasan
---------

Lesson **Periksa Harga Daging** (node 471, alias `/lesson/periksa-harga-daging`) di `python.pesanweb.my.id` telah diperbaiki total dari sumber `https://python.skilling.us/lesson/check-meal-cost`. Sebelumnya halaman dipenuhi error parser Skilling; sekarang render bersih dengan 0 error.

Gejala sebelum perbaikan
------------------------

*   `Character tag needs internal_name option.` — 5 kali
*   `Tag parameter parse error in: meal_cost = float(input('Meal cost? '))…` — 1 kali
*   `Missing/invalid close tag? Missing . at end? Tag: character` — 1 kali
*   Dialog karakter terpotong (mis. "Apa itu" saja, sisanya bocor jadi teks liar di luar blok)
*   MCQ lama masih memakai format inline `title=` yang tidak render widget

Akar masalah (3 lapis)
----------------------

### 1\. Artefak dump body: CR + literal backslash-n

Saat body node di-dump lewat `drush sql:query` melalui pipeline SSH/terminal, akhiran baris CRLF terekam sebagai CR + teks literal `\n` (bukan LF asli). Akibatnya setelah parser mengubah `<br />` menjadi LF, baris kosong tidak pernah membentuk `\n\n` — dan opsi tag diekstrak sampai `\n\n` pertama, jadi opsi "menelan" seluruh section sampai masuk ke blok karakter berikutnya. Ini penyebab utama seluruh keluarga error di atas.

**Fix:** normalisasi sebelum rebuild:

    $body = str_replace(chr(13) . chr(92) . 'n', chr(10), $body); // CR + literal \n -> LF
    $body = str_replace(chr(13), '', $body);                     // CR telanjang

### 2\. Escape ganda pada &amp;

Langkah escape body sempat menjalankan `str_replace('&', '&amp;', $body)`. Padahal body yang sudah di-decode tidak mengandung `&` mentah, tapi string pengganti tag mengandung entitas `&nbsp;`. Hasilnya `&nbsp;` jadi `&amp;nbsp;`, parser tidak lagi mengenali `&nbsp;` → spasi, dan baris `internal_name=…` gagal di-`parse_ini_string`. Escape yang benar hanya NBSP → `&nbsp;` dan apostrof → `&#039;`.

### 3\. Regex dialog karakter berhenti di tag dalam

Pola tangkap dialog memakai lookahead yang memasukkan `<code>`, `<li>`, `<div>`, `<p>`, `<h3>`. Dialog apa pun yang memuat `<code>` inline terpotong (mis. "Apa itu `ValueError` di `except ValueError:`?" jadi "Apa itu"), menyisakan teks yatim setelah `/character.` yang merusak parsing tag berikutnya. Pola yang benar menangkap sampai pemisah paragraf `<br />\n<br />` plus opsional blok `skilling-code-container` susulan (untuk dialog yang sumbernya menyertakan contoh kode).

Yang dilakukan
--------------

*   Normalisasi akhiran baris body (fix artefak #1)
*   Hapus blok `toc.` (sumber tidak punya) — blok `annotation.` (catatan login untuk anonim) dipertahankan
*   Konversi 3 blok MCQ inline lama → tag `mcq.` + `internal_name=`; buat 3 node MCQ baru: `mcq_float_conversion_validation` (nid 1009), `mcq_simple_if_numeric_2` (nid 1010), `sys_exit` (nid 1011) lengkap dengan paragraf respons benar/salah
*   Koreksi tag `pattern.`: internal\_name salah `pattern_one_time_numeric_validation` → `pattern_one_time_numeric_input_validation` (node 963); buang konten inline dan penutup yang tidak perlu
*   Tag `exercise.` → `internal_name=exercise_parade_length` (node 981), konten inline dibuang (node exercise sudah memuat tugas)
*   Heading `<h2>` → `<h3>` (6 section: Bukan angka, Apakah pengguna mengetik angka?, Pemeriksaan rentang, Pola lain, Ringkasan, Latihan)
*   Konversi 13 dialog `<em>Nama</em>: …` → tag `character.` dengan pemetaan Adela→Izza, Ray→Hafidz, Georgina→Fina, Ethan→Hafidz (sesuai skill `skilling-lesson-update`)
*   Hapus seluruh penutup `/mcq.`, `/pattern.`, `/exercise.` (tag-tag itu tidak berpenutup di Skilling) dan tag `pattern.` yatim di ekor body

Verifikasi (render HTTP 200)
----------------------------

Item

Hasil

skilling-parsing-error

0

skilling-custom-tag-option-parse-error

0

Character tag needs internal\_name

0

Missing/invalid close tag

0

Character container ter-render

13 (Izza, Hafidz, Fina — foto tampil)

MCQ widget ter-render

3 (mcq\_float\_conversion\_validation, mcq\_simple\_if\_numeric\_2, sys\_exit)

Exercise ter-render

1 (Panjang parade)

Pattern ter-render

1 (One-time numeric input validation)

Pause (More...) ter-render

8

Code container ter-render

15

Heading section ter-render

6 (h4 hasil offset heading)

Catatan
-------

*   Node `mcq_float_conversion` lama (nid 906) sengaja TIDAK diubah: isinya dipakai lesson 888 (Kumpulan Data). Karena internal\_name tidak boleh ganda, MCQ sumber dibuat sebagai node baru dengan nama akhiran `_validation`.
*   Kutip tunggal di dalam `<code>` tampil sebagai `&#039;` literal — ini perilaku yang sama dengan lesson rujukan 871 (more-functions), bukan regression.
*   Dialog adela\_smile5 ("Oh, I see! The float()…") tidak ada di versi Bahasa Indonesia situs, sehingga tidak ditambahkan.
*   Semua temuan sudah dipindai ke skill `skilling-lesson-update` (section "Rebuilding from a dumped body: normalize first" + pitfall parser internals + internal\_name collision).