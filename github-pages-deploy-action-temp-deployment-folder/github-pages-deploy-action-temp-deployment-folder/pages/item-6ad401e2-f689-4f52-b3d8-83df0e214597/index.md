Analisis & Rekomendasi VHost Multisite Drupal (drupal8.pesanweb.my.id)
======================================================================

\# Analisis & Rekomendasi Setup VHost Multisite Drupal (drupal8.pesanweb.my.id)

Temuan saat ini
---------------

### Home.pesanweb.my.id (192.168.2.40) - stack drupal811

*   Compose aktif: \`/data/compose/34/\` (drupal811). Nginx di container \`drupal\_web\_server\` listen di:

*   Port 80 (host 8086) -> vhost default \`server\_name localhost\`, root \`/var/www/html/drupal11/web\`, fastcgi ke \`drupal\_fpm:9000\` (Drupal 11)

*   Port 81 (host 8087) -> vhost default \`server\_name localhost\`, root \`/var/www/html2/drupal8/web\`, fastcgi ke \`drupal\_fpm7:9000\` (Drupal 8 multisite)

*   Seleksi multisite Drupal 8 dilakukan berdasarkan Host header (bukan server\_name spesifik per domain). Kedua vhost menggunakan \`server\_name localhost\` sebagai catch-all.

*   Struktur sites Drupal 8 (\`/data/compose/34/drupal8-src/web/sites/\`):

*   \`sites.php\` sudah berisi alias:

    $sites['belajarweb.pesanweb.my.id'] = 'belajarweb.pesanweb.my.id';$sites['web.pesanweb.my.id'] = 'web.pesanweb.my.id';// Nama-DB sebagai alias (non-breaking)$sites['drupal8.pesanweb.my.id']     = 'drupal8';$sites['web-legacy.pesanweb.my.id']  = 'web';

*   Direktori yang ada: \`belajarweb/\`, \`belajarweb.pesanweb.my.id/\`, \`drupal8/\`, \`web/\`, \`web.pesanweb.my.id/\`, \`default/\`

*   Site dir \`drupal8/\` memiliki \`settings.php\` yang mengarah ke DB \`drupal8\` (host \`drupal\_db\`, user \`drupal8user\`).

*   Pengujian lokal di host:

*   \`curl -H 'Host: drupal8.pesanweb.my.id' http://127.0.0.1:8087/\` -> HTTP 200, title "Selamat datang | Belajar Membuat Website" (sama dengan \`web.pesanweb.my.id\`)

*   Artinya secara internal di home sudah bisa melayani \`drupal8.pesanweb.my.id\` via Host header ke port 8087 dengan mapping alias ke direktori \`drupal8/\` + DB \`drupal8\`.

### Database (home)

*   DB di container \`drupal\_database\`: \`drupal8\` (246 tabel, ~236 node), \`belajarweb\` (246 tabel), \`web\` (246 tabel).

*   \`drupal8.pesanweb.my.id\` saat ini dipetakan ke direktori \`drupal8/\` yang konek ke DB \`drupal8\` - ini konsisten.

### Routing via Nginx Proxy Manager (NPM) di 192.168.2.57

Dari NPM DB (\`npm\_db\` di stack drupal-nfs):

| Domain | Forward | Port | Enabled |

|---|---|---|---|

| belajarweb.pesanweb.my.id | 192.168.2.57 | 8087 | yes |

| tty.drupal.lokal | 192.168.2.52 | 2222 | yes |

| curhatmu.my.id | 192.168.2.52 | 81 | yes |

\*\*Penting:\*\* \`web.pesanweb.my.id\` TIDAK ada di NPM - domain ini dilayani langsung oleh home.pesanweb.my.id (8087). \`drupal8.pesanweb.my.id\` juga belum terlihat di mapping NPM saat ini; kalau DNS/domain mengarah ke NPM atau belum dipetakan, bisa jadi belum bisa diakses dari luar meski internal home sudah bisa.

### ok.curhatmu.my.id (jelas dari mana munculnya)

*   NPM mem-forward \`curhatmu.my.id\` ke \`192.168.2.52:81\` (bukan ke home dan bukan ke 192.168.2.57)

*   Saat diuji dari home dengan Host \`ok.curhatmu.my.id\` ke \`127.0.0.1:8087\` mengembalikan konten dengan title "Welcome to Belajar PHP Database | Belajar PHP Database" dan \`contentTypeCounts\` kosong (semua 0). Ini bukan site yang sama dengan \`web/belajarweb\` di home - kemungkinan ada vhost lain / site berbeda di stack home atau behavior fallback.

*   Tidak ditemukan referensi \`ok.curhatmu.my.id\` di \`/data/compose/34/\`. Domain ini kemungkinan dilayani oleh host berbeda (192.168.2.52) yang didelegasikan lewat NPM.

Mengapa drupal8.pesanweb.my.id "belum bisa dipanggil"
-----------------------------------------------------

Ada beberapa kemungkinan:

1\. \*\*Belum dipetakan di Nginx Proxy Manager\*\* - Jika domain ini diarahkan melalui NPM (reverse proxy), perlu ditambahkan proxy host baru di NPM yang forward ke \`http://:8087\` (atau sesuai target). Tanpa ini, request dari luar tidak sampai ke home:8087.

2\. \*\*DNS belum mengarah\*\* - Pastikan DNS/domain \`drupal8.pesanweb.my.id\` resolve ke IP yang benar (bisa ke home via tunnel/cloudflared atau langsung).

3\. \*\*Konfigurasi nginx di home sudah cukup untuk Host header, tapi edge proxy belum dikonfigurasi\*\* - Nginx di home sudah catch-all di port 81, jadi cukup tambah mapping di NPM (atau pastikan routing langsung ke home:8087).

4\. \*\*Perlu memastikan sites.php + direktori konsisten\*\* - Yang sudah Anda lakukan (alias + dir drupal8) sudah benar. Yang perlu dicek adalah edge proxy.

Langkah-langkah yang disarankan
-------------------------------

### A. Verifikasi internal (sudah OK)

    # Dari homecurl -i -H 'Host: drupal8.pesanweb.my.id' http://127.0.0.1:8087/ | head -20

Hasil saat ini: HTTP/1.1 200 OK.

### B. Tambahkan mapping di Nginx Proxy Manager

Tambahkan Proxy Host baru:

*   Domain Names: \`drupal8.pesanweb.my.id\`

*   Scheme: http

*   Forward Hostname/IP: IP home.pesanweb.my.id atau 192.168.2.40 (sesuai jaringan internal)

*   Forward Port: 8087

*   Cache Assets: sesuai kebutuhan

*   Block Common Exploits: aktif

*   Websockets Support: bisa aktif jika ada

*   SSL: bisa request Let's Encrypt via NPM

Setelah ditambahkan, test dari luar/klien: \`curl -H 'Host: drupal8.pesanweb.my.id' https://drupal8.pesanweb.my.id/\` atau akses via browser.

### C. Alternatif: jika ingin pakai nama domain tanpa lewat NPM yang ada

Tergantung arsitektur routing saat ini. Karena \`web.pesanweb.my.id\` tidak ada di NPM dan bisa diakses (dilayani home), kemungkinan ada routing langsung. Namun untuk konsistensi dan SSL via NPM, lebih baik tambahkan ke NPM.

### D. Checklist konfigurasi multisite Drupal yang benar

Untuk setiap domain baru di multisite D8:

1\. \*\*sites.php\*\* - tambahkan \`$sites\['domain.tld'\] = 'nama-folder-db-atau-dir';\` (sudah ada untuk drupal8 -> drupal8) ✓

2\. \*\*Direktori sites/\*\* - pastikan ada direktori untuk mapping tsb. Ada \`sites/drupal8/\` ✓

3\. \*\*settings.php\*\* di direktori tersebut - konek ke DB yang benar. \`sites/drupal8/settings.php\` sudah mengarah ke DB \`drupal8\` ✓

4\. \*\*Files dir\*\* - \`sites/drupal8/files/\` perlu ada dan writable. Cek: \`ls -la /data/compose/34/drupal8-src/web/sites/drupal8/files/\`

5\. \*\*Cache clear\*\* - setelah perubahan: \`drush -r /var/www/html2/drupal8/web -l drupal8.pesanweb.my.id cr\` (atau via container)

6\. \*\*Edge routing\*\* - nginx vhost/NPM mengarah ke server+port yang benar ✓ (internal OK)

Rekomendasi spesifik untuk kasus ini
------------------------------------

1\. \*\*Tambahkan proxy host di NPM\*\* untuk \`drupal8.pesanweb.my.id\` mengarah ke \`192.168.2.40:8087\` (atau IP internal home). Ini yang paling mungkin kurang.

2\. \*\*Verifikasi SSL/DNS\*\* - pastikan record DNS mengarah ke NPM atau sesuai setup saat ini.

3\. \*\*Jika ingin menghindari duplikasi konten\*\* - karena \`drupal8\` dan \`web.pesanweb.my.id\` sama-sama ke DB \`drupal8\` dan konten sama, ini sudah benar sebagai alias.

4\. \*\*Untuk \`ok.curhatmu.my.id\`\*\* - domain ini dikelola terpisah (forward ke 192.168.2.52:81). Tidak ada kaitan langsung dengan stack drupal8 di home; muncul karena ada service lain di 192.168.2.52 yang di-proxy oleh NPM.

Langkah praktis (bisa langsung dicoba)
--------------------------------------

    # 1) Test internal dari homessh home.pesanweb.my.id "curl -s -o /dev/null -w '%{http_code}' -H 'Host: drupal8.pesanweb.my.id' http://127.0.0.1:8087/"# 2) Test dari luar setelah ditambahkan di NPMcurl -I https://drupal8.pesanweb.my.id/# 3) Clear cache jika perlu setelah perubahanssh home.pesanweb.my.id "docker exec d8_fpm drush -r /var/www/html2/drupal8/web -l drupal8.pesanweb.my.id cr"