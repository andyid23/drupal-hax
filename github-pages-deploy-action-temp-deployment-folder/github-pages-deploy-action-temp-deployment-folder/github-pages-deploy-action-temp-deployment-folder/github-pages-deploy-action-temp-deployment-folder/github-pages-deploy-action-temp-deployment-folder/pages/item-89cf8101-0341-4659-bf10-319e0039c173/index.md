Keputusan yang Dipakai
----------------------

*   **Penamaan vhost non-breaking** — domain lama tetap dilayani direktori aslinya; nama-DB ditambahkan sebagai alias. Tidak ada URL publik yang berubah.
*   **Sumber tunggal** — DB `drupal8` dan direktori `sites/web.pesanweb.my.id/files` di home adalah sumber sync.
*   **NPM dan DNS tidak disentuh** — entry proxy dan record DNS akan dibuat terpisah untuk pengujian lokal.

Hasil Akhir: Enam DB Identik
----------------------------

    home/drupal8     246 tabel  236 node  md5 94dd3460…
    home/belajarweb  246 tabel  236 node  md5 94dd3460…
    home/web         246 tabel  236 node  md5 94dd3460…
    2.57/belajarweb  246 tabel  236 node  md5 94dd3460…
    2.57/drupal8     246 tabel  236 node  md5 94dd3460…
    2.57/web         246 tabel  236 node  md5 94dd3460…

Sebelum sync: home/belajarweb 117 node (basi), home/web 47 node (legacy), 2.57/drupal8 47 node (orphan), 2.57/web belum ada. Semua target dibackup lebih dulu ke `db-backup/*-pre-sync-*.sql.gz`.

Pemetaan Nama
-------------

### home.pesanweb.my.id (2.40)

*   `sites/web.pesanweb.my.id/` — direktori asli, DB `drupal8` (sumber)
*   `sites/drupal8` — symlink ke `web.pesanweb.my.id`
*   `sites/belajarweb` — symlink ke `belajarweb.pesanweb.my.id`
*   `sites/web/` — direktori baru, DB `web`, files hasil sync
*   `sites.php`: `drupal8.pesanweb.my.id → drupal8`, `web-legacy.pesanweb.my.id → web`, domain lama tetap ke direktorinya masing-masing

### 192.168.2.57

*   `sites/belajarweb.pesanweb.my.id/` — direktori asli, DB `belajarweb`
*   `sites/drupal8/` — direktori baru, DB `drupal8`
*   `sites/web/` — direktori baru, DB `web`
*   `sites/web.pesanweb.my.id` — symlink ke `drupal8`, supaya referensi file lama tetap resolve
*   `sites.php`: `drupal8.pesanweb.my.id → drupal8`, `web.pesanweb.my.id → drupal8`, `web-legacy.pesanweb.my.id → web`

Verifikasi
----------

*   Keempat Host header di home:8087 dan 2.57:8087 mengembalikan judul situs yang benar, dan aset contoh `manja-vitolic-gKXKBY-C-Dk-unsplash.jpg` HTTP 200.
*   `drush cr` sukses untuk `web.pesanweb.my.id`, `drupal8.pesanweb.my.id`, `belajarweb.pesanweb.my.id`, `web-legacy.pesanweb.my.id` di kedua host.
*   Direktori files tiap situs berisi 310 file `gambar` setelah sync.

Otomasi
-------

*   **File (terjadwal)**: `/root/sync-haxcms-files.sh` di home, cron `/etc/cron.d/haxcms-files-sync` harian 02:30. Fan-out ke 5 target (2 lokal + 3 di 2.57), hanya direktori konten; `css/`, `js/`, `php/`, `config_*` dikecualikan karena aggregate per-situs.
*   **DB (manual, destruktif)**: `/root/sync-db-from-drupal8.sh --force`. Men-dump `drupal8`, membackup tiap target, lalu DROP+CREATE+import ke `belajarweb` dan `web` di home, serta ke tiga DB di 2.57. Sengaja tidak dijadwalkan: import ulang menghapus perubahan lokal di target dan memutus koneksi sesaat.

Catatan Penting
---------------

*   Sync file bersifat aditif (tanpa `--delete`) supaya unggahan yang dibuat langsung di salah satu situs tidak hilang.
*   Aggregate `css/js/php` boleh dihapus kapan saja; Drupal membuatnya ulang saat halaman diminta. Ini alasan direktori `belajarweb` di home sempat terlihat lebih besar (489 file aggregate lama) padahal kontennya hanya 59 gambar.
*   Karena semua DB kini identik, isi keenam situs sama. Kalau salah satu akan dipakai berbeda, hentikan sync file untuk situs itu lebih dulu, lalu cabut targetnya dari skrip.
*   Domain `drupal8.pesanweb.my.id` belum ada di DNS; pengujian saat ini hanya lewat Host header internal.