Ringkasan
---------

Lesson `https://vba.skilling.us/lesson/easy-good` ("Easy is good") dikloning ke `vba.pesanweb.my.id` dan diterjemahkan ke Bahasa Indonesia. Semua gate verifikasi lulus.

*   Node: **nid 373** — tipe `lesson`, status published
*   Judul: **Mudah itu bagus**
*   Alias: `/lesson/easy-good` (manual) dan `/lesson/mudah-itu-bagus` (pathauto) — keduanya HTTP 200
*   Book: bid 330 ("Pemrograman dengan Excel VBA"), pid 343 ("Simple Programs"), weight -8, depth 3
*   Panjang body: 10.932 byte (UTF-8), format `skilling`

Inventaris sumber vs hasil render
---------------------------------

Widget

Sumber (easy-good)

Hasil (nid 373)

`h3`

8

8

`pause.`

8

8

`character.`

5

5

`reflect.`

3

3

`pattern.`

1

1

`principle.`

1

1

`annotation.`

1

1

Blok kode

7 (6 di body + 1 di dalam dialog karakter)

7

Gambar

6 inline + 5 foto karakter

6 inline (semua HTTP 200) + 5 foto dari node karakter

Parser error

—

0

Urutan widget (_sequence diff_, 27 token) identik dengan sumber; yang berbeda hanya teks hasil terjemahan dan nama karakter hasil recast.

Pemetaan karakter (Adela → Fina, Georgina → Izza)
-------------------------------------------------

Sumber

Dipakai di Drupal

Dialog (Indonesia)

`adela_sceptical1`

`character_fina_heran`

Siapa tahu? x36? y33a? Bisa berarti apa saja.

`ray_listening2`

`character_ray_listening2` (tetap)

Yah, bukan. Sepertinya tanda + yang kedua seharusnya \*.

`adela_sceptical2`

`character_fina_heran`

Akan lebih mudah kalau kita tahu rumus itu untuk apa.

`adela_smile6`

`character_fina_senang`

Sekarang kita tahu apa yang harus dicari di Google…

`adela_smile7`

`character_fina_senang`

Mengubah perilakunya?

Lesson sumber tidak memuat Georgina, sehingga tidak ada pemetaan ke Izza. Hasil render: 0 kemunculan "Adela"/"Georgina".

Dependensi baru yang harus dibuat lebih dulu
--------------------------------------------

Dua tag tidak bisa dirender sebelum node rujukannya ada. Keduanya dibuat baru pada run ini:

*   `principle_incremental_testing` → **nid 371**, tipe `principle`, judul "Pengujian bertahap", alias `/principle/pengujian-bertahap` (HTTP 200). Sebelumnya tipe `principle` ada di instalasi tetapi berisi 0 node.
*   `pattern_hungarian_naming` → **nid 372**, tipe `pattern`, judul "Nama variabel Hungarian", alias `/pattern/nama-variabel-hungarian` (HTTP 200). Field: summary, situation, needs, action (termasuk tabel tipe data).

Tag `principle.` dan `pattern.` sama-sama `has_close_tag = false`: satu baris pembuka plus `&nbsp;&nbsp;internal_name=…`, tanpa penutup.

Temuan teknis (masuk ke skill)
------------------------------

*   **Marker heading-offset tidak boleh menempel pada baris tag.** Bila `annotation.` adalah blok pertama body, menempelkan `<div class="skilling-parse-unit-decrease-heading-offset"></div>` di awal body menghasilkan satu baris logis `<div …></div>annotation.` dan tag tidak dikenali. Marker harus disisipkan tepat sebelum `<h3>` pertama (pola yang dipakai node 344), bukan di awal body.
*   **`principle.` butuh node dulu.** Tanpa node bertipe `principle` dengan `field_internal_name` yang cocok, tag hanya menghasilkan error; tipe bundle-nya sendiri sudah ada di situs ini.
*   **Judul gambar (`title=`) juga harus diterjemahkan**, bukan hanya `alt=` — tema menampilkannya sebagai tooltip/caption (mis. "Kambing", "Ketik beberapa karakter pertama").
*   **Komentar VBA di dalam blok kode ikut diterjemahkan** (`' Compute volume of a cone.` → `' Hitung volume kerucut.`) karena dibaca siswa; kode itu sendiri tidak diubah.
*   String sistem tetap Inggris dan bukan cacat terjemahan: "More…", "Show all", "Toggle Dropdown", serta baris bantuan widget reflect.

Verifikasi
----------

*   `/lesson/easy-good` → 200, `/lesson/mudah-itu-bagus` → 200, `/node/373` → 200
*   Parser error 0, tidak ada markup mentah bocor (`pause.`/`internal_name`)
*   Pager: prev "Cells yang terpisah" (200), Up "Simple Programs" (200); next kosong karena lesson "Debugging" belum ada di buku target
*   Seluruh gambar eksternal (goat4, goat8, 4 PNG Ctrl+Space, foto Ray) → HTTP 200