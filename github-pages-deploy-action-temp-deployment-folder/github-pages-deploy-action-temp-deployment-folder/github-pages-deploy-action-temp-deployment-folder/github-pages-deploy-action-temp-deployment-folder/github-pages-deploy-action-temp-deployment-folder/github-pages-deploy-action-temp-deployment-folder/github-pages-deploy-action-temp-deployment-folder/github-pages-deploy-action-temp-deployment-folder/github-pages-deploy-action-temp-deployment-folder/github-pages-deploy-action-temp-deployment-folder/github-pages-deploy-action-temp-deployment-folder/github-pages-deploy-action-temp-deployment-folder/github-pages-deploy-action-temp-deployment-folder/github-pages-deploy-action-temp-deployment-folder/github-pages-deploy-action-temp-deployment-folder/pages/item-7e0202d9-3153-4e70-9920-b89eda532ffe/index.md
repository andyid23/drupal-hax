Ringkasan
---------

Lesson **Latihan: while** (node 474, alias `/lesson/latihan-while`) di `python.pesanweb.my.id` telah diperbaiki dari sumber `https://python.skilling.us/lesson/practice-while`. Halaman kini render bersih dengan 4 MCQ, 3 pause, dan 0 error parser.

Gejala sebelum perbaikan
------------------------

Halaman _tidak_ memunculkan string error apa pun, tetapi isinya praktis hilang: hanya muncul widget "Lesson contents" (toc) dan dua baris teks literal:

*   `character.\n internal_name=character_adela`
*   `character.\n internal_name=character_georgina`

Intro, 4 MCQ, dan 3 pause tidak render sama sekali (0 `skilling-insert-mcq`, 0 `skilling-pause-container`).

Akar masalah
------------

### 1\. Artefak dump body: `<br />` + literal backslash-n

Body node 474 hasil build sebelumnya memakai pemisah `<br />` yang diikuti teks literal `\n` (bukan LF asli). Setelah parser mengubah `<br />` menjadi LF, tidak ada dua LF berturut-turut (`\n\n`) yang pernah terbentuk — padahal opsi setiap tag diekstrak sampai `\n\n` pertama. Akibatnya opsi tag pertama (toc) "menelan" seluruh sisa body: annotation, intro, dan keempat MCQ.

### 2\. Tag yatim tanpa konten

Body lama diakhiri dua tag `character.` tanpa dialog dan tanpa penutup — sisa build gagal sebelumnya. Karena barisnya memuat teks literal `\n` di antara `character.` dan `internal_name=`, baris itu tidak lagi berbentuk `character.` murni, jadi parser memperlakukannya sebagai teks biasa.

### 3\. Node MCQ berupa placeholder

Keempat node MCQ sudah ada (947-950: while1, while2, while3, while6) tetapi berisi placeholder: judul "MCQ: while 1", pertanyaan "MCQ 1 from practice-while", dan respons sampah ("Nothing" ditandai benar, "Something"). Widget MCQ akan render tanpa error meski isinya tidak berarti.

Yang dilakukan
--------------

*   **Isi 4 node MCQ dengan konten asli** (pertanyaan Bahasa Indonesia + blok kode + respons benar/salah + penjelasan):
    *   947 `while1` — output loop doggos; benar: C (3 iterasi)
    *   948 `while2` — output loop Snorlax, input 2; benar: C (count <= times → 3 iterasi)
    *   949 `while3` — output "Ready, set", input 4; benar: D (4 titik)
    *   950 `while6` — perkalian nilai, input 20; benar: C (16, karena 16\*2=32 tidak < 20)Paragraf respons lama dihapus, diganti paragraf baru (4/4/5/5 respons).
*   **Rebuild body node 474** sesuai struktur sumber: annotation catatan login (konvensi situs), intro, `<h3>Latihan</h3>`, lalu siklus `mcq. internal_name=whileN` + `pause.` (4 MCQ, 3 pause). Semua penutup `/mcq.` dan tag `character.` yatim dibuang (tag mcq tidak berpenutup; sumber tidak punya karakter).
*   Pemisah baris memakai `<br />` + LF asli; escape hanya NBSP → `&nbsp;` dan apostrof → `&#039;` (tanpa escape `&`).

Verifikasi (render HTTP 200)
----------------------------

Item

Hasil

skilling-parsing-error

0

skilling-custom-tag-option-parse-error

0

MCQ widget ter-render

4 (while1, while2, while3, while6)

Respons per MCQ

4 / 4 / 5 / 5 — sesuai sumber

Jawaban benar (DB)

C, C, D, C — satu per node

Pause (More...)

3

Code container

4 (satu di setiap MCQ)

Heading section

1 (Latihan, h4 hasil offset)

Catatan login anonim

render

Kebocoran teks literal (character./mcq.)

0

Pelajaran untuk skill
---------------------

*   **Halaman bisa rusak tanpa pesan error**: hitung widget yang ter-render (insert-mcq, pause-container, code-container) terhadap inventaris sumber, jangan hanya grep string error.
*   **Node MCQ bisa berupa placeholder**: sebelum memakai internal\_name yang sudah ada, cek isi `field_question_value` dan paragraf responsnya, bukan hanya keberadaan node.
*   **Apostrof mentah di baris kode**: `'` di dalam blok kode mengalami dobel-escape dan tampil literal; tulis `'` mentah (khusus baris kode/MCQ, bukan prosa).

Semua temuan sudah dipindai ke skill `skilling-lesson-update`.