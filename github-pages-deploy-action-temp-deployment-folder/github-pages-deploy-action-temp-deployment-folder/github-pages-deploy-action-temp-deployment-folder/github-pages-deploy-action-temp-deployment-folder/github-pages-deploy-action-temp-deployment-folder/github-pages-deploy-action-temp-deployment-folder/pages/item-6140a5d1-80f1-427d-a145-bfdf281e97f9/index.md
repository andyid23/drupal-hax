Laporan T1: Kloning 3 pelajaran yang hilang ke vba.pesanweb.my.id
-----------------------------------------------------------------

Audit situs vba menemukan 3 pelajaran dari `vba.skilling.us` yang belum ada di target. Ketiganya sudah di-clone, diverifikasi penuh, dan diterbitkan pada 2026-11.

### Node yang dibuat

Sumber

Target (nid)

Alias

Bab

/lesson/debugging

517 "Debugging"

/lesson/debugging

Simple programs (pid 343)

/lesson/feedback

518 "Umpan balik"

/lesson/feedback

Getting ready (pid 334)

/lesson/exercises

519 "Latihan"

/lesson/exercises

Getting ready (pid 334)

### Dependency nodes yang dibuat lebih dulu

*   `principle_read_code_actively` (nid 511) — "Baca kode secara aktif"
*   `exercise_circle_bug_hunt` (nid 512) — "Berburu bug lingkaran"
*   `exercise_joke_cute_animal` (nid 513) — "Lelucon, dan hewan lucu"
*   `mcq_missing_email` (nid 514), `mcq_wrong_initials` (nid 515), `mcq_changing_password` (nid 516) — jawaban benar disimpulkan dari konteks (C spam folder / B Account>Edit / B reset password) dan dicatat di field\_notes karena sumber tidak menandai jawaban benar di HTML

### Pemetaan karakter

Ray dan Ethan dipertahankan (node-nya ada di DB). Adela aha1 & smile2 → `character_fina_senang`; Georgina smile5 → `character_izza_senang`. Semua 9 variasi yang dipakai terverifikasi ada di `node__field_internal_name`.

### Hasil verifikasi (5 gerbang)

*   HTTP 200 di semua alias (manual + pathauto + alias dependency): /lesson/debugging, /lesson/feedback, /lesson/exercises, /lesson/umpan-balik, /lesson/latihan, /principle/baca-kode-secara-aktif, 2 alias /exercise/
*   Hitungan widget = inventaris sumber: debugging 6 h3 / 11 pause / 6 karakter / 4 reflect / 1 catatan / 1 principle / 1 latihan; feedback 3 h3 / 4 pause / 1 karakter / 2 catatan / 2 MCQ; exercises 5 h3 / 7 pause / 2 karakter / 1 MCQ / 1 latihan
*   `custom-tag-error`/`parser-error` = 0 di ketiga halaman; tidak ada tag mentah bocor
*   Heading semua render sebagai h3 (0 h2/h1/h4); 33 URL eksternal + 2 GIF karakter lokal semuanya 200; ketiga MCQ merender pertanyaan + semua opsi (6 dan 3 respons)
*   Nav book benar: Debugging prev=easy-good next=more-debugging; Umpan balik prev=Latihan next=ketika-anda-mengalami-kebuntuan; Latihan prev=menggunakan-situs-ini next=Umpan balik

### Perbaikan ikutan yang ikut dikerjakan

*   Re-space bobot buku bab Simple programs sesuai urutan sumber (13 anak; sekaligus memperbaiki tabrakan bobot -13 antara nid 349 dan 362 yang membuat urutan String salah)
*   Memperbaiki salah tempatnya "Profesormu" (nid 453, bobot -12 bertabrakan dengan "Bagaimana kursus berjalan" → dipindah ke bobot 3, urutan Getting ready kini benar)
*   Dedupe global `path_alias`: 14 baris duplikat dihapus (termasuk /node/508 kunci-pintasan-vba dan 12 node karakter), tanpa filter status
*   Alias manual ketiga lesson di-pin dengan `pathauto=0` agar tahan save/cron

### Kondisi setelah T1

Book kini memuat 64 lesson (semua lesson sumber terpetakan kecuali yang masih jadi agenda T2: 3 node yatim 262/338/345). Sisa T3: rekonsiliasi urutan bab Keputusan, Analisis Data, dan Tips (bobot saat ini belum persis urutan sumber). Judul "Latihan" dipilih agar alias tidak bentrok dengan nid 391 (/lesson/latihan-latihan).