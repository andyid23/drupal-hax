Clone lesson ranges ke vba.pesanweb.my.id
-----------------------------------------

Tanggal: 14 November 2026. Sumber: `https://vba.skilling.us/lesson/ranges` → target node **464**, alias `/lesson/ranges`, judul _Range_, bab _Analisis Data_ (pid 452, weight 1, setelah _Tabel data_).

### Inventaris sumber vs render

*   H3: 10 / Pause: 20 / Karakter: 4 / Kode: 26 — semua sama dengan sumber, urutan widget identik.
*   MCQ: 0. FIB: 1 (`fib_value_in_range`) — node nid 462 dibuat; jawaban **10** bukan ditebak: diambil dari workbook asli `messing-with-a-range.xlsm` (terlampir di halaman sumber) lewat openpyxl — baris 5 range = Shayla, kolom 3 = Coolness = 10.
*   Pattern: 1 (`pattern_create_range`, nid 460, judul "Membuat Range").
*   Exercise: 1 (`exercise_fave_leaf` "Daun favorit", nid 461) — kolom _Pesan_ tabel output sengaja tetap Inggris (string literal yang harus di-output kode siswa).
*   Reflect: 1 (`reflect_remove_row`) — tag `reflect.` dengan penutup `/reflect.`.
*   Annotation: 2 di sumber — "Log in" dilewati (chrome modul untuk anonim), "Note" diterjemahkan jadi blok `annotation. title=Catatan`.
*   Karakter: 3 Ray tetap (yay3, listening1, smiling2 — semua ada di target); Georgina aha1 → `character_izza_heran`.
*   Gambar: 32 tag render, semua external `vba.skilling.us` + probe HEAD 200; dua lampiran .xlsm juga 200.

### Verifikasi lima gerbang

*   HTTP 200: `/lesson/ranges`, `/lesson/range` (pathauto), `/pattern/membuat-range`, `/exercise/daun-favorit`; 403 di `/fib/nilai-di-dalam-range` (wajar, akses anonim ditolak — sama seperti /mcq).
*   errors=0, tidak ada markup mentah bocor, alias lain tetap 200 (better-validation, how-succeed, dkk.).
*   Book nav benar: prev = Tabel data, Up = Analisis Data, next = Tips.

### Temuan teknis baru

*   **Jawaban FIB bisa diambil dari workbook terlampir**: halaman sumber memuat `field--name-field-attachments` (.xlsm). Unduh, baca dengan openpyxl — data tabel diperoleh persis, tanpa OCR. (OCR RapidOCR dipakai dulu untuk memverifikasi konsistensi: kolom Coolness baris Shayla terbaca "10" — cocok.)
*   **Baris book rusak saat insert dengan pid yang node-nya tidak ada**: karena sesi paralel menghapus node chapter lama (447) dan membuat ulang sebagai node 452, insert dengan pid 447 menghasilkan baris rusak (`p1=464, depth=1`) — navigasi prev/next hilang diam-diam. Gejala = persis peringatan runbook tentang raw insert. Perbaikan: hapus baris, insert ulang dengan pid yang VALID (cek ulang tabel book + node chapter sebelum insert).
*   **Sesi paralel bisa mengganti node chapter**: re-query pohon buku (`SELECT nid,bid,pid,weight,title FROM book` + alias) tepat sebelum menentukan pid — jangan percaya hasil query awal sesi.
*   Deadlock `cache_entity` saat create node (transien, sesi paralel + cron): create ulang — create\_lesson.php idempoten karena SKIP jika alias sudah ada.
*   `BookManager::addToOutline()` tidak ada di D8 — pola `$n->book = [...]; $n->save(); updateOutline($n);` tetap yang benar, asalkan pid-nya valid.

### Artifak

*   Builder: `/opt/data/cache/scratch/build_ranges.py`; deps: `deps_ranges.json` + `create_deps_ranges.php` (pattern/exercise/fib); `messing.xlsm` untuk verifikasi jawaban FIB.
*   Runbook `drupal-lesson-authoring` diperbarui dengan temuan di atas.