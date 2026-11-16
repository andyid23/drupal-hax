Plan agen: klon bab "Validation library" ke webapps.pesanweb.my.id
------------------------------------------------------------------

_Plan, bukan eksekusi. Referensi: `references/clone-runbook.md` dari skill `drupal-lesson-authoring` (bagian 0–11). Tanggal inventaris: 2026-11-15._

### 1\. Status terverifikasi

*   Sumber: `https://webapps.skilling.us/lesson/validation-library` — 1 bab + 10 anak (validation-basics, lets-play-evil-php-validation, multifield-validation, dont-cross-streams, validation-functions, code-library, paths, functions-and-teams, project-mortgage-payments, fun-rps-cheater).
*   Target: `webapps.pesanweb.my.id` (multisite D8 di home.pesanweb.my.id, container `d8_fpm` + `drupal_web_server`, port 81). HTTP saat ini: `/lesson/validation-library`, `/lesson/validation-basics`, `/lesson/database-apps`, `/lesson/capturing-data` semua **404** → jalur CREATE, bukan UPDATE.
*   Target sudah berisi klon bab sebelumnya: Bersiap (nid 247), URL dan file (nid 351), Membuat HTML (nid 409, 24 anak sampai "Komponen halaman" nid 433). Book root bid=331, chapter depth 2.
*   Stok node target: character 83, exercise 17, fill\_in\_the\_blank 16, multiple\_choice\_question 19, pattern 7, **principle 0**.

### 2\. Inventaris bab Validation library (exact-class, body region)

Halaman

h3

pause

char

mcq

fib

pattern

principle

exercise

code

img

validation-library (intro)

0

0

0

0

0

0

1

0

0

0

validation-basics

8

22

12

3

0

3

1

0

41

22

lets-play-evil-php-validation

0

0

0

0

0

0

0

0

0

0

multifield-validation

7

14

1

1

0

1

0

1

19

12

dont-cross-streams

0

0

0

0

0

0

0

0

0

0

validation-functions

12

24

10

0

0

0

2

1

46

22

code-library

1

2

0

0

0

0

1

0

3

1

paths

0

0

0

0

0

0

0

0

0

0

functions-and-teams

0

1

3

0

0

0

0

0

2

4

project-mortgage-payments

0

0

0

0

0

0

0

1

0

0

fun-rps-cheater

3

3

0

0

0

0

0

0

5

2

**TOTAL**

**31**

**66**

**26**

**4**

**0**

**4**

**5**

**3**

**116**

**64**

Aset eksternal: 30 `img` dari `webapps.skilling.us` (URL absolut, biarkan), 3 iframe YouTube.

### 3\. Jawaban cek spesifik

*   **cek fib mcq**: `fib.` = **0** di seluruh bab (tidak ada node fill\_in\_the\_blank yang perlu dibuat). `mcq.` = **4**: validation-basics id sumber 1207, 1189, 1206; multifield-validation id 1191. Buat 4 node `multiple_choice_question` baru (internal\_name baru `mcq_*`, jawaban benar tidak ditandai di HTML sumber — infer dari konteks, catat di field\_notes).
*   **cek karakter Adela/Georgina → Fina/Izza**: 26 kontainer karakter, 17 pose unik. Semua pose yang dibutuhkan sudah ada di target dengan prefix `character_` (83 node karakter). Pemetaan sesuai aturan: Adela `smile*` → `character_fina_senang`, Adela `sceptical1/2` → `character_fina_heran`; Georgina `smile1/4` → `character_izza_senang`. Ray (neutral1, neutral3, smiling2) dan Ethan (smile1/2/5/12/15, talking5): node ada di target → **keep** + terjemahkan dialog saja (sesuai aturan keep-Ray/Ethan; opsi recast ke Hafidz/Wildan hanya jika diminta). Validasi seluruh `internal_name=character_*` terhadap DB sebelum transport, bukan setelah render.
*   **tag textile pause**: 66 widget pause, kelas render sumber `skilling-pause-container btn-group`. Di body ditulis `pause.` satu baris, WAJIB dipisah blok dengan baris kosong (`<br />\n<br />\n` join) — tanpa terminator `\n\n` tag options memakan sisa body (kasus nid 467/470).
*   **selfie-holder**: tidak ada di bab ini → jangan ditambahkan.
*   **pattern/principle/exercise**: pattern dibutuhkan `pattern_simple_output` (sudah ada, nid 364), `pattern_flag` (sudah ada, nid 366), `pattern_validation_chain` (baru), `pattern_multifield_validation` (baru). Principle: `principle_reuse` ×4 + `principle_dont_trust_user_data` ×1 — bundle principle di webapps **0 node**, wajib dibuat dulu atau tag merah error. Exercise: `exercise_goat_fuel`, `exercise_parade_length`, `exercise_mortgage_payments` (semua baru).

### 4\. Fase eksekusi agen (jika disetujui)

*   **F0 — Muat konteks**: skill `drupal-lesson-authoring` + `references/clone-runbook.md` §0–§11; discovery environment (§1); re-verify 404 (§2).
*   **F1 — Fetch + inventaris**: 11 halaman sumber ke scratch; inventory per halaman dengan exact-class match (bukan substring — nested widget menggelembungkan hitungan); ekstrak urutan blok dengan lxml `iterchildren()`.
*   **F2 — Dependency nodes dulu** (§5, idempoten by `field_internal_name`, save dalam `executeInRenderContext`): 2 pattern, 2 principle, 3 exercise, 4 MCQ. MCQ: `field_question` + paragraphs bundle `multiple_choice_question_respons` (field\_response, field\_correct, field\_explanation) — diterjemahkan lengkap (prosa Indonesia, angka kode tetap).
*   **F3 — Build body** per halaman (§6): join `<br />\n<br />\n` antar blok; satu `skilling-parse-unit-decrease-heading-offset` sebelum h3 pertama; `pause.` 66×; blok `character.` bentuk   `internal_name=...` + dialog; terjemahan (§6l): prosa + dialog + alt/title + judul node Indonesia, literal kode & angka tabel tetap; `<pre><code>` disalin verbatim; tanpa karakter `|` di body (ganti dengan `&#8250;`).
*   **F4 — Create node** (§7): batch via `scripts/create_deps.php` lalu `scripts/create_lessons.php` (LESSON\_SPECS); abort bila ada inventaris yang belum lengkap — jangan node ber-body kosong.
*   **F5 — Book outline** (§9): parent dulu — chapter "Pustaka validasi" bid=331, pid=331, depth 2, weight 4 (setelah 409 w=3); 10 anak depth 3, weight urut sidebar sumber; `$n->book = [...]` + `updateOutline()` dengan nid+original\_bid.
*   **F6 — Alias pin** (§8/8b): pin `path` field `pathauto=0` di SEMUA node baru termasuk dep nodes; dedupe global `GROUP BY path, alias` tanpa filter status; baru `cache:rebuild`.
*   **F7 — Verifikasi 5 gerbang** (§11) per 11 halaman: HTTP 200 dengan `Host: webapps.pesanweb.my.id`; hitungan h3/pause/char/mcq/pattern/principle/exercise/code/img == inventaris F1; 0 `parser-error`; semua `src` eksternal 200; urutan widget == urutan sumber. Grep hasil dibatasi region body (judul pelajaran lain di sidebar bisa memicu false positive "fina").
*   **F8 — Penerbitan hasil**: catat outcome (nid, alias, weight, dedupe) ke halaman HAXCMS baru dan push.

### 5\. Risiko yang sudah diketahui

*   Cron BookUpdateTracker menghapus alias manual menit-menit setelah save → pin + re-assert + cek DB (bukan cuma HTTP 200 cache).
*   Tunnel SSH flaky (websocket bad handshake) → retry 2–5 s.
*   Sesi klon paralel bisa menabrak weight/alias yang sama → re-query sibling saat insert; jalankan dedupe di akhir setiap sesi.
*   Hitungan substring `<img`/`skilling-code-container` menggelembung oleh internal widget → bandingkan per kelompok (body vs exercise-task vs throbber).

### 6\. Bab lanjutan: database-apps & capturing-data (inventaris, belum dieksekusi)

*   **database-apps** (14 anak + intro): h3 78, pause 117, char 24 (20 pose unik — semua ada di target), mcq 0, fib 0, pattern 0, principle `principle_reuse` ×2, exercise 6 (`exercise_id_from_get`, `exercise_supernatural_accuracy`, `exercise_books_app`, `exercise_1_n`, `exercise_wombat_queries`, `exercise_wombat_list` — semua baru), code 140, img 155.
*   **capturing-data** (15 anak + intro): h3 74, pause 107, char 24 (7 pose unik), mcq 0, fib 0, pattern 2 (`pattern_same_page_validation`, `pattern_separate_input_from_processing` — baru), principle 5 (`principle_get_requirements_right`, `principle_reduce_cognitive_complexity`, `principle_confirm_destructive_action`, `principle_reuse` ×2), exercise 6 (`exercise_add_wombat`, `exercise_pokemon_evolutions`, `exercise_sales_tax_form`, `exercise_your_app`, `exercise_pokemon_tickets`, `exercise_wombat_games` — semua baru), code 170, img 156.
*   Kedua bab TANPA mcq/fib → tidak ada node kuis dibuat untuk keduanya; jejak `fib`/mcq kosong adalah temuan nyata, bukan langkah terlewat.
*   Urutan disarankan bila lanjut: Validation library → database-apps → capturing-data (urutan sidebar sumber).