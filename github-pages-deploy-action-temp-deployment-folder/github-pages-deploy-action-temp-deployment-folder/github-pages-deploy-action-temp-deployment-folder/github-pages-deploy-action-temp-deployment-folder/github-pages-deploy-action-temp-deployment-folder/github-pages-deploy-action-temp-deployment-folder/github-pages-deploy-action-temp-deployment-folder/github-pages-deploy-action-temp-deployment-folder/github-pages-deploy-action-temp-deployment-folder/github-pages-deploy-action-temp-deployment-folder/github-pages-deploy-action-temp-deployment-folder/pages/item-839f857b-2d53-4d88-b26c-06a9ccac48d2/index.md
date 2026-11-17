Laporan: Audit situs python.pesanweb.my.id vs python.skilling.us
----------------------------------------------------------------

Audit penuh sumber `python.skilling.us` (72 lesson, 8 top-level) vs target, inventaris DB, audit render 79 halaman, census gambar `selfie-holder`, dan perbaikan. Tanggal 2026-11.

### Temuan utama

*   Struktur sumber: How the course works (11 anak), Name tags (8), Useful calculators (8), Validation (9), Reuse (13), Data analysis (13), What now? (standalone), Extras (2).
*   Target hampir lengkap: 7 dari 8 top-level ada; urutan 6 dari 7 bab sudah benar; `parser-error` = 0 di 79 halaman.
*   **Node yang belum ada: `what-now` ("What now?")** — satu-satunya lesson sumber hilang.
*   Node 868 "Adela functionates" ada di book tapi tanpa alias.
*   Duplikat: 907 "Cleaning" (EN 94,8 KB) vs 916 "Pembersihan Data" (ID, in book); 1014 "Data types" (EN 2 KB) vs 936 "Tipe Data" (ID, in book); 1015 "Code standards" unik tapi di luar book.
*   Sampah struktur: 7 node uji unpublished (781–787) dengan book tree sendiri + 3 design\_page (796–798) dengan book tree bid 796.
*   Duplikat baris `path_alias`: 22 lesson + 4 rute webform.
*   Urutan menyimpang di bab 247 (using-site/exercises/uploading-python/about-me teracak, bobot tabrakan -11 dan -10).

### Selfie-holder (census sumber vs target)

Sumber hanya memakai `<p class="selfie-holder">` di 5 lesson; target menyimpang di semuanya: 417 (3 slot — 1 slot diganti kelas `student-image`, 1 sudah ada gambar substitusi dengan kelas benar, 1 hilang), 874 (hilang), 875 (1 hilang, 1 tanpa kelas), 871 (gambar di task exercise tanpa kelas), 955 (tanpa kelas).

### Perbaikan yang dieksekusi

*   **what-now dibuat** (nid 1022 "Apa selanjutnya?", alias `/lesson/what-now`, outline root w=5 antara Analisis Data dan Ekstra; nav terverifikasi prev=latihan-analisis, next=ekstra).
*   **868**: alias `/lesson/adela-functionates` ditambahkan.
*   **1015** masuk book di bawah 903 (w=-6, sebelum Tipe Data 936) — urutan Extras kini sesuai sumber.
*   **Bab 247** di-re-space urutan sumber (373…371), tabrakan bobot hilang; alias di-pin ulang.
*   **Deduplikat di-unpublish** (reversible): 907, 1014; sampah 796–798 di-unpublish; book tree uji bid 781/796 dihapus dari tabel book.
*   **Selfie-holder**: 417 (kelas student-image → selfie-holder; lifevest.png disisipkan), 874 (goats-spaceship1), 875 (marcs-dog + two.jpg dibungkus), 955 (chico + caption digabung), exercise 700 (Flavor\_Flav + kredit WikiMedia). Verifikasi akhir: ke-8 gambar kini di dalam `p.selfie-holder`.
*   **Dedupe global path\_alias**: 30+ baris duplikat dihapus (termasuk 4 rute webform).

### Insiden: situs down saat perbaikan & pemulihan node 868

Sesaat setelah gelombang save, seluruh halaman 502/500. Watchdog menunjuk `BookManager->bookLinkTranslate()` pada null: **node 868 kehilangan baris `node_revision` dan `node__body` — dihapus oleh proses di luar sesi ini** (watchdog juga mencatat "Data types has been deleted" ×2 dari proses lain; node lain seperti Goatcam/Jape Joint/Wellermen/Mad lib sempat dihapus sebelumnya). Baris book yatim menyebabkan crash pada setiap render.

Pemulihan: baris book yatim dilepas → situs kembali 200; kemudian node 868 direkonstruksi dari sumber `python.skilling.us/lesson/adela-functionates` (baris `node_revision` disisipkan ulang, body Indonesia baru 5,5 KB: 1 h3, 3 pause, 9 karakter — Adela→Fina, Georgina→Izza, Ray/Ethan exact variants — 4 reflect, 1 catatan, 6 code container), book dipulihkan (pid 865, w=-13, urutan functions → adela-functionates → practice-functions sesuai sumber), alias di-pin. Verifikasi akhir: HTTP 200, errors=0, semua hitungan sesuai sumber.

### Status akhir

*   Semua 8 lesson sumber top-level terpetakan; urutan book penuh sesuai sumber; 0 error render.
*   Kegagalan tunggal tersisa: tanda tanya penghapusan oleh proses luar — pemilik situs disarankan memeriksa cron/job lain yang menghapus konten.