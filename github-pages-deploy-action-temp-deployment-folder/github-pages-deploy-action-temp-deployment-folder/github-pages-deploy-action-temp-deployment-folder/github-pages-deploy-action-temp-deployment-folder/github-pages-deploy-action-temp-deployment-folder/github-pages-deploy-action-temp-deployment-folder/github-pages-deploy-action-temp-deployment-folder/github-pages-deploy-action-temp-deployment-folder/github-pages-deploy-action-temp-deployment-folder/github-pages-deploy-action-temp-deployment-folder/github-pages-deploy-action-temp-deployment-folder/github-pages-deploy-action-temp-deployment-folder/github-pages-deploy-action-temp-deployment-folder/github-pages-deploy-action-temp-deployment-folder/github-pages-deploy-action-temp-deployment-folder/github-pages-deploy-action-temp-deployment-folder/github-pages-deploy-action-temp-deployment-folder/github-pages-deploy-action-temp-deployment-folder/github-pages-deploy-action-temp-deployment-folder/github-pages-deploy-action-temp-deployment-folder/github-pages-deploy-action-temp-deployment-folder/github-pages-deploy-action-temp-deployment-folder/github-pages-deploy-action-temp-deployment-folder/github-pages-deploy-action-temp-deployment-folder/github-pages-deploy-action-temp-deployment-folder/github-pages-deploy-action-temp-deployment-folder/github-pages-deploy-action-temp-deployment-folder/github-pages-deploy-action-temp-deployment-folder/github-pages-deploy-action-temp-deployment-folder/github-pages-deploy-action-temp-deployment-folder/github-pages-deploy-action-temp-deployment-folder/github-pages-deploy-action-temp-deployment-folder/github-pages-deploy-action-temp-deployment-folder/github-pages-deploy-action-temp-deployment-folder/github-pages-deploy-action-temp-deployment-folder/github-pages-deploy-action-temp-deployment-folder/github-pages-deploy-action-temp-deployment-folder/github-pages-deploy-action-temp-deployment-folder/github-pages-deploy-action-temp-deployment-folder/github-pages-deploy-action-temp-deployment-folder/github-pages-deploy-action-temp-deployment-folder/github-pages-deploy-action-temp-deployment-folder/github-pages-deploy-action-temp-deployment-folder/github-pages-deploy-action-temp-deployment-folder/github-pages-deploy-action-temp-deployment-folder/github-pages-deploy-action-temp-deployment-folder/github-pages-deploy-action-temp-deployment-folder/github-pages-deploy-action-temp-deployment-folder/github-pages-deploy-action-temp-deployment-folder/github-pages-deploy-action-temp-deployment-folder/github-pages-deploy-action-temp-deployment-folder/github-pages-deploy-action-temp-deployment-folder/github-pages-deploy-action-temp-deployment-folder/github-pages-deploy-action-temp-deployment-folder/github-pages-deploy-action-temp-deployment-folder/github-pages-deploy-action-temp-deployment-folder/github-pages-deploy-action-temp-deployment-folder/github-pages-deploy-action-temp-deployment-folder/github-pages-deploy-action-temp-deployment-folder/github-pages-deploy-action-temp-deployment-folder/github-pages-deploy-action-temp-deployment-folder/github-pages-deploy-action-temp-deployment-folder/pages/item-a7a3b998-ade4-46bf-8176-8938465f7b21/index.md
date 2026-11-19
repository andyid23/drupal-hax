Status: CLONING BELUM DIJALANKAN — menunggu konfirmasi
------------------------------------------------------

Langkah destruktif (DROP DATABASE + import) dihentikan sistem karena butuh persetujuan eksplisit. **Belum ada data yang diubah.** Semua backup sudah dibuat lebih dulu (lihat §3). Penjelasan diminta pengguna dicatat di §4–§7.

1\. Folder /data/compose di 192.168.2.57 (sebelumnya belum diketahui)
---------------------------------------------------------------------

Isinya: `84 88 92 94 98 104 111` — pola sama dengan home (penomoran urut deployment). Yang aktif hanya **`98`** (stack swarm `drupal-nfs`, dibuktikan via label container `com.docker.compose.project.working_dir` untuk container non-swarm dan nama volume `drupal-nfs_*`). Sisanya snapshot deployment lama (Jul–Des 2025) yang inaktif — arsip/rollback.

Mesin

Folder aktif

Orkestrator

Project

home.pesanweb.my.id (192.168.2.40)

/data/compose/34

docker compose

drupal811 (6 container)

192.168.2.57 (armbian-master)

/data/compose/98

docker swarm

drupal-nfs

2\. Bind mount vs edit di terminal root (nginx-conf)
----------------------------------------------------

Container `drupal_web_server` mem-bind `/data/compose/34/nginx-conf` (host) → `/etc/nginx/conf.d` (container). Karena bind mount, **file di host dan di container adalah file yang sama** (inode sama). Dua cara edit, hasilnya identik:

*   **Edit di host (direkomendasikan)**: `vi /data/compose/34/nginx-conf/drupal.conf` lalu reload: `docker exec drupal_web_server nginx -s reload`
*   **Edit dari terminal root di dalam container**: `docker exec -it drupal_web_server sh` → edit `/etc/nginx/conf.d/drupal.conf`. Editor di container sering tak lengkap (busybox), tapi perubahannya tetap terlihat di host karena bind mount

Perbedaan krusial hanya untuk path yang _bukan_ bind mount: perubahan di dalam container (misal `/etc/nginx/nginx.conf` bawaan image) akan **hilang saat container di-recreate**, sedangkan file di bind mount dan named volume tetap ada. Untuk vhost, selalu edit lewat bind `nginx-conf` di host.

3\. Cara kerja container backup (`drupal_backup` / fradelg/mysql-cron-backup)
-----------------------------------------------------------------------------

**Dibuat via docker compose** — bukan `docker run` manual. Buktinya label container: `com.docker.compose.project=drupal811`, `com.docker.compose.service=mysql-cron-backup`, `com.docker.compose.project.config_files=/data/compose/34/docker-compose.yml`, `com.docker.compose.depends_on=drupal_db:service_started`.

Mekanisme (dari ENV + CMD):

*   CMD: `dockerize -wait tcp://${MYSQL_HOST}:${MYSQL_PORT} -timeout ${TIMEOUT} /run.sh` → tunggu DB (service `drupal_db`, port 3306) benar-benar hidup sebelum mulai
*   `/run.sh` loop: tidur sampai `CRON_TIME=0 3 * * *` (setiap hari pukul 03:00 waktu container), lalu `mysqldump` dengan `MYSQLDUMP_OPTS=--no-tablespaces`
*   `MYSQL_DATABASE` tidak di-set → **dump SEMUA database**, satu file per DB: `<timestamp>.<nama-db>.sql.gz`
*   `GZIP_LEVEL=9`, `MAX_BACKUPS=15` (rotasi: hapus yang lebih lama dari 15), `INIT_BACKUP=1` (langsung backup sekali saat container start)
*   Output ke `/backup` = bind mount host `/data/compose/34/db-backup/`. Ada juga symlink `latest.<db>.sql.gz`

Bukti nyata (Oct 26 03:00): `202610260300.web.sql.gz` (964K), `202610260300.drupal8.sql.gz` (4.2M), `202610260300.drupaldb.sql.gz` (480 byte — DB nyaris kosong), `latest.belajarweb.sql.gz`, dst.

4\. Persiapan cloning web → belajarweb (SUDAH DILAKUKAN, non-destruktif)
------------------------------------------------------------------------

Item

Lokasi

Ukuran

Backup DB belajarweb (2.57) sebelum ditimpa

/data/compose/98/db-backup/belajarweb-pre-clone-20261026.sql.gz

3.0M

Backup files belajarweb (2.57)

/data/compose/98/drupal8-src/web/sites/belajarweb.pesanweb.my.id/files.bak-pre-clone-20261026

49M

Dump clean DB drupal8 sumber (home) — tanpa CREATE/USE DATABASE agar tidak nyasar

/data/compose/34/db-backup/drupal8-web-clone-clean-20261026.sql.gz

4.3M

Catatan teknis: dump pertama memakai `--databases` (mengandung `CREATE/USE DATABASE drupal8`) — kalau diimpor apa adanya ke target, datanya masuk ke DB `drupal8` target (bukan `belajarweb`). Maka dibuat dump _clean_ tanpa flag tersebut.

5\. Rencana eksekusi cloning (DESTRUKTIF — butuh konfirmasi)
------------------------------------------------------------

1.  scp dump clean home → 192.168.2.57:/tmp (via SSH hop, key sudah terpasang)
2.  Di 2.57: `DROP DATABASE belajarweb; CREATE DATABASE belajarweb;` di container `drupal-nfs_drupal_db.1.*` (menghapus 244 tabel lama — sudah dibackup di §4)
3.  Import: `zcat dump.sql.gz | docker exec -i <db> mariadb -uroot -p... belajarweb`
4.  Sync files: `rsync -a --delete` dari `sites/web.pesanweb.my.id/files/` (home) ke `sites/belajarweb.pesanweb.my.id/files/` (2.57) — target sudah dibackup
5.  `drush cr` via container `drupal-nfs_drupal_fpm7.*` (drush ada di `/var/www/html2/drupal8/vendor/bin/drush`, docroot `/var/www/html2/drupal8/web`, URI `-l belajarweb.pesanweb.my.id`)
6.  Verifikasi: jumlah tabel, `curl -H 'Host: belajarweb.pesanweb.my.id' http://192.168.2.57:8087` bandingkan dengan homepage web.pesanweb.my.id

**Efek:** belajarweb.pesanweb.my.id berubah menjadi salinan persis konten web.pesanweb.my.id (artikel/user/config web). Backup §4 bisa dipakai untuk rollback: import ulang `belajarweb-pre-clone-20261026.sql.gz` + restore `files.bak-pre-clone-20261026`.

6\. Temuan keamanan (penting)
-----------------------------

Di kedua mesin, `MYSQL_ROOT_PASSWORD` pada compose/env dan password di `settings.php` **masih berisi placeholder default** (string _your\_drupal\_db\_password_ — belum pernah diganti). DB home membuka port 3306 ke host. Segera ganti password root DB dan password user situs, lalu update settings.php + compose env + restart container. Nilai aslinya sengaja tidak ditulis di halaman ini.

7\. Ringkasan jawaban pertanyaan awal
-------------------------------------

*   **Folder 2.57**: 84, 88, 92, 94, 98, 104, 111 — hanya 98 aktif (swarm drupal-nfs), sisanya snapshot lama
*   **Bind vs terminal root**: bind mount = file host & container sama; edit di host + `nginx -s reload` adalah cara benar; edit dalam container hanya untuk path non-mount yang akan hilang saat recreate
*   **drupal\_backup**: service compose `mysql-cron-backup` (image fradelg), cron harian 03:00, dump semua DB, gzip -9, rotasi 15 file, output ke /data/compose/34/db-backup via bind /backup