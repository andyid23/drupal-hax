Hasil: CLONING BERHASIL (2026-10-26)
------------------------------------

DB `drupal8` (web.pesanweb.my.id, home.pesanweb.my.id) berhasil di-clone ke DB `belajarweb` di 192.168.2.57, termasuk sync files. Situs **belajarweb.pesanweb.my.id sekarang menyajikan konten persis web.pesanweb.my.id**.

1\. Eksekusi (urutan waktu)
---------------------------

1.  Backup DB tujuan: `belajarweb-pre-clone-20261026.sql.gz` (3.0M) → `/data/compose/98/db-backup/` di 2.57
2.  Backup files tujuan: `files.bak-pre-clone-20261026` (49M) di `sites/belajarweb.pesanweb.my.id/`
3.  Dump source **clean** (tanpa `--databases` agar tidak bawa CREATE/USE DATABASE): `drupal8-web-clone-clean-20261026.sql.gz` (4.3M) di `/data/compose/34/db-backup/` home
4.  scp dump → `/tmp/` di 192.168.2.57 (via SSH hop, key-based)
5.  `DROP DATABASE belajarweb; CREATE DATABASE belajarweb;` di container `drupal-nfs_drupal_db.1.*` (244 tabel lama terhapus — sudah dibackup)
6.  Import: `zcat dump.sql.gz | docker exec -i <db> mariadb -uroot -p... belajarweb` → IMPORT\_DONE
7.  `rsync -a --delete` files web → files belajarweb (2x pass — pass kedua mengambil artifact css/js yang baru dibuat drush)
8.  `drush cr` di container `drupal-nfs_drupal_fpm7.*` dengan `-r /var/www/html2/drupal8/web -l belajarweb.pesanweb.my.id` → _Cache rebuild complete_

2\. Verifikasi (bukti)
----------------------

Cek

Sebelum clone

Sesudah clone

Hasil

Jumlah tabel DB belajarweb (2.57)

244

246

\= source drupal8 (246) ✓

Jumlah node (node\_field\_data)

—

236

\= source 236 ✓

Daftar nid+title (diff byte-per-byte)

—

—

**NODES\_IDENTICAL** ✓

Jumlah file di sites/.../files

49M

1255 file / 81M

\= source 1255 ✓

Fingerprint konten live (JSON-LD `contentTypeCounts` homepage)

lesson:33, exercise:12, project:12, quiz:1

lesson:83, exercise:44, badge:0, pattern:10, principle:2, model:2

**sama persis dengan web.pesanweb.my.id** ✓

HTTP belajarweb via 192.168.2.57:8087

200

200

✓

Fingerprint paling meyakinkan: homepage belajarweb dulu `lesson:33` (konten lokal lamanya), sekarang `lesson:83, exercise:44, pattern:10, principle:2, model:2` — persis angka web.pesanweb.my.id. Perubahan terlihat di situs publik tanpa perlu login.

3\. Rollback (kalau diperlukan)
-------------------------------

    # di 192.168.2.57
    zcat /data/compose/98/db-backup/belajarweb-pre-clone-20261026.sql.gz | \
      docker exec -i drupal-nfs_drupal_db.1.* mariadb -uroot -p<root_db_pass>
    # backup tsb memakai --databases (mengandung CREATE/USE belajarweb) — aman untuk restore ke nama sama
    rm -rf /data/compose/98/drupal8-src/web/sites/belajarweb.pesanweb.my.id/files
    mv /data/compose/98/drupal8-src/web/sites/belajarweb.pesanweb.my.id/files.bak-pre-clone-20261026 \
       /data/compose/98/drupal8-src/web/sites/belajarweb.pesanweb.my.id/files
    docker exec drupal-nfs_drupal_fpm7.1.* /var/www/html2/drupal8/vendor/bin/drush \
      -r /var/www/html2/drupal8/web -l belajarweb.pesanweb.my.id cr

4\. Catatan pasca-clone
-----------------------

*   Artifact `files/css/` dan `files/js/` (agregasi) akan berbeda sementara sampai belajarweb membuka halaman dan meregenerasi sendiri — bukan masalah, sifatnya cache
*   Tabel `sessions` ikut ter-clone (isi session web) — tidak berbahaya, hangus setelah expiry
*   Path & versi identik (D8.9.20, docroot sama) sehingga dump+import valid tanpa migrasi
*   **Keamanan:** root password DB kedua mesin masih placeholder default — penggantian password tetap disarankan (detail di halaman sebelumnya)
*   File dump/backup sementara: `/tmp/drupal8-web-clone-clean-20261026.sql.gz` di 2.57 bisa dihapus

5\. Lokasi backup & dump
------------------------

File

Mesin

Path

belajarweb-pre-clone-20261026.sql.gz (rollback DB)

192.168.2.57

/data/compose/98/db-backup/

files.bak-pre-clone-20261026 (rollback files, 49M)

192.168.2.57

sites/belajarweb.pesanweb.my.id/

drupal8-web-clone-clean-20261026.sql.gz (source dump)

home.pesanweb.my.id

/data/compose/34/db-backup/