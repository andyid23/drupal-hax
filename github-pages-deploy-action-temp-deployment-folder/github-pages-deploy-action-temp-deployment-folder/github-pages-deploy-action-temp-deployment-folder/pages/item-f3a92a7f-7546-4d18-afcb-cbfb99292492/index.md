Rencana: books app dengan MySQL + arsitektur + tautan keluar
------------------------------------------------------------

Jawaban atas tiga pertanyaan: (1) books app ber-MySQL, (2) cara kerja web ber-DB terpisah dari Drupal, (3) inventaris tautan keluar keempat situs. Belum dieksekusi — rencana.

### 1\. Books app dengan MySQL — bisa

*   **Fakta penting: seluruh 200 file `.php` di `/phpexamples/` adalah output yang sudah di-render** (0 tag `<?php`) — server sumber mengeksekusi PHP sebelum unduhan, jadi kode sumber aslinya tidak bisa didapat. Membuat books bekerja dengan MySQL berarti **menulis ulang** app-nya (bukan sekadar clone).
*   App books kecil: 3 halaman (index.php, books.php, authors.php) — di sumber daftarnya **kosong** (DB sumber juga kosong; siswa mengisi sendiri). Re-implementasi ≈ 200 baris CRUD: daftar + tambah books, authors, dan relasi `wrote` (m-n).
*   **Schema tersedia**: `books.sql` dari `webapps.skilling.us/.../attachments/1/books.sql_.zip` — tabel `authors (author_id, first_name, last_name, notes)`, `books (book_id, title, notes)`, `wrote (author_id, book_id)` dengan PK/FK lengkap. _Tanpa data seed_.
*   **PDO mysql tersedia** di d8\_fpm (PHP 7.4.26, pdo\_mysql ✓).

### 2\. Cara kerja: web ber-DB lain + situs statis + Drupal

Satu nginx (drupal\_web\_server, port 81 internal) melayani tiga lapisan, semuanya berdasar path/Host:

Lapisan

Contoh

Yang mengeksekusi

Database

Statis

`/examples/`, `/skilling/`, situs web-contoh

nginx langsung (tanpa PHP, tanpa DB)

—

PHP mandiri

`/phpexamples/…`

php-fpm d8\_fpm:9000 (PHP 7.4)

**DB contoh sendiri** — bukan DB Drupal

Drupal multisite

vba/python/web/webapps

php-fpm → Drupal (sites.php by Host)

DB Drupal per situs

Semua database hidup di satu container `drupal_database` (MariaDB 12.1) tapi **DB dipisah per peran**: Drupal pakai DB-nya masing-masing; contoh PHP akan pakai DB baru `webappexamples` + user khusus — tanpa koneksi apa pun ke DB Drupal. Kelola datanya lewat phpMyAdmin yang sudah ada (`drupal_pma`, port 8083): import books.sql, tambah baris, dll.

`web-contoh.pesanweb.my.id` tidak butuh apa pun selain DNS + proxy host NPM: file statis dilayani catch-all nginx untuk Host apa pun; backend dan port sama dengan situs lain.

### 3\. Tautan keluar — inventaris 194 tautan

Situs

Tautan keluar

Contoh domain terbesar

python

10

wikipedia, docs.python.org, unsplash

vba

25

msdn.microsoft ×3, twitter, youtube, techonthenet

webapps

75

**getbootstrap ×13**, bootswatch, moodle, reclaimhosting, php.net

web

84

**getbootstrap ×12**, **andy1.rf.gd ×20** (hosting lama Anda), google, apache

#### Kategori + solusi

Kategori

Jumlah

Solusi

Dokumentasi/alat resmi (getbootstrap, php.net, xdebug, filezilla, msdn, stackoverflow…)

~70

**Biarkan** — dokumentasi online resmi; lokal-kan cepat basi

**CDN aset** (cdn.jsdelivr.net bootstrap.min.css ×10, stackpath ×2, code.jquery)

~13

**Lokal-kan**: unduh css/js ke `/library/` + rewrite — kalau CDN gagal/offline, tampilan app contoh rusak

Host milik Anda sendiri: **andy1.rf.gd ×20** (web) — sebagian path persis sama dengan mirror `/examples/`

20

**Rewrite ke lokal** yang cocok (grid/, bs-themes/, responsive-grid/…); yang tidak ada di mirror (kucing/, restauran/, cryptid/) → mirror kecil atau biarkan; hosting gratisan bisa mati sewaktu-waktu

Hiburan/karakter konten (cutepuppers, cupcakeipsum, xkcd, paulapoundstone, goatsoap…)

~20

Biarkan; probe — yang 404 permanen di-de-link

`http://localhost/…` (instruksi XAMPP untuk mesin siswa)

2

Biarkan — instruksional

Wikipedia/YouTube/Twitter/lainnya

sisanya

Biarkan

### Rencana (belum dieksekusi)

*   **H1 — Books app CRUD**: buat DB `webappexamples` + user khusus di mariadb eksisting; import books.sql (via phpMyAdmin); tulis ulang index/books/authors.php (+ add/edit, relasi wrote) dengan PDO di `/phpexamples/database/exercises/books/` (file lama render di-backup); verifikasi tambah-lihat-hapus via web.
*   **H2 (opsional, lanjutan)**: re-implementasi app ber-DB lain yang sering dipakai latihan (wombat-list, nobody, dishes) — satu per satu, pola sama dengan H1.
*   **M1 — Lokal-kan CDN aset**: unduh bootstrap.min.css (+jquery/popper yang dipakai) ke `/phpexamples/library/` dan `/examples/…`, rewrite 13 tautan CDN.
*   **M2 — Tautan andy1.rf.gd**: rewrite 20 tautan → `/examples/…` untuk path yang cocok; sisanya keputusan (mirror kecil atau biarkan).
*   **R1 — Probe tautan eksternal**: HEAD-probe 194 tautan, de-link yang mati permanen; tabel hasil dipublikasi.
*   **R2 — Publikasi laporan** (halaman ini + hasil eksekusi nanti).