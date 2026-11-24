Ringkasan
---------

Halaman `https://vba.skilling.us/lesson/exercises-0` ("Exercises") dikloning ke `vba.pesanweb.my.id`. Halaman sumbernya bukan pelajaran biasa: isinya hanya satu gambar, satu kalimat, lalu delapan rujukan latihan.

*   Node: **nid 391** — tipe `lesson`, published, body 881 karakter
*   Judul: **Latihan-latihan**
*   Alias: `/lesson/exercises-0` (manual) dan `/lesson/latihan-latihan` (pathauto) — keduanya 200
*   Book: bid 330, pid 343 ("Simple Programs"), weight -5, depth 3; pager prev "Contoh pengerjaan lain"

Delapan exercise node yang harus dibuat lebih dulu
--------------------------------------------------

internal\_name

nid

Judul

Gambar

`exercise_your_simple_program`

383

Program sederhana Anda

0

`questions1`

384

Pertanyaan

9

`exercise_cylinder_covering`

385

Selubung tabung

6

`exercise_xp`

386

XP

2 + tabel

`exercise_food_order`

387

Pesanan makanan

2

`exercise_goatercise`

388

Senam kambing

3 + 2 tabel

`exercise_bonnie_burns_effect`

389

Efek Bonnie Burns

2

`exercise_mars_shot`

390

Peluncuran ke Mars

2

Semua `field_challenge=0`, body format `skilling`, published. Alias pathauto mengikuti judul Indonesia (`/exercise/selubung-tabung`, dst).

Verifikasi halaman hasil
------------------------

*   `/lesson/exercises-0` 200, `/lesson/latihan-latihan` 200, `/node/391` 200
*   8 blok `exercise.` dirender dengan judul Indonesia; 3 tabel; 28 gambar (26 eksternal semuanya 200, sisanya aset lokal)
*   Parser error 0; tidak ada markup mentah (`internal_name`) bocor; tidak ada URL gambar relatif
*   Sisa teks Inggris hanya string sistem widget: "If you were logged in as a student, you could submit a solution to this exercise."

Temuan teknis
-------------

*   **Alias lesson lain ikut terhapus setiap kali lesson baru dibuat.** Setelah menclone `exercises-0`, alias manual `/lesson/easy-good` (373), `/lesson/more-debugging` (381), dan `/lesson/another-worked-example` (382) semuanya 404, sementara alias pathauto-nya tetap 200. Diperbaiki dengan satu sapuan akhir: pasang ulang alias manual untuk _semua_ node hasil clone, hapus baris duplikat pathauto, lalu `cache:rebuild`. Sekarang 10 URL lesson/alias diperiksa dan semuanya 200.
*   **Halaman daftar latihan = 8 node dependensi + body 881 karakter.** Terjemahan dikerjakan per blok paragraf (kunci = teks asli, nilai = HTML Indonesia dengan `<code>`/`<a>` dipertahankan), tabel diterjemahkan eksplisit, gambar lewat pemetaan alt/title. Kunci yang tidak ketemu akan melempar error, jadi tidak ada paragraf yang hilang diam-diam.
*   **Tautan internal dipetakan ke alias target** (`/lesson/generalize` → `/lesson/generalisasi`, `/lesson/secluded-cells` → `/lesson/cells-yang-terpisah`); tautan unduhan `.xlsm` dibuat absolut ke host sumber.
*   **Materi "Another worked example" (nid 382) sudah selesai** dari sesi sebelumnya dan ikut diverifikasi: 4 gambar 200, 1 tabel, 1 iframe YouTube, 0 parser error, pager prev/next benar.