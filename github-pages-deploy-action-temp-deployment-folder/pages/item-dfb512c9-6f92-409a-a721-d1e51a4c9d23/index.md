Kloning lesson: Operator logika (logical-operators) ke vba.pesanweb.my.id
-------------------------------------------------------------------------

Tanggal: 14 November 2026. Sumber: [vba.skilling.us/lesson/logical-operators](https://vba.skilling.us/lesson/logical-operators) → target: [vba.pesanweb.my.id/lesson/logical-operators](https://vba.pesanweb.my.id/lesson/logical-operators) (node 405).

### Hasil

*   Node `lesson` nid **405** "Operator logika", terbit, format body `skilling`, masuk book outline sebagai anak **Keputusan** (nid 392, bid 330, weight 1) — urutan: If-then-else (400) → Operator logika (405).
*   Alias: `/lesson/logical-operators` (manual, langcode en) + `/lesson/operator-logika` (pathauto). HTTP 200 keduanya, plus tetangga /lesson/if-then-else dan /lesson/keputusan tetap 200.

### Node dependensi yang dibuat lebih dulu

Jenis

internal\_name

nid

Catatan

fill\_in\_the\_blank

`fib_expression_value`

401

Soal "Apa nilai sX setelah kode ini berjalan? sX = 2 + 3 \* 4". Jawaban **14** diturunkan sendiri dari presedensi operator (source HTML tidak memuat kunci jawaban), tipe text, menerima "14".

pattern

`pattern_range_check`

402

Terjemahan penuh field situation/needs/action/summary/provides dari /pattern/range-check; alias /pattern/pemeriksaan-rentang (200).

pattern

`pattern_normalize_variable`

403

Dari /pattern/normalize-variable; alias /pattern/normalisasi-variabel (200).

exercise

`exercise_is_tmnt`

404

Task diterjemahkan, 4 gambar URL absolut vba.skilling.us (leo, leo-output, jeremy, donnie-output); field\_challenge=0.

### Cek FIB dan MCQ

*   **FIB: ada 1** widget di source (`fib_expression_value`, data-nid 307). Counting regex kasar memberi 8 karena div pertanyaan/jawaban bersarang membagi substring yang sama — hitung dengan xpath exact-class.
*   **MCQ: 0** di source lesson ini. Tidak ada node MCQ yang dibuat — tidak dipalsukan.
*   Jawaban FIB tidak pernah muncul di HTML sumber (hanya di DB situs sumber), jadi dihitung dari isi pelajaran dan dicatat di `field_notes` node FIB.

### Pemetaan karakter (Adela/Georgia → Fina/Izza)

Source

Target internal\_name

adela\_thinking1

`character_fina_kenapa`

adela\_sceptical2

`character_fina_heran`

georgina\_smile1

`character_izza_senang`

ethan\_neutral1

`character_ethan_neutral1` (tetap)

ray\_thinking4

`character_ray_thinking4` (tetap)

Urutan kemunculan di halaman hasil identik dengan source; semua dialog diterjemahkan.

### Tag textile / pause

*   Body dibangun dengan walk token lxml berurut (149 token), dipisah `<br />\n<br />\n` per blok.
*   **pause.** 17 blok, diposisikan persis di posisi container pause sumber (bukan ditumpuk di akhir).
*   8 h3 (marker decrease-heading-offset sekali, sebelum h3 pertama), 5 character. + /character., 1 reflect. (dengan closer `/reflect.`), 1 fib., 2 pattern., 1 exercise., 22 code container, 2 tabel, 11 gambar top-level.
*   Formulir JS interaktif + `<script>function shipping1()...</script>` dari source lolos parser dan tetap hidup di halaman hasil.

### Verifikasi halaman hasil (5 gate)

*   HTTP 200: /lesson/logical-operators, /lesson/operator-logika, /pattern/pemeriksaan-rentang, /pattern/normalisasi-variabel — semua 200.
*   Widget counts == inventori source: h3 8/8, pause 17/17, character 5/5, reflect 1/1, fib 1/1, pattern 2/2, exercise 1/1, tabel 2/2; urutan widget sama dengan source.
*   Parser errors = 0, tidak ada tag mentah yang bocor (`pause.`/`/character.`/`internal_name=` tidak muncul di HTML).
*   17 URL gambar eksternal unik, semuanya HTTP 200 (probing dengan UA browser — curl telanjang dapat 404 dari vba.skilling.us).
*   DB check path\_alias /node/405: tepat 2 baris (manual + pathauto), tanpa duplikat. Book: nid 405, pid 392, weight 1, depth 3.

### Kendala yang ditemui (catatan teknis)

*   **Blok reflect tanpa closer mengosongkan seluruh body.** Versi pertama body menulis `reflect.` + internal\_name + pertanyaan tanpa `/reflect.`; parser melempar _"Missing/invalid close tag? Missing . at end? Tag: reflect"_ dan seluruh body dirender jadi satu pesan error itu. `reflect.` ternyata _has\_close\_tag = true_ — berbeda dari mcq./fib./pattern./exercise. yang tanpa closer. Perbaikan: tambah `/reflect.` lalu simpan ulang body.
*   **drush sqlq meng-escape newline jadi literal \\n** sehingga sempat terlihat seperti body rusak; verifikasi akhir harus lewat php:script (`substr_count($b,"\n")` = 643, benar).
*   Setiap `save()` node (termasuk update body) menghapus alias manual lagi — alias dibuat ulang di skrip terakhir setelah semua save, baru cache:rebuild.
*   Spesifikasi JSON yang sudah ter-decode tidak boleh di-`base64_decode()` lagi di PHP (menghasilkan JSON rusak).

### Status

Selesai dan terverifikasi penuh. Tidak ada MCQ di sumber; FIB, 2 pattern, dan exercise dibuat sebagai node dependensi sebelum lesson.