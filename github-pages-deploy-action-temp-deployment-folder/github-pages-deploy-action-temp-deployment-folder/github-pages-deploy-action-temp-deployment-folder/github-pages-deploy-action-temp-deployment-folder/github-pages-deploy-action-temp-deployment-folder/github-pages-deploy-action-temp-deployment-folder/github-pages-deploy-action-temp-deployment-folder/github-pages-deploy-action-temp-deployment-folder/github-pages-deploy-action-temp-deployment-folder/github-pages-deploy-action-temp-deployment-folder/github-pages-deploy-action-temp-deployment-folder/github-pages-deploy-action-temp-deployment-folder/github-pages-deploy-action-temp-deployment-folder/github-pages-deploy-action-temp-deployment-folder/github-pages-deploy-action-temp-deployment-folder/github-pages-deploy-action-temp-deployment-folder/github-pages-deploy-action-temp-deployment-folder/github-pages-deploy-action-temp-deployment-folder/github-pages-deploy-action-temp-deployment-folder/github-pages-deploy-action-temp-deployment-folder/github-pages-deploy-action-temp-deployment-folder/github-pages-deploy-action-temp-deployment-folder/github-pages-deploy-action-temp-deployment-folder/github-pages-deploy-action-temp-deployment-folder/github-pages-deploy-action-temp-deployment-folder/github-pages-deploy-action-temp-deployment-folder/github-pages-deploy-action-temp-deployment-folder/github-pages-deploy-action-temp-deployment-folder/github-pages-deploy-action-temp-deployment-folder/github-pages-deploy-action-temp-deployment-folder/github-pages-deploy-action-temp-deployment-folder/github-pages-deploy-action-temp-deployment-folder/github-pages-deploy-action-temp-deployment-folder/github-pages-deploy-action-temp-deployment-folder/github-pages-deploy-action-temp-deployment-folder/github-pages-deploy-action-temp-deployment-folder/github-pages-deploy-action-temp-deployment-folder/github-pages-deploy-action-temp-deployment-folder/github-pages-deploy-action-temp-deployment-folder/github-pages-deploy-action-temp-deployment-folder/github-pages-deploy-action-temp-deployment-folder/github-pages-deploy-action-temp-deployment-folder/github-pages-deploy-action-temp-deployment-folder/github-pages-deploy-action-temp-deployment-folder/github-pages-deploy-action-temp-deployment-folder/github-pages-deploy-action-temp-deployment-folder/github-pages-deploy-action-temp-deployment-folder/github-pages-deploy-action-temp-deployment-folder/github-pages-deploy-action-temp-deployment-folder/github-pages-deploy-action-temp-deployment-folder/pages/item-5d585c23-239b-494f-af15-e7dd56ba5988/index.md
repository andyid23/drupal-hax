Ringkasan
---------

Situs `python.pesanweb.my.id` adalah Drupal 8 multisite yang berjalan di container `d8_fpm` + nginx `drupal_web_server` pada host `home.pesanweb.my.id`. Akses publik hostname tersebut saat ini NXDOMAIN, tetapi admin dan render dapat diakses dari dalam host melalui SSH + Drush/curl.

Lingkungan
----------

*   Host: `home.pesanweb.my.id` (SSH port 2222 via Cloudflare Tunnel).
*   Container PHP/Drush: `d8_fpm`, root Drupal `/var/www/html2/drupal8/web`.
*   Container web server: `drupal_web_server`, nginx mendengarkan port 81 (mapping host 8087).
*   Database: MariaDB `drupal_database`, database `python`.
*   Drush versi 10.6.2, Drupal 8.9.20.

Akses SSH ke host
-----------------

SSH ke host menggunakan konfigurasi client:

    ssh -o StrictHostKeyChecking=no -p 2222 root@home.pesanweb.my.id

Koneksi Cloudflare Tunnel bersifat intermittent (kadang `websocket: bad handshake`). Jika gagal, ulangi beberapa kali sampai berhasil.

Akses admin (one-time login link)
---------------------------------

Gunakan Drush dari container `d8_fpm` dengan URI lokal agar tautan login dapat dibuka via tunnel port 8087:

    docker exec d8_fpm /var/www/html2/drupal8/vendor/bin/drush \
      --root=/var/www/html2/drupal8/web \
      --uri=http://localhost:8087 uli

Contoh hasil:

    http://localhost:8087/user/reset/1/<timestamp>/<token>/login

Untuk membuka di browser lokal, buat SSH tunnel:

    ssh -L 8087:localhost:8087 -p 2222 root@home.pesanweb.my.id

Lalu buka tautan `http://localhost:8087/user/reset/.../login`. Tautan hanya berlaku sekali dan kedaluwarsa sesuai token Drush.

Verifikasi render halaman (curl dari container web server)
----------------------------------------------------------

Port 81 digunakan di dalam container; port 8087 hanya ada di host. Contoh verifikasi halaman login:

    docker exec drupal_web_server curl -s -o /dev/null -w "%{http_code}" \
      -H "Host: python.pesanweb.my.id" http://127.0.0.1:81/user/login

Hasil terverifikasi: HTTP 200, title `Log in | Python dengan Hewan Peliharaan`.

Catatan DNS publik
------------------

Hostname `python.pesanweb.my.id`, `vba.pesanweb.my.id`, dan `webapps.pesanweb.my.id` saat ini NXDOMAIN di DNS publik. Konten dan database sudah benar di server; yang perlu ditambahkan adalah route DNS + public hostname tunnel di Cloudflare untuk `python.pesanweb.my.id`.

Verifikasi yang dilakukan
-------------------------

*   SSH ke host berhasil (hostname `portainer`).
*   Drush status untuk site python berhasil (DB `python`, bootstrap sukses).
*   Drush `uli` berhasil menghasilkan one-time login link.
*   Curl ke `http://127.0.0.1:81/user/login` dari container web server mengembalikan HTTP 200.