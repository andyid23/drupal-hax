Install scroll\_top\_button, Sync DB/Files & Persiapan 3 Situs Baru (python/webapps/vba)
========================================================================================

Laporan eksekusi 27 Oktober 2026 — stack `drupal811` di home.pesanweb.my.id (192.168.2.40) dan stack `drupal-nfs` di 192.168.2.57.

1\. Install module scroll\_top\_button
--------------------------------------

*   **Versi:** `drupal/scroll_top_button` 2.1.1 — kompatibel Drupal `^8.8 || ^9 || ^10 || ^11` (terverifikasi dari drupal.org), aman untuk Drupal 8.9.20.
*   **home (d8\_fpm):** `composer require drupal/scroll_top_button:^2.1` berhasil; module di-ENABLE di `web.pesanweb.my.id` lewat `drush pm:enable scroll_top_button -y` + `drush cr`.
*   **2.57 (fpm7):** container masih Composer **1.10.23** yang gagal dengan error _“Illegal string offset 'versions'”_. Di-upgrade ke Composer 2.10.3 (`composer self-update --2`), lalu require berhasil.
*   Catatan: alias `drush en` gagal (_“Too many arguments”_) pada build Drusih ini — pakai `pm:enable`.
*   `composer audit` melaporkan **150 security advisory pada 15 package** di codebase Drupal 8 — PR usang, tinjau terpisah.

2\. Sync DB + files ke belajarweb (kedua host)
----------------------------------------------

Prosedur: backup target → dump source tanpa `--databases` → DROP/CREATE (collation target sendiri) → import → rewrite referensi file → `drush cr` scoped per domain → rsync files additive (exclude `/css/ /js/ /php/` ter-anchor).

### home (2.40): drupal8 → belajarweb

*   DB `belajarweb`: 246 tabel, 236 node, digest **identik** dengan `drupal8` (`md5=94dd346050c38bbd6b6f6897465ff142`).
*   Referensi file di-rewrite: `sites/web.pesanweb.my.id` → `sites/belajarweb.pesanweb.my.id` (82 baris `node__body`: 0 lama / 82 baru).
*   Files: rsync dari `sites/web.pesanweb.my.id/files/`; verifikasi internal `curl -H 'Host: belajarweb.pesanweb.my.id' 127.0.0.1:8087` → **200**.

### 2.57: dump fresh dari home drupal8 → belajarweb (LIVE via NPM)

*   DB `belajarweb`: 246 tabel, 236 node, digest identik; `scroll_top_button` hadir di `core.extension` dan `drush pm:list` scoped `belajarweb.pesanweb.my.id`.
*   Referensi di-rewrite (82 → 0/82), files rsync dari home (408 → 409 file, parity).
*   Verifikasi publik: `https://belajarweb.pesanweb.my.id/` → **200**, title “Selamat datang | Belajar Membuat Website”; gambar konten `files/gambar/TE-2022-100000-depan.jpg` → **200**.
*   `web.pesanweb.my.id` publik tetap **200** (tidak terganggu).

### Backup (rollback)

*   home: `/data/compose/34/db-backup/belajarweb-pre-sync-2026-10-27-0713.sql.gz` + `belajarweb-pre-refsync-2026-10-27-071736.sql.gz`; files: `sites/belajarweb.pesanweb.my.id/files.bak-pre-sync-2026-10-27-0713`.
*   2.57: `/data/compose/98/db-backup/belajarweb-pre-sync-2026-10-27-1418.sql.gz` + `belajarweb-pre-refsync-2026-10-27-141957.sql.gz`; files: `files.bak-pre-sync-2026-10-27-1418`.
*   Rollback: import backup _dengan_ `--databases`, restore files.bak, lalu `drush -l <domain> cr`.

3\. Tiga situs baru (materi python/webapps/vba.skilling.us)
-----------------------------------------------------------

Disiapkan di home (2.40) sebagai clone konfigurasi `web.pesanweb.my.id` — DB, files, dan seluruh config module (termasuk `scroll_top_button`) identik; konten bisa langsung diganti/ditambah lewat UI.

Domain

Database

Direktori sites

Status

python.pesanweb.my.id

`python`

`sites/python.pesanweb.my.id/`

200, 246 tabel, digest parity, media 200

webapps.pesanweb.my.id

`webapps`

`sites/webapps.pesanweb.my.id/`

200, 246 tabel, digest parity, media 200

vba.pesanweb.my.id

`vba`

`sites/vba.pesanweb.my.id/`

200, 246 tabel, digest parity, media 200

### Rincian tiap situs

*   **DB:** dibuat dengan collation host target (`utf8mb4_uca1400_ai_ci` di home), import dump clean `drupal8` — 246 tabel, 236 node, digest identik dengan source; user DB memakai `drupal8user` (lihat catatan keamanan).
*   **settings.php:** hasil sed dari `sites/drupal8/settings.php` — `'database' => '<nama>'` dan `config_sync_directory => 'sites/<domain>/sync'` (sync config terpisah per situs).
*   **Files:** `sites/<domain>/files/` terisi konten dari `sites/web.pesanweb.my.id/files/` (exclude agregat); referensi di-rewrite ke dir sendiri (82 → 0/82) sehingga gambar tampil di domain baru.
*   **sites.php:** alias identitas ditambahkan (`$sites['python.pesanweb.my.id'] = 'python.pesanweb.my.id'`, dst.) — pola sama dengan entri lama, non-breaking.
*   **Cache:** `drush -l <domain> cr` per situs; title “Selamat datang | Belajar Membuat Website” di ketiganya.

### Nginx Proxy Manager (2.57) — sudah disiapkan

*   3 baris `proxy_host` (id 4–6): python/webapps/vba.pesanweb.my.id → `http://192.168.2.40:8087`, enabled, cache + block-exploits aktif.
*   Karena NPM **tidak** meregenerate nginx config dari insert SQL langsung (hanya via API/UI), file conf ditulis meniru template (`proxy_host/4.conf`–`6.conf` dari `1.conf`) lalu `nginx -t && nginx -s reload`. Baris DB tetap sumber kebenaran; saat entri di-save lewat UI nanti, NPM akan regenerate semua conf dengan resmi.
*   Verifikasi lewat NPM (Host header): ketiganya **200** dengan title situs Drupal; `belajarweb` tetap 200 (tidak ada downtime berkelanjutan).

4\. Langkah tersisa (user)
--------------------------

1.  **DNS:** buat record A/CNAME untuk `python.pesanweb.my.id`, `webapps.pesanweb.my.id`, `vba.pesanweb.my.id` di Cloudflare → IP publik NPM **103.189.201.134** (proxy orange-cloud sesuai pola `belajarweb`). Domain baru saat ini belum resolve.
2.  **SSL:** setelah DNS propagasi, minta Let's Encrypt per domain di NPM (tab SSL) — HTTP sudah jalan.
3.  **Konten:** login `/user/login` tiap domain (kredensial user 1 ter-clone dari drupal8), ganti title/slogan lewat _Configuration → System → Basic site settings_, lalu tambah materi baru (modul `skilling`, `skilling_content_types`, `webform`, `paragraphs` sudah aktif).
4.  **Ganti password user 1** di tiap situs sebelum dipublikasikan (kredensial ter-clone).

5\. Catatan keamanan (state, bukan nilai)
-----------------------------------------

*   User DB app (`drupal8user`, `belajarweb`) memakai **password placeholder/lemah** dan punya grant `ALL *.* WITH GRANT`; `root@'%'` aktif di kedua host; MariaDB 3306 terekspos. phpMyAdmin sudah di-stop (hardening sebelumnya) — pertahankan.
*   Password `belajarweb` di home dan 2.57 masih **identik** (satu password, dua mesin) — pisahkan saat rotasi (runbook `keamanan-phpmyadmin-runbook-password-db`).
*   Composer audit: 150 advisory pada 15 package (Drupal 8 EOL-ish stack) — jadwalkan migrasi Drupal 11.

6\. Ringkasan verifikasi
------------------------

Pemeriksaan

Hasil

scroll\_top\_button enabled di web.pesanweb.my.id, belajarweb (2.57), python, webapps, vba

✓ (core.extension + pm:list)

Digest node\_field\_data drupal8 = belajarweb(home) = belajarweb(2.57) = python = webapps = vba

✓ 236 node, md5 sama

Referensi file setelah rewrite

✓ 0 ref domain lama, 82 ref domain sendiri (tiap DB target)

HTTP 200 (internal & publik/edge)

✓ web, belajarweb, python, webapps, vba

Gambar konten (node/48, files/gambar/…)

✓ 200 di semua domain

Backup pre-sync tersimpan

✓ kedua host (DB + files)