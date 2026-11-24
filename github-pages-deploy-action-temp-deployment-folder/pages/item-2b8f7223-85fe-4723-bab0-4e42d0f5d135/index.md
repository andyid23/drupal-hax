Perbaikan 5 isu: contoh PHP debugging-old-school, attachment kucing, refleksi broken-styles, gambar Kity
--------------------------------------------------------------------------------------------------------

Laporan pengguna mencakup lima hal di `webapps.pesanweb.my.id`. Diagnosis dari data nyata (curl via nginx + Host header, dump DB, banding sumber), bukan asumsi.

### 1\. bug2/3/4/6.php — baris debug hilang

*   Href di lesson **sudah benar** dan logika cost **sudah benar** (0.4). Yang hilang: blok debug `<pre>` yang di-echo sebelum doctype (ter-strip saat clone).
*   Sumber asli di `webappexamples.skilling.us` (bukan webapps.skilling.us — URL itu 404); WAF-nya 406 untuk curl tanpa UA, jadi golden direkonstruksi dari body lesson yang memang sudah memuat teksnya.
*   Restorasi per file sesuai golden: bug2 = After GET; bug3/4 = +cost; bug6 = +cost +Before if _false_ +After if _0.4_.
*   Verifikasi curl `?quonks=1&insured=Y`: keempat output kini cocok dengan golden. bug1/bug5 tidak tersentuh (tetap 200).

### 2\. Tautan exercise fakta-kucing-rusak "tidak bisa"

*   `dog.html` ternyata **halaman 404 Drupal yang tersimpan sebagai file** (35KB "Page not found") — status 200, jadi cek status-saja tidak mendeteksinya.
*   Di-rebuild dari sampel kode ter-escape di body lesson + analisis screenshot tujuan (`kucing_0.jpg`): halaman "Kucing Fakta Menarik", ol 5 fakta.
*   `styles.css` sudah benar: CSS sengaja-rusak (back-color, sansserif, font-weight: bald, orenge, italian, `ol l1`) — sesuai desain lesson, tidak dibetulkan.

### 3\. Refleksi broken-styles "tidak ada"

*   Ternyata **sudah ada** di HTML target: `286-reflect_refresh_bold_or_not`, id & posisi identik sumber (4 pause sebelum reflect di kedua halaman). Tidak ada perubahan.

### 4\. Ganti gambar Kity di happy.html

*   `img` diganti: rosie1.jpg → `kucing-moya.webp` (alt="Kity"), verifikasi 200 image/webp.

### Catatan kecil

*   `<link href="styles.css">` di halaman bug 404 lokal, dan file sumber tak bisa dikonfirmasi (WAF 406). Teks golden tidak menyebut styling — dibiarkan, jangan mengarang.

### Pelajaran

*   Cek status HTTP saja tidak cukup: attachment 200 bisa berisi halaman 404 tersimpan — selalu inspeksi isi.
*   Sebelum "menambah" widget yang dilaporkan hilang, bandingkan dulu id & posisi dengan sumber — bisa jadi sudah ada.
*   Restorasi file contoh: cocokkan echo debug dengan teks golden di body lesson, bukan tebak-tebakan.