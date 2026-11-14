Clone lesson vladidation-c ke vba.pesanweb.my.id
------------------------------------------------

Tanggal: 14 November 2026. Sumber: `https://vba.skilling.us/lesson/vladidation-c` (node sumber 6289, judul "Vladidation in C#" — typo di sumber) → target node **445**, judul _Validasi dalam C#_, alias manual `/lesson/vladidation-c`, bab _Keputusan_ (bid 330, pid 392, weight 8).

### Inventaris sumber vs hasil render

*   Karakter: 6 → 6, urutan container identik dengan sumber: `character_izza_senang`, `character_ethan_smile14`, `character_ray_skeptical1`, `character_ray_smiling3`, `character_fina_senang`, `character_izza_masuk`.
*   Code container: 2 → 2 (blok VBA + blok C#).
*   Pause: 0 di sumber (tidak ada `skilling-pause-container` di HTML sumber) → 0 di target. Body tetap textile-safe: join `<br />`, tanpa `|`, tanpa bare `<br>`.
*   FIB/MCQ: 0 widget di HTML sumber — tidak ada node fib/mcq yang dibuat atau direferensikan lesson ini. Situs vba punya 17 fib + 9 mcq nodes, tapi tidak satu pun direferensikan halaman ini.
*   H3: 0 → 0; marker heading-offset tidak dipakai (sumber tanpa heading).

### Pemetaan karakter (Adela→Fina, Georgina→Izza; Ray/Ethan dipertahankan)

*   `georgina_smile5` → `character_izza_senang` (nid 64)
*   `georgina_neutral1` → `character_izza_masuk` (nid 65)
*   `adela_smile8` → `character_fina_senang` (nid 79)
*   Dipertahankan: `ethan_smile14` (nid 285), `ray_skeptical1` (nid 320), `ray_smiling3` (nid 258)

### Terjemahan

*   Prose, dialog karakter, komentar kode VBA (`'...`) dan C# (`//...`), serta catatan KRM di dalam code container — semuanya diterjemahkan ke Indonesia.
*   Literal string kode tetap Inggris (`Sorry, meal cost must be a number.` dst.) supaya cocok dengan file latihan; kode dan identifier tidak diubah — termasuk typo sumber `sTpRate` (dicatat di `field_notes`).
*   Foto karakter: Fina/Izza dari file lokal situs (`IMG_7699.gif`/`IMG_7740.gif`/`IMG_8335.gif`, probe 200); Ray/Ethan tetap memakai foto eksternal `vba.skilling.us` (probe 200 dengan browser UA) — kondisi lama node karakter tersebut, bukan bagian dari clone ini.

### Perbaikan book chapter Keputusan (temuan sesi ini)

*   Tabrakan weight dari sesi clone paralel: nid 444/440/420 bertiga-tiganya berada di weight 5. Dinormalisasi ke urutan sumber: 444 w5, 440 w6, 420 w7, 445 w8.
*   `exercises-1` (nid 436, "Latihan-latihan 2") salah tempat di bawah Simple Programs (pid 343) — padahal di buku sumber ia anak terakhir Decisions, tepat setelah vladidation-c. Dipindahkan ke pid 392 weight 9; alias manualnya di-pin ulang.
*   Lesson yang disentuh (420/440/444/445) plus 436 di-pin alias manualnya lewat field `path` node (`pathauto=0`) agar tahan re-save cron `BookUpdateTracker`; baris `path_alias` ganda dibersihkan (dedupe GROUP BY path, alias tanpa filter status).

### Verifikasi

*   HTTP 200: `/lesson/vladidation-c` dan `/lesson/validasi-dalam-c`; sweep 45 alias lesson di situs semuanya 200.
*   Render node 445: 6 character container, 2 code container, 0 pause, 0 h3, 0 `custom-tag-error`, 0 `parser-error`, tidak ada markup tag mentah yang bocor.
*   Book nav: prev `/lesson/better-validation`, Up `/lesson/keputusan`, next `/lesson/exercises-1` — sesuai nav sumber.
*   Baris alias DB node 445 tepat 2: `/lesson/validasi-dalam-c` (pathauto) + `/lesson/vladidation-c` (manual, ter-pin).