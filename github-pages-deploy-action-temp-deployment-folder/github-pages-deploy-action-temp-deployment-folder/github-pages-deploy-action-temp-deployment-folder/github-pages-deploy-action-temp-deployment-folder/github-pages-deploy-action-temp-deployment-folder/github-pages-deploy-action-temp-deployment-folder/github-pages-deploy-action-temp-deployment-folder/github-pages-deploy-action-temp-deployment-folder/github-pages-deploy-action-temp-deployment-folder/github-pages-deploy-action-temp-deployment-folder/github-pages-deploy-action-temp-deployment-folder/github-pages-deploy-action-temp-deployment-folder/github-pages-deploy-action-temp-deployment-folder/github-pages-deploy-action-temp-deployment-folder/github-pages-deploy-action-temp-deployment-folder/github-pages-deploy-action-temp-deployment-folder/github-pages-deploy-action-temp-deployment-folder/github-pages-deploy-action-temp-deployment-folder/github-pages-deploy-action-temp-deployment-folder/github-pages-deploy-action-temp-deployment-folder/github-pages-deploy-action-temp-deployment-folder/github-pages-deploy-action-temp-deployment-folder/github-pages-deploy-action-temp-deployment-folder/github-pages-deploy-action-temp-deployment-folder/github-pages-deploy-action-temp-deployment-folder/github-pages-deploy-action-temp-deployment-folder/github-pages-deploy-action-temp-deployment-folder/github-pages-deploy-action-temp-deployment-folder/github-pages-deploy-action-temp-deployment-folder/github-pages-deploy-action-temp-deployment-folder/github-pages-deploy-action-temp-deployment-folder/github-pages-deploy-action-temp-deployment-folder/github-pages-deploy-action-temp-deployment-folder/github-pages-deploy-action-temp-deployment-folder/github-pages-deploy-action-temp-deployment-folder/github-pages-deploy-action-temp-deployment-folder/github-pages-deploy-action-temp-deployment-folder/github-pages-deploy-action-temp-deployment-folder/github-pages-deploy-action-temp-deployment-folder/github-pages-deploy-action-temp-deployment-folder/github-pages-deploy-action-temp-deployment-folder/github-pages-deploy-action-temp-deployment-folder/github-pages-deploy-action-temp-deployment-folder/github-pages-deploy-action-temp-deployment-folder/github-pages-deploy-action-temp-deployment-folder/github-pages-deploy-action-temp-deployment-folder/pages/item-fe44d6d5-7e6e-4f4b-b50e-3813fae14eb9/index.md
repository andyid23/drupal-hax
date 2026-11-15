Ringkasan Infrastruktur
-----------------------

Hasil pemeriksaan konfigurasi server Drupal dan mapping situs web.pesanweb.my.id / belajarweb.pesanweb.my.id.

### 1\. Server home.pesanweb.my.id (192.168.2.40)

*   Docker stack `drupal811`: nginx (drupal\_web\_server, publish 8086:80 dan 8087:81), d11\_fpm (Drupal 11, PHP 8.4), d8\_fpm (Drupal 8, PHP 7.4), drupal\_database (MariaDB), phpmyadmin, mysql-cron-backup harian ke /data/compose/34/db-backup/
*   DB: drupal8, belajarweb, web, drupaldb, appdb

### 2\. Server belajarweb (192.168.2.57, hostname armbian-master)

*   Nginx Proxy Manager (npm\_app) meneruskan belajarweb.pesanweb.my.id ke http://192.168.2.57:8087
*   Stack `drupal-nfs`: nginx, drupal\_fpm7 (Drupal 8), drupal\_fpm, phpmyadmin, mysql-cron-backup, drupal\_db (MariaDB)
*   DB di sini: belajarweb, drupal8, drupal11, drupal8db
*   Multi-site aktif hanya: sites/belajarweb.pesanweb.my.id (DB belajarweb, user belajarweb) dan sites/default (DB drupal8)
*   Folder sites/web.pesanweb.my.id TIDAK ADA di server ini

### 3\. Kesimpulan

web.pesanweb.my.id dilayani oleh home.pesanweb.my.id:8087 (DB drupal8 di sana), sedangkan belajarweb.pesanweb.my.id dilayani oleh server terpisah 192.168.2.57:8087. Untuk menyalin konten web ke belajarweb perlu dump dari DB drupal8 di home.pesanweb.my.id lalu import ke DB belajarweb di 192.168.2.57.