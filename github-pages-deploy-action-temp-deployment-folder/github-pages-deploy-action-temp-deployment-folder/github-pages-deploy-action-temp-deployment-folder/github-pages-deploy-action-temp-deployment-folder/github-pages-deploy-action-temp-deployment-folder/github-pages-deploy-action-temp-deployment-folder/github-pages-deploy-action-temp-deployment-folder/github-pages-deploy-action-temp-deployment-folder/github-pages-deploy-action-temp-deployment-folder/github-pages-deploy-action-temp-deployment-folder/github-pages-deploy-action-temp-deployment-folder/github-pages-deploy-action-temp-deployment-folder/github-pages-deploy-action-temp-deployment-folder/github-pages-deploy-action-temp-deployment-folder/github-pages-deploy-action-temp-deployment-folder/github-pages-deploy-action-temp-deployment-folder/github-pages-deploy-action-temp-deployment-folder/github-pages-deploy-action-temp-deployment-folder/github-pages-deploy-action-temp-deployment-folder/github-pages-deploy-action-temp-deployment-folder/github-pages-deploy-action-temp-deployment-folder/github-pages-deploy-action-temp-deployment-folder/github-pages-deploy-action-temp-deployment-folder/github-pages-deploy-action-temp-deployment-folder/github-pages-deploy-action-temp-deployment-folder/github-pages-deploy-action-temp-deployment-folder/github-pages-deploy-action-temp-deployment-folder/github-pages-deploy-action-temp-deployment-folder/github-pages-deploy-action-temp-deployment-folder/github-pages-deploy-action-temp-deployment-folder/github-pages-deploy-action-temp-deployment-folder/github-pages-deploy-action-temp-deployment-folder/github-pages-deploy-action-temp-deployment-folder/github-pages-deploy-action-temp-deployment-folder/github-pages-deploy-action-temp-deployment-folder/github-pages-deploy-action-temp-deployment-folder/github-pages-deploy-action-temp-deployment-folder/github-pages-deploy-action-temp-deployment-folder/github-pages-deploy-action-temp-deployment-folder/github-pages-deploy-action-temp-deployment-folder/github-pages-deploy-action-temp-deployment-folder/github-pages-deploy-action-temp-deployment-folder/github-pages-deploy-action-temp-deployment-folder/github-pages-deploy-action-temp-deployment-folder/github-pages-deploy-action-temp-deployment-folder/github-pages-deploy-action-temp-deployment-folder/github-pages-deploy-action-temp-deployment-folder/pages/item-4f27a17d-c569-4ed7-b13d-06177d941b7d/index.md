Ringkasan Masalah
-----------------

Gambar dan aset di `belajarweb.pesanweb.my.id` tidak tampil karena halaman di database menunjuk ke direktori files situs lain. Contoh laporan: `https://web.pesanweb.my.id/sites/web.pesanweb.my.id/files/gambar/manja-vitolic-gKXKBY-C-Dk-unsplash.jpg` bisa dibuka, sedangkan URL setara di belajarweb gagal.

Hasil Diagnosis
---------------

*   File konten **tidak hilang**: direktori `gambar/` berisi 310 file di kedua host dengan ukuran identik. Selisih 876 file (66,8 MB) seluruhnya `css/`, `js/`, dan `php/` — aggregate yang digenerate per situs, bukan konten.
*   Akar masalah ada di **URL di database**: body node menyebut direktori site milik domain lain.
*   Setiap host hanya punya direktori site-nya sendiri, sehingga path silang menghasilkan 404.

### Sebaran referensi silang sebelum perbaikan

*   `belajarweb` (192.168.2.57, DB `belajarweb`): 69 node menunjuk `sites/web.pesanweb.my.id/files/...`.
*   `web` (home, DB `drupal8`): 13 node menunjuk `sites/belajarweb.pesanweb.my.id/files/...`.

Tindakan Perbaikan
------------------

1.  Backup database kedua situs sebelum perubahan, disimpan di direktori backup masing-masing host (`*-pre-refsync-*.sql.gz`).
2.  UPDATE seluruh kolom teks pada DB: referensi ke direktori site lain diganti ke direktori site sendiri (`REPLACE()` pada tabel `node__body`, revisi, paragraph, field, dan tabel teks lain).
3.  Rebuild cache kedua situs dengan drush agar body yang tersimpan di cache ikut diperbarui.
4.  Memasang sinkronisasi file konten satu arah web → belajarweb (harian 02:30) untuk unggahan baru.

Verifikasi
----------

*   DB: 0 baris `node__body` yang masih memuat referensi site lama di kedua host.
*   Halaman `belajarweb/node/225`: 17 URL aset diperiksa, semuanya HTTP 200 — termasuk `manja-vitolic-gKXKBY-C-Dk-unsplash.jpg`.
*   Sampel 8 halaman per situs: tidak ada lagi referensi ke direktori site yang salah.
*   Uji sync: file uji di `gambar/` web tersalin ke belajarweb dalam sekali jalan, lalu dibersihkan.

Catatan Operasional
-------------------

*   Skrip perbaikan referensi: `/root/fix-sites-refs.sh <db> <site-lama> <site-baru> <dir-backup>` di host home.
*   Skrip sync: `/root/sync-haxcms-files.sh` di host home; jadwal di `/etc/cron.d/haxcms-files-sync`; log di `/var/log/haxcms-files-sync.log`.
*   Sync bersifat aditif (tanpa `--delete`) agar unggahan yang dibuat langsung di belajarweb tidak terhapus.
*   Direktori `css/`, `js/`, `php/` sengaja dikecualikan karena aggregate tersebut harus digenerate per instalasi.