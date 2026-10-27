Status Fase 1 hardening (SUDAH DILAKUKAN, 2025-10-26)
-----------------------------------------------------

*   Home: container `drupal_pma` **STOPPED** (Exited) — phpMyAdmin :8083 offline, situs tetap live (D8 port 8087 = HTTP 200)
*   192.168.2.57: service `drupal-nfs_phpmyadmin` di-scale ke **0 replica** (1/0) — :8083 offline
*   Belum ada password yang diubah — ini baru menutup pintu masuk. Fase 2-4 (rotasi password) menunggu keputusan

1\. Kondisi password DB (state, tanpa nilai asli)
-------------------------------------------------

Nilai asli tersimpan di `settings.php` masing-masing situs dan env container — sengaja tidak ditulis di halaman ini. Ringkasan kondisinya:

Mesin

User

Privilege

State password

home (2.40)

root (localhost + %)

superuser

placeholder default (belum diganti)

home

drupal8user

**ALL \*.\* WITH GRANT (superuser)** + drupal8.\*

placeholder default

home

belajarweb

**superuser** + belajarweb.\* + web.\*

**lemah: sama dengan username**

home

web

**superuser** + web.\* (legacy)

**lemah: sama dengan username**

home

drupaluser

USAGE + drupaldb.\*

placeholder default

home

appdbuser

**superuser** + appdb.\*

placeholder default

2.57

root (localhost + %)

superuser

placeholder default

2.57

belajarweb

**superuser** + belajarweb.\*

**lemah: sama dengan username** — hash-nya IDENTIK dengan user belajarweb di home (satu password dipakai dua mesin)

2.57

drupal8user

**superuser** + drupal8db.\*

placeholder default

2.57

drupal11

**superuser** + drupal11.\*

tidak diketahui (hash beda dari placeholder; tidak ada settings aktif yang memakai)

2.57

drupaluser

USAGE + drupaldb.\* (DB tidak ada → orphan)

placeholder default

**Kesimpulan:** tidak ada satupun password yang kuat; 6 user punya hak superuser `ALL ON *.* WITH GRANT OPTION` dengan password placeholder/lemah, tersebar di 2 mesin, port 3306 & 8083 terbuka ke LAN, `root@'%'` aktif di kedua mesin.

2\. Nama database (mana yang Drupal)
------------------------------------

Mesin

DB

Tabel

Drupal?

Dipakai siapa

home (2.40)

drupal8

246

ya (D8)

**web.pesanweb.my.id — LIVE**

home

belajarweb

245

ya (D8)

config multisite belajarweb ADA di home tapi **tidak live** (NPM tidak mengarah ke home untuk domain ini)

home

web

244

ya (D8, 47 node)

**LEGACY** — tidak ada vhost/settings aktif yang memakai

home

appdb

240

ya (D8, 1 node)

**LEGACY/orphan**

home

drupaldb

0

—

default init container, kosong

home

_drupal11 TIDAK ADA di home_ (vhost D11 stale → HTTP 404)

2.57

belajarweb

246

ya (D8)

**belajarweb.pesanweb.my.id — LIVE via NPM** (isi = hasil clone web, 236 node)

2.57

drupal8

244

ya (D8)

tidak ada settings aktif yang mereferensikan → **orphan data**

2.57

drupal11

74

ya (D11)

vhost internal `drupal11.lokal` saja, settings.php 0 byte → **tidak aktif**

2.57

drupal8db

22

bukan

user drupal8user (placeholder) — legacy

3\. Situs apa saja (inventory)
------------------------------

Mesin

Domain

Stack

Akses

DB

Status

home 2.40

web.pesanweb.my.id

compose drupal811, D8 multisite

host 8087 (container :81)

drupal8 / drupal8user

**LIVE**

home 2.40

belajarweb.pesanweb.my.id

config multisite ada

—

belajarweb / belajarweb

tidak live (dilayani 2.57)

home 2.40

(D11, port 8086/80)

compose drupal811

—

tidak ada

**stale → 404**; settings.php berupa DIREKTORI (rusak)

2.57

belajarweb.pesanweb.my.id

swarm drupal-nfs, D8 multisite

NPM → 192.168.2.57:8087 (container :81)

belajarweb / belajarweb

**LIVE** (isi = clone web)

2.57

drupal11.lokal (internal)

swarm, vhost :80

internal only

drupal11 (settings.php 0 byte)

tidak aktif

2.57

(default D8)

—

—

settings = copy default (placeholder)

tidak terkonfigurasi

NPM di 2.57 juga merute `tty.drupal.lokal → 192.168.2.52:2222` dan `curhatmu.my.id → 192.168.2.52:81` (bukan mesin ini). Multisite resolusi murni by folder nama domain (`sites.php` default, tanpa alias custom).

4\. Langkah akses DB (dari mesin lokal)
---------------------------------------

1.  **SSH hop** (key sudah terpasang, passwordless): `ssh home.pesanweb.my.id` lalu `ssh root@192.168.2.57`. Dari luar LAN, ini satu-satunya path aman — port 3306/8083 tidak boleh dibuka ke internet
2.  **CLI mysql/mariadb** lewat docker exec:
    *   home: `docker exec -it drupal_database mariadb -uroot -p<pass> -e "SHOW DATABASES;"`
    *   2.57: `docker exec -it drupal-nfs_drupal_db.1.<id> mariadb -uroot -p<pass> -e "SHOW DATABASES;"`
3.  **Lewat Drupal (drush)**: home `docker exec d8_fpm vendor/bin/drush -r web -l <domain> sqlq "SHOW TABLES;"`. Di 2.57 container fpm7 **tidak punya mysql client** → `drush sqlq` gagar; gunakan db container langsung (atau `drush sql:connect` untuk lihat DSN-nya)
4.  **GUI phpMyAdmin**: SAAT INI OFFLINE kedua mesin (Fase 1). Butuh sementara? `docker start drupal_pma` (home) lalu segera stop lagi — lebih baik pakai ssh tunnel + container PMA sementara dengan password kuat
5.  **Baca kredensial situs**: `grep -A10 "databases\['default'\]\['default'\]" sites/<domain>/settings.php`

5\. Kenapa ganti password bisa/bukan memutus Drupal
---------------------------------------------------

*   Drupal baca `settings.php` tiap request — tidak ada cache kredensial. `ALTER USER` duluan = gap error 500 sampai settings diperbarui
*   **Metode bebas putus**: CREATE USER baru + grant → swap settings.php (atomic `mv`) → verifikasi HTTP 200 → DROP user lama. Situs tidak pernah putus
*   **Root tidak dipakai Drupal** (dipakai backup container + PMA) — rotate root tidak memutus situs, tapi memutus cron backup & PMA sampai container di-recreate. Karena compose file hilang, rekonstruksi dulu (Fase 0 di halaman runbook)
*   Jangan lupa: home punya 2 settings aktif (web, belajarweb), 2.57 punya 1 (belajarweb) — update SEMUA yang mereferensikan user yang diubah

6\. Temuan kritis lain (untuk runbook)
--------------------------------------

*   Compose file `/data/compose/34/docker-compose.yml` **hilang** di home (container jalan, label menyebut file itu) — rekonstruksi dari `docker inspect` atau Portainer sebelum recreate container
*   Home: `drupal11-src/web/sites/default/settings.php` berupa **direktori** (deploy rusak) → vhost D11 404
*   2.57: `drupal11-src/.../settings.php` = **file 0 byte**; `drupal8-src/.../default/settings.php` = salinan default (placeholder) → D11 & default D8 tidak terkonfigurasi
*   Hash password `belajarweb` sama di kedua mesin → satu password dipakai dua mesin, harus di-break jadi password berbeda per mesin saat rotasi

Referensi
---------

*   Runbook ganti password lengkap (Fase 0-4): slug `keamanan-phpmyadmin-runbook-password-db`
*   Hasil cloning DB web → belajarweb: slug `hasil-cloning-db-web-belajarweb`