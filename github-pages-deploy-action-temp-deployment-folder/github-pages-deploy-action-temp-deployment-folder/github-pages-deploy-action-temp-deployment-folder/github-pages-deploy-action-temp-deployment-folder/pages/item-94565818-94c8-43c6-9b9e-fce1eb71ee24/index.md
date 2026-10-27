A. Penjelasan rollback cloning (pertanyaan: bagaimana cara pakainya)
--------------------------------------------------------------------

Rollback = mengembalikan `belajarweb.pesanweb.my.id` di 192.168.2.57 ke keadaan **sebelum** cloning (konten lama belajarweb, bukan konten web). Dipakai kalau hasil clone bermasalah: konten salah, ada modul/config yang bentrok, atau ada data penting yang ternyata hanya ada di belajarweb lama.

**Tiga langkah (urutan wajib):**

1.  **Import backup DB** — file `belajarweb-pre-clone-20261026.sql.gz` di `/data/compose/98/db-backup/` (2.57). Dump dibuat dengan `--databases` jadi sudah berisi `CREATE/USE belajarweb` — aman di-restore ke nama DB yang sama. Perintah: `zcat file.sql.gz | docker exec -i <db-container> mariadb -uroot -p<pass>` (tanpa menyebut nama DB di akhir, karena USE sudah di dalam dump). Ini menimpa 246 tabel hasil clone kembali menjadi 244 tabel lama.
2.  **Restore files** — hapus folder files hasil clone, kembalikan backup: `rm -rf .../sites/belajarweb.pesanweb.my.id/files && mv .../files.bak-pre-clone-20261026 .../sites/belajarweb.pesanweb.my.id/files`. Wajib, karena file hasil clone (81M dari web) sudah menggantikan yang lama (49M).
3.  **Rebuild cache** — `docker exec <fpm7> /var/www/html2/drupal8/vendor/bin/drush -r /var/www/html2/drupal8/web -l belajarweb.pesanweb.my.id cr`, lalu verifikasi homepage (fingerprint `contentTypeCounts` harus kembali ke `lesson:33, exercise:12`).

**Berapa lama rollback tersedia?** Backup DB + files.bak tersimpan di 2.57 sampai dihapus manual. Selain itu ada `drupal_backup` (mysql-cron-backup) yang dump SEMUA DB setiap hari pukul 03:00 ke `/data/compose/*/db-backup/` (RPO maks 24 jam, rotasi 15 file) — lapisan kedua kalau rollback manual tidak cukup.

B. phpMyAdmin :8083 tanpa password — TIDAK AMAN, urgensi TINGGI
---------------------------------------------------------------

**Sebbab "bisa masuk tanpa password":** container `drupal_pma` (home) dan service phpmyadmin (2.57) di-set `PMA_USER=root` + `PMA_PASSWORD=<placeholder>` → phpMyAdmin mode auto-login: tidak ada form login sama sekali, siapa saja yang membuka :8083 langsung masuk sebagai **root database**.

Fakta (diverifikasi 2025-10-26)

home.pesanweb.my.id (2.40)

192.168.2.57

phpMyAdmin auto-login root

ya (PMA\_USER=root, port 0.0.0.0:8083)

ya (port ingress 8083)

Password root DB

**masih placeholder default** (string _your\_drupal\_db\_password_ — belum pernah diganti, berlaku di kedua mesin)

Port DB 3306

published 0.0.0.0:3306

ingress 3306 (LAN)

User root remote

root@'%' aktif

root@'%' aktif

User aplikasi

drupal8user, belajarweb, web, drupaluser, appdbuser — semua host '%'

belajarweb, drupal8user, drupaluser, drupal11 — semua '%'

Compose file

**HILANG** — /data/compose/34/docker-compose.yml tidak ada (label container menyebutnya, filenya tidak)

perlu cek ulang sebelum rotate

**Eksposur internet:** dari luar, `http://home.pesanweb.my.id:8083` tidak merespons (Cloudflare tunnel tidak membuka port itu) — jadi kemungkinan besar tidak langsung terbuka ke internet. TAPI: (1) siapa saja di LAN (perangkat tamu, IoT, laptop yang pernah connect) bisa root kedua DB; (2) kalau router mem-forward 8083/3306 dari luar, jadi kritis; (3) password-nya adalah string placeholder yang tercantum di dokumentasi publik image compose. **Verdict: anggap bocor, segera tutup.**

**Kenapa urgent sekarang:** setelah cloning, konten web (236 node, 81M files) tersimpan di 2 DB di 2 mesin — permukaan serangan jadi dua kali; backup harian ada tapi RPO 24 jam; satu perintah DROP dari PMA bisa menghapus semua DB sekaligus.

C. Apakah Drupal putus koneksi DB saat password diganti?
--------------------------------------------------------

**Ya — kalau urutannya salah.** Drupal membaca kredensial dari `settings.php` setiap request. Kalau `ALTER USER` dijalankan duluan, Drupal masih pakai password lama → error koneksi (halaman 500) sampai `settings.php` diperbarui. Gap-nya detik, tapi pada situs live tidak nyaman.

**Metode zero-downtime (user baru, bukan ALTER user live):**

1.  CREATE USER baru dengan password kuat + GRANT yang sama
2.  Tulis settings.php baru (atomic `mv`), request berikutnya sudah pakai user baru — situs tidak pernah putus
3.  Verifikasi HTTP 200 + `drush sqlq "SHOW TABLES;"`
4.  DROP user lama

**Khusus root:** root TIDAK dipakai Drupal (dipakai `drupal_backup` dan phpMyAdmin). Ganti root tidak memutus situs, tetapi memutus backup cron & PMA sampai container di-recreate dengan env baru — dan karena compose file hilang, rekonstruksi compose dulu (Fase 0).

D. Runbook ganti password (4 fase)
----------------------------------

### Fase 0 — Persiapan

*   Rekonstruksi `docker-compose.yml` project drupal811 dari `docker inspect` 6 container (env, mounts, ports, networks) — atau ambil dari Portainer (stack tersimpan di Portainer DB)
*   Generate password kuat (password manager). **Jangan simpan password di halaman HAX, git, atau skill**
*   Backup tiap settings.php: `cp settings.php settings.php.bak-TANGGAL`

### Fase 1 — Hardening segera (menit, tanpa downtime situs)

*   Home: `docker stop drupal_pma` (PMA offline, situs tetap jalan)
*   2.57: `docker service scale drupal-nfs_phpmyadmin=0`
*   Opsional: iptables/ufw batasi 8083 & 3306 ke LAN saja

### Fase 2 — Password user aplikasi (metode zero-downtime)

Mesin

Situs

DB

User

settings.php

home (2.40)

web.pesanweb.my.id

drupal8

drupal8user

/data/compose/34/drupal8-src/web/sites/web.pesanweb.my.id/

home (2.40)

belajarweb (tidak live di sini)

belajarweb

belajarweb

/data/compose/34/drupal8-src/web/sites/belajarweb.pesanweb.my.id/

home (2.40)

default fallback

drupal8

drupal8user

/data/compose/34/drupal8-src/web/sites/default/

2.57

belajarweb.pesanweb.my.id (LIVE)

belajarweb

belajarweb

/data/compose/98/drupal8-src/web/sites/belajarweb.pesanweb.my.id/

2.57

default fallback

drupal8

(settings masih placeholder)

/data/compose/98/drupal8-src/web/sites/default/

2.57

drupal11

drupal11

drupal11

/data/compose/98/drupal11-src/web/sites/default/

User lain di home (web, drupaluser, appdbuser) cek dulu dipakai situs apa sebelum diganti.

### Fase 3 — root, PMA, backup (least privilege)

*   Buat user khusus `backup`: GRANT SELECT, LOCK TABLES, RELOAD, SHOW VIEW, EVENT, TRIGGER ON \*.\* → update env container `drupal_backup` (MYSQL\_USER/MYSQL\_PASS) → recreate. mysqldump --all-databases cukup dengan privilege itu
*   PMA: matikan auto-login (hapus PMA\_USER/PMA\_PASSWORD), auth\_type cookie; atau beri user pma terbatas. Recreate container
*   ALTER root (localhost + %), lalu **DROP root@'%'** — root cukup localhost
*   Recreate `drupal_backup` & `drupal_pma` via compose hasil rekonstruksi (Fase 0)

### Fase 4 — Verifikasi & cleanup

*   curl tiap domain → 200; drush sqlq OK; log drupal\_backup jalan (cek file baru di db-backup pukul 03:00 berikutnya); PMA login manual pakai password baru
*   Hapus dump sementara: `/tmp/drupal8-web-clone-clean-20261026.sql.gz` (2.57), `/tmp/web_nodes.txt` (home)

E. Rollback kalau ganti password gagal
--------------------------------------

*   Situs error setelah swap settings: kembalikan `settings.php.bak-TANGGAL` (metode user baru: user lama masih ada sampai Fase 2 selesai → tinggal kembalikan settings)
*   Lupa password root setelah ALTER: start ulang container mariadb dengan `skip-grant-tables` (volume data tetap) — prosedur darurat MariaDB standar
*   Rollback cloning DB (kalau diperlukan): lihat §A di atas

F. Referensi terkait di situs ini
---------------------------------

*   Hasil Cloning DB web → belajarweb (BERHASIL) — slug: `hasil-cloning-db-web-belajarweb`
*   Cloning DB web ke belajarweb — Status & Penjelasan — slug: `cloning-db-web-belajarweb-2-57`
*   Hasil Cek Config Server 192.168.2.57 — slug: `hasil-cek-belajarweb-192-168-2-57`