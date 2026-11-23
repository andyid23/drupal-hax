Tombol "More..." bergeser keluar kolom di webapps
-------------------------------------------------

**Laporan:** tombol _More..._ di `http://webapps.pesanweb.my.id/lesson/classes-and-ids` tergeser ke kanan.

**Akar masalah:** bukan CSS. Tombol itu adalah widget pause Skilling (`.skilling-pause-container.btn-group`, 11 buah di halaman itu) dan tidak ada rule CSS posisi untuk `.skilling-pause-container` sama sekali (markup widget identik dengan sumber webapps.skilling.us). Penyebabnya **HTML tersimpan node MCQ yang tidak seimbang**: tiap field membawa satu `</div>` ekstra.

### Bukti

*   Region render `field--name-body` sampai pause pertama: target **25 buka / 29 tutup, sumber 25/24 → kelebihan 5 `</div>`.**
***   DB nid 381 (`mcq_css_p_tags`): `field_question` 1 buka/2 tutup, keempat `field_response` 1/2. Nid 392: keempat respons 1/2.
*   Efeknya browser menutup `field--name-body` dan `node__content` lebih awal, sehingga seluruh 11 widget pause jatuh sebagai anak langsung `article` — di luar kolom konten. HTTP 200, nol parser-error, semua count widget benar: hanya struktur DOM yang mendeteksi ini.
*   Node MCQ nid 381 juga membawa img `skilling-mcq-throbber` sampah (URL webapps.skilling.us) yang tersalin dari render sumber.**

**

### Perbaikan

*   Strip `</div>` ekstra dari `field_question` + semua `field_response` node MCQ (nid 381 dan 392), hapus img throbber sampah — simpan dalam render context.
*   `drush cr`, cek ulang alias + dedupe global (0 duplikat), HTTP 200.

### Verifikasi hasil

*   Region body→pause: 25/29 → **25/24, identik dengan sumber**.
*   Ke-11 widget pause kembali di dalam kolom: `row > col > region > block > article > node__content > field--name-body`, sama seperti sumber.
*   Halaman pemakai nid 392 (`/lesson/gaya-rusak`, sumber `broken-styles`): balance body 59/60 = 59/60 sumber.

### Pelajaran untuk clone wave berikutnya

*   Gate baru: bandingkan keseimbangan `<div>` buka/tutup pada tiap field HTML tersimpan (MCQ question/respons) dan pada region render body→pause pertama; harus sama dengan sumber.
*   Gejala pergeseran tombol widget jangan dicari di CSS dulu — cek dulu apakah widget masih di dalam `field--name-body` di DOM hasil render.

**