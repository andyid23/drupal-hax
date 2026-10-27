Pertanyaan yang Dijawab
-----------------------

Dari inventaris DB: di `home.pesanweb.my.id` ada database untuk `web.pesanweb.my.id` dan `belajarweb.pesanweb.my.id`. Apa bedanya, bisakah multi-host dilayani lewat Nginx Proxy Manager (NPM), dan apa rekomendasi multisite-nya.

1\. Isi Database (verifikasi langsung, bukan dari catatan lama)
---------------------------------------------------------------

*   **home/drupal8** — 246 tabel, 236 node, 12 user. **LIVE** untuk web.pesanweb.my.id.
*   **home/belajarweb** — 245 tabel, 117 node, 12 user. Konfigurasi multisite ada, tetapi **tidak dilayani**; ini salinan lama.
*   **home/web** — 244 tabel, 47 node. Legacy, tidak ada settings/vhost aktif.
*   **home/appdb** — 240 tabel, 1 node. Orphan.
*   **2.57/belajarweb** — 246 tabel, 236 node. **LIVE** untuk belajarweb.pesanweb.my.id (lewat NPM).
*   **2.57/drupal8** — 244 tabel, 47 node. Orphan, tidak direferensikan settings aktif.
*   **2.57/drupal11** — 74 tabel, 0 node. Vhost internal saja, settings 0 byte.
*   **2.57/drupal8db** — 22 tabel, bukan skema Drupal.

2\. Temuan Kunci: Kedua Situs Isinya Identik
--------------------------------------------

Checksum judul seluruh node di kedua DB live sama persis:

    md5(group_concat(title order by nid)) = 94dd346050c38bbd6b6f6897465ff142
    node = 236, max(nid) = 246  (home/drupal8 dan 2.57/belajarweb)

Artinya `belajarweb` saat ini adalah **mirror** dari `web`, bukan situs dengan konten berbeda. Perbedaan yang nyata hanya: domain di `settings.php`, direktori `sites/<domain>/files`, dan aggregate css/js yang digenerate per instalasi.

3\. Beda Tiap Database
----------------------

*   **Dua DB live** (`drupal8` di home, `belajarweb` di 2.57) memuat konten yang sama tetapi hidup di dua instalasi Drupal terpisah, dengan user DB dan host DB masing-masing.
*   **DB legacy/orphan** (`web`, `appdb`, `drupal8` di 2.57, `drupal8db`) tidak dipakai settings/vhost mana pun — kandidat arsip, bukan sumber kebenaran.
*   **DB belajarweb di home** adalah salinan lebih tua (117 node). Jangan dipakai untuk migrasi tanpa membandingkan dulu.

4\. Multi-host lewat NPM
------------------------

**Bisa.** NPM hanya reverse proxy berbasis Host header, tidak peduli target ada di mesin mana. Saat ini NPM (di 192.168.2.57) baru memetakan:

    belajarweb.pesanweb.my.id -> 192.168.2.57:8087
    tty.drupal.lokal          -> 192.168.2.52:2222
    curhatmu.my.id            -> 192.168.2.52:81

`web.pesanweb.my.id` belum lewat NPM — dilayani langsung home port 8087. Menambahkannya cukup satu entry baru ke `192.168.2.40:8087`.

Yang harus diperhatikan:

*   TLS per domain di NPM (Let's Encrypt), dan DNS mengarah ke edge NPM.
*   Nginx di kedua host memakai vhost catch-all berbasis port; pemilihan domain terjadi di Drupal multisite lewat Host header. Jadi uji dengan `curl -H 'Host: ...'`, jangan simpulkan dari curl biasa.
*   NPM DB (`proxy_host`) adalah sumber kebenaran routing, bukan hasil curl ke satu port.
*   Bila NPM mem-proxy ke host yang sama untuk dua domain, keduanya tetap butuh direktori `sites/<domain>` masing-masing.

5\. Rekomendasi Multisite
-------------------------

Pilih berdasarkan niat: apakah belajarweb akan _selalu_ sama dengan web, atau akan bercabang?

*   **Jika tetap mirror** — paling murah: satu instalasi, satu DB, dua domain. Home sudah bisa melayani keduanya sekarang (tes Host header: keduanya HTTP 200). Matikan sync, arahkan kedua domain ke satu host + satu DB, dan selesai. Tidak ada drift, tidak ada rsync.
*   **Jika akan bercabang** — pertahankan dua instalasi, tetapi standarkan: (a) semua domain masuk NPM, (b) satu pipeline deploy, (c) sinkron file satu arah hanya selama konten masih sama, (d) hentikan sync begitu konten mulai berbeda supaya tidak saling menimpa.
*   **Yang tidak disarankan** — dua instalasi dengan dua DB yang dipakai bergantian tanpa aturan sumber kebenaran. Itu kondisi sekarang dan sumber masalah referensi file yang baru diperbaiki.

6\. Langkah Menuju Satu Edge (NPM)
----------------------------------

1.  Pastikan DNS `web.pesanweb.my.id` mengarah ke IP publik edge (saat ini Cloudflare/ tunnel ke home).
2.  Di NPM: _Hosts → Proxy Hosts → Add_, domain `web.pesanweb.my.id`, forward `192.168.2.40`:`8087`, aktifkan Websockets dan Block Common Exploits, lalu request sertifikat SSL.
3.  Verifikasi dari dalam LAN: `curl -s -H 'Host: web.pesanweb.my.id' http://192.168.2.40:8087 | grep -o '<title>[^<]*'` — harus judul situs web, bukan halaman default.
4.  Verifikasi dari luar: `curl -sI https://web.pesanweb.my.id` → 200, dan cek sertifikat.
5.  Setelah yakin, tutup akses langsung ke port 8087 dari internet bila ada, supaya semua trafik lewat edge.
6.  Untuk konsolidasi ke satu host: import dump DB belajarweb dari 2.57 ke DB baru di home, arahkan `sites/belajarweb.pesanweb.my.id/settings.php` ke DB itu, lalu `drush cr` dan uji kedua domain.

7\. Verifikasi yang Sudah Dilakukan
-----------------------------------

*   Checksum judul node kedua DB live identik (lihat bagian 2).
*   Home melayani kedua Host header dengan HTTP 200 dan judul situs yang benar.
*   NPM `proxy_host` dibaca langsung dari DB: hanya tiga entry, `web` belum tercantum.
*   Direktori `sites/web.pesanweb.my.id` dan `sites/belajarweb.pesanweb.my.id` keduanya ada di home.