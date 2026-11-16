Ringkasan
---------

Lesson `https://vba.skilling.us/lesson/more-debugging` ("More debugging") dikloning ke `vba.pesanweb.my.id` dan diterjemahkan ke Bahasa Indonesia. Semua gate verifikasi lulus.

*   Node: **nid 381** — tipe `lesson`, published, body 12.960 karakter (format `skilling`)
*   Judul: **Debugging lebih lanjut**
*   Alias: `/lesson/more-debugging` (manual) dan `/lesson/debugging-lebih-lanjut` (pathauto) — keduanya HTTP 200
*   Book: bid 330, pid 343 ("Simple Programs"), weight -7, depth 3; pager prev "Mudah itu bagus"

Inventaris sumber vs hasil render
---------------------------------

Widget

Sumber

Hasil (nid 381)

`h3`

5

5

`pause.`

8

8

`character.`

11

11

`mcq.`

2

2

`exercise.`

1

1

`annotation.`

3

3

`reflect.`

1

1

`toc.`

1

1 (judul "Daftar isi")

Blok kode

1

1

Gambar

13 di body + 2 di exercise + 1 di MCQ + 11 foto karakter

semua 200, tidak ada URL relatif

Parser error

—

0

Urutan widget (_sequence diff_, 33 token) identik dengan sumber; perbedaan hanya teks terjemahan dan nama karakter hasil recast.

Pemetaan karakter (Adela → Fina, Georgina → Izza)
-------------------------------------------------

Sumber

Dipakai di Drupal

Catatan

`adela_smile2`, `adela_smile3`

`character_fina_senang`

ramah/positif

`adela_pc_3`

`character_fina_masuk`

pose di depan komputer, netral

`adela_pc_1`

`character_fina_kenapa`

pose duduk/menjelaskan

`adela_pc_2`

`character_fina_heran`

mengamati nilai variabel

`adela_oh_no1`

`character_fina_sedih`

kaget/menyesal

`georgina_pc_2`

`character_izza_senang`

berterima kasih

`ray_listening2`, `ray_smiling3`

`character_ray_listening2`, `character_ray_smiling3`

Ray dipertahankan

Semua 11 blok karakter mendapat foto dari node (tidak ada gambar karakter inline). Teks yang menyebut nama juga disesuaikan: "Hai, Fina!", "Fina bisa memperbaikinya", "terima kasih sudah menunjukkan itu, Fina". Hasil render: 0 kemunculan "Adela"/"Georgina".

Dependensi baru yang dibuat lebih dulu
--------------------------------------

*   `mcq_breakpoint` → **nid 378**, judul "Apa itu breakpoint?", 4 pilihan, jawaban benar C ("Menghentikan program, sehingga Anda bisa mengintip isi memori"). Jawaban benar tidak ada di HTML sumber — diturunkan dari isi pelajaran dan dicatat di `field_notes`.
*   `mcq_locals_window` → **nid 379**, judul "Apa yang ditampilkan jendela Locals VBE?", 4 pilihan, jawaban benar B ("Nilai variabel yang ada di memori").
*   `exercise_goat_party_planner` → **nid 380**, judul "Perencana pesta kambing", `field_challenge=0`, tugas HTML lengkap dengan tabel biaya dan dua gambar (200). Alias `/exercise/perencana-pesta-kambing` 200.

Bundle `multiple_choice_question` di vba ternyata sudah berisi node (4 sebelumnya) — jadi tag `mcq.` memang bisa dirender di situs ini, bukan harus diganti teks statis.

Temuan teknis (masuk ke skill)
------------------------------

*   **Alias lesson yang sudah selesai bisa hilang saat mengerjakan lesson berikutnya.** Setelah node 381 dibuat dan masuk book outline, alias manual `/lesson/easy-good` (nid 373) ikut terhapus dan muncul baris pathauto ganda. Perbaikannya: di akhir sesi, pasang ulang alias manual untuk _semua_ node yang pernah dikloning, hapus baris duplikat, lalu `cache:rebuild`. Tanpa rebuild, alias yang dihapus masih menyajikan 200 dari cache sementara alias baru justru 404.
*   **Tag `toc.` tidak butuh node**, tetapi judul defaultnya tetap "Lesson contents" (Inggris) — gunakan opsi `title=Daftar isi`.
*   **Jawaban benar MCQ tidak pernah ada di HTML sumber** (semua opsi berkelas `response-active`); harus diturunkan dari teks pelajaran.
*   **Path `/mcq/<slug>` membalas 403 untuk anonim** — itu halaman access-denied untuk node yang ada, bukan tanda node gagal dibuat; verifikasi lewat widget di dalam lesson.
*   **Builder lxml:** `tostring(el).strip()` membuang _tail text_ anak, yaitu tempat baris kosong antar-paragraf dialog karakter berada. Akibatnya dialog multi-paragraf tidak pernah cocok dengan tabel terjemahan. Gunakan `with_tail=True` dan strip hanya pada hasil gabungan.

Verifikasi
----------

*   `/lesson/more-debugging`, `/lesson/debugging-lebih-lanjut`, `/node/381`, `/lesson/easy-good`, `/lesson/mudah-itu-bagus`, `/lesson/cells-yang-terpisah`, `/exercise/perencana-pesta-kambing` → 200
*   Parser error 0; tidak ada markup mentah bocor; 18 URL gambar eksternal semuanya 200
*   Pager: prev "Mudah itu bagus"; next kosong karena lesson "Another worked example" belum ada di buku target