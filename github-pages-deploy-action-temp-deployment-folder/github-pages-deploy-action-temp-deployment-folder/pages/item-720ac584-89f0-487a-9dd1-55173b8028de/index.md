Clone lesson better-validation ke vba.pesanweb.my.id
----------------------------------------------------

Tanggal: 14 November 2026. Sumber: `https://vba.skilling.us/lesson/better-validation` → target node **420**, alias `/lesson/better-validation`, judul _Validasi lebih baik_, bab _Keputusan_ (pid 392, weight 5).

### Inventaris sumber vs hasil render

*   MCQ: 1 (`mcq_what_is_flag_pattern`) — node dibuat nid 419, jawaban benar A (disimpulkan dari teks pelajaran, dicatat di `field_notes`).
*   Pause: 11 — ditempatkan posisional, urutan widget render identik dengan sumber.
*   H3: 6 (Penanganan error bisa lebih baik, Membersihkan output, Pesan error yang bertahan, Tampilkan semua error sekaligus, Gabungkan semuanya, Latihan).
*   Karakter: 1 — `georgina_smile2` diganti `character_fina_senang` (Fina, dialog "Keren banget! Sederhana.").
*   Pattern: 2 — `pattern_flag` (nid 416) dan `pattern_validation_better` (nid 417), keduanya belum ada di target, dibuat dari halaman `/pattern/flag` dan `/pattern/validation-better` sumber.
*   Exercise: 1 — `exercise_more_dwarves` "Kurcaci lagi?" (nid 418), 6 gambar absolute URL.
*   TOC: 1 (`toc. title=Daftar isi`).
*   FIB: 0 di sumber — tidak ada tag `fib.` di target (sesuai aturan runbook).
*   Gambar body: 13 tag, semua absolute `https://vba.skilling.us/...`, probe HEAD 200.

### Verifikasi lima gerbang

*   HTTP 200: `/lesson/better-validation`, `/lesson/validasi-lebih-baik`, `/pattern/flag`, `/pattern/validasi-lebih-baik`, `/exercise/kurcaci-lagi`; 403 di `/mcq/apa-itu-pattern-flag` (wajar — akses ditolak untuk anonim).
*   Hitungan render == inventaris sumber: 6 h3 / 11 pause / 1 karakter / 1 mcq / 2 pattern / 1 exercise / 1 toc.
*   `custom-tag-error`/`parser-error`: 0. Tidak ada markup mentah yang bocor.
*   Urutan widget (MCQ→P→TOC→H3→CODE…EX) identik dengan sumber.
*   Book nav: prev = Validasi dasar: string, Up = Keputusan, next = Validasi dalam C# — ketiganya 200.

### Temuan teknis baru

*   **Format gambar skilling**: blok `!(figure)<img alt=".." src=".." title=".." />!` harus baris logika TELANJANG — membungkusnya dalam `<p>...</p>` membuat gambar tidak dirender sama sekali (0 gambar body muncul, tanpa pesan error). Bentuk alternatif yang juga valid: `<p class="figure-indent"><img class="skilling-parser-image" ...></p>` seperti yang dipakai node 405.
*   **Cron book-order recompute benar-benar menghapus alias manual** — terbukti langsung di watchdog: `BookUpdateTracker->updatePagesBookOrder()` (11:46) me-re-save semua node buku; alias manual yang baru dibuat hilang dan alias pathauto terduplikasi dua kali. Tidak cukup mem-pin node baru: SEMUA node lesson (dan node dependensi pattern/exercise/mcq!) harus di-pin `path = ['alias' => ..., 'pathauto' => 0]` lewat field `path` node sendiri.
*   **Pin juga node dependensi**: alias `/pattern/flag` hilang walaupun bukan anggota buku — sweep pathauto/pembersihan dari sesi paralel ikut menghapusnya. Pin 416–419 menyelesaikannya.
*   **HTTP 500 transien** saat cron dan sesi clone paralel berjalan bersamaan: deadlock `cache_entity`/`cache_menu` ("Record has changed since last read") di watchdog; halaman kembali 200 tanpa perubahan apa pun setelah rebuild. Jangan langsung debugging isi body ketika 500 muncul bersamaan dengan cron — cek watchdog dulu.
*   **Parallel session**: sesi lain sedang clone lesson di situs yang sama (nid 440 dst. muncul di tengah pekerjaan); dedupe alias global (GROUP BY path, alias TANPA filter status) harus dijalankan ulang di akhir.
*   `global $renderer` tidak berfungsi di `drush php:script` dalam fungsi — definisikan `$renderer = \Drupal::service('renderer')` di dalam tiap fungsi.

### Artifak

*   Builder body: `/opt/data/cache/scratch/build_bv.py`; deps: `deps.json` + `create_deps_bv.php`; finalisasi: `finish_bv.php`.
*   Runbook `drupal-lesson-authoring` diperbarui dengan temuan di atas.