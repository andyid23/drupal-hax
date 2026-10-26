Ringkasan
---------

Hasil cek langsung ke mesin **192.168.2.57** (hostname `armbian-master`) via SSH hop dari `home.pesanweb.my.id`. Konfirmasi: **belajarweb.pesanweb.my.id live di 192.168.2.57**, bukan di home.pesanweb.my.id. Sumber kebenaran routing ada di tabel `proxy_host` Nginx Proxy Manager (NPM) yang berjalan di mesin ini.

1\. Routing NPM (otoritatif)
----------------------------

Query langsung ke DB NPM (`npm_db`, database `npm`, tabel `proxy_host`):

id

domain

forward

enabled

1

belajarweb.pesanweb.my.id

http://192.168.2.57:8087

ya

2

tty.drupal.lokal

http://192.168.2.52:2222

ya

3

curhatmu.my.id

http://192.168.2.52:81

ya

**web.pesanweb.my.id TIDAK ada di NPM ini** — domain itu disajikan oleh nginx di home.pesanweb.my.id (port 8087 host → vhost :81 container → Drupal 8). Dua situs dengan nama mirip, dua mesin berbeda.

2\. Docker stack `drupal-nfs` di 192.168.2.57
---------------------------------------------

Container (swarm service)

Image

Fungsi

`drupal-nfs_drupal_nginx.1.*`

nginx:1.29-alpine

Web server, 2 vhost (lihat §3)

`drupal-nfs_drupal_fpm7.1/2.*`

drupal:8.9.20-php7.4-fpm-alpine3.13

PHP-FPM Drupal 8 (2 replica)

`drupal-nfs_drupal_fpm.1/2/3.*`

drupal:11.2.2-php8.4-fpm-alpine3.21

PHP-FPM Drupal 11 (3 replica)

`drupal-nfs_drupal_db.1.*`

mariadb:latest

DB server, volume `drupal-nfs_drupal_db_data`

`drupal-nfs_phpmyadmin.1.*`

linuxserver/phpmyadmin:5.2.2

phpMyAdmin

`drupal-nfs_mysql-cron-backup.1.*`

fradelg/mysql-cron-backup

Auto-backup DB harian (healthy, jalan 2 minggu)

`npm_app.1.*` / `npm_db.1.*`

jc21/nginx-proxy-manager / mariadb-aria

Reverse proxy + DB konfigurasinya

Compose project root: `/data/compose/98/` (isi: `drupal8-src`, `drupal11-src`, `drupal8_files`, `drupal8_data`, `nginx-conf`, `db-backup`, `dump`, `import`, `images`, `snippets`, `template`, `private`).

3\. vhost nginx di mesin ini (`/data/compose/98/nginx-conf/drupal.conf`)
------------------------------------------------------------------------

listen

server\_name

root

fastcgi\_pass

80

drupal11.lokal

/var/www/html/drupal11/web

drupal\_fpm:9000 (D11)

81

\_ (default)

/var/www/html2/drupal8/web

drupal\_fpm7:9000 (D8)

Komentar di file conf sendiri menulis: port 81 container di-map ke **8087 di host** — itulah port yang dituju NPM. Situs D8 pakai multisite: Host header `belajarweb.pesanweb.my.id` → folder `sites/belajarweb.pesanweb.my.id/`.

4\. Database (MariaDB `drupal-nfs_drupal_db.1.*`)
-------------------------------------------------

Database

Jumlah tabel

Keterangan

belajarweb

244

**Pakai belajarweb.pesanweb.my.id** (user `belajarweb`, host `drupal_db`)

drupal8

244

Fallback `sites/default`

drupal11

74

Situs Drupal 11 (vhost :80)

drupal8db

22

Tidak dipakai multisite aktif

5\. Multi-site & settings.php
-----------------------------

*   `sites/belajarweb.pesanweb.my.id/` — **satu-satunya situs aktif**, DB `belajarweb`
*   `sites/default/` — fallback multisite; DB `drupal8` (file settings-nya masih menyisakan placeholder contoh `databasename` di bagian komentar — hati-hati saat baca)
*   **Tidak ada** folder `sites/web.pesanweb.my.id/` di mesin ini — membuktikan web.pesanweb.my.id bukan situs mesin ini

6\. Verifikasi HTTP dari home.pesanweb.my.id
--------------------------------------------

*   `curl -H 'Host: belajarweb.pesanweb.my.id' http://192.168.2.57:8087` → **HTTP 200**, title _"Selamat datang | Belajar Membuat Website"_ (tema skilling: 33 lesson, 12 exercise, 12 project, 1 quiz)
*   Host lain (misal `web.pesanweb.my.id`) juga dapat 200 karena mengenai vhost default (`server_name _`) — tapi di produksi NPM tidak pernah mengarahkan domain itu ke sini

7\. Rencana ekspor DB web → belajarweb (antar mesin)
----------------------------------------------------

1.  Backup DB `belajarweb` di 192.168.2.57 dulu (wajib — import menimpa total)
2.  Dump DB `drupal8` (punya web.pesanweb.my.id) dari container `drupal_database` di home.pesanweb.my.id
3.  Pindahkan file dump ke 192.168.2.57 (scp via hop) lalu import ke DB `belajarweb` di container `drupal-nfs_drupal_db.*`
4.  Sync `files/`: dari `sites/web.pesanweb.my.id/files/` (home) ke `sites/belajarweb.pesanweb.my.id/files/` (192.168.2.57)
5.  Rebuild cache: `drush cr` di `drupal-nfs_drupal_fpm7.*` untuk domain belajarweb

**Peringatan:** jangan import ke DB `belajarweb` yang ada di home.pesanweb.my.id — itu DB konteks lain. Tujuan benar adalah DB `belajarweb` di MariaDB mesin 192.168.2.57. Versi Drupal sama (8.9.20) dan path sama, jadi dump+import valid; D8→D11 nanti butuh migrasi penuh, bukan sekadar dump.