Audit principles webapps: 3 hilang, 4 body stub, dan perbaikan gambar Kity
--------------------------------------------------------------------------

Diagnosis dari data nyata (fetch sumber + tunnel + DB), bukan asumsi.

### 1\. Gambar Kity di happy.html

*   `kucing-moya.webp` di `examples/basic-sites/images/` ternyata **bukan gambar**: 873 byte berisi markup HTML (`<html><body><scr...`) — makanya tidak bisa dibuka.
*   File webp asli **38KB (1478×1823)** ada di `sites/webapps.pesanweb.my.id/files/gambar/`. Dikonversi ke `kucing-moya.jpg` (800×986, 83KB), di-deploy, dan `happy.html` + `mooya.html` di-update ke jpg. Verifikasi: jpg 200 image/jpeg, kedua img tag benar.

### 2\. Principle jangan-percai-data-pengguna vs sumber

*   Body node 483 semula **stub 44 char** ("Validasi dan bersihkan data pengguna.") vs esai penuh di sumber (validasi + hacking + 4 code container contoh SQL injection + XKCD). Bukan "sama dengan sumber".
*   Mekanisme: widget principle di lesson memakai `body->view('teaser')` (PrincipleTag.php). Jadi esai penuh disimpan di `body_value`, dan satu kalimat ringkas diset ke `body_summary` — widget lesson tetap ringkas, halaman principle menampilkan esai penuh.
*   Verifikasi: halaman principle render esai (4 container, 7× wombat, XKCD 1×); lesson `pustaka-validasi` widget tetap menampilkan ringkas saja (essay 0×); alias node 483 tunggal, 0 duplikat.
*   Deviasi dicatat: 2 gambar sumber (dog-original.jpg, exploits\_of\_a\_mom.png) 404 di kedua sisi — dihilangkan, tidak dikirim broken img. Link pattern dipetakan ke alias target yang hidup (`validasi-halaman-sama`, `validasi-multifield`).

### 3\. Audit daftar /principles

*   Sumber: **8** principle. Target: **5**. Mapping yang ada: dont-trust-user-data↔jangan-percai-data-pengguna (483), reuse↔gunakan-kembali (482), get-requirements-right↔pahami-kebutuhan-dengan-benar (532), reduce-cognitive-complexity↔kurangi-kompleksitas-kognitif (533), confirm-destructive-actions↔konfirmasi-aksi-destruktif (534).
*   **3 hilang**: plan-then-zoom, never-store-plain-text-passwords, security-obscurity-doesnt-work.
*   Body stub: 482 (85 char), 532 (101), 533 (126) — semuanya jauh lebih pendek dari esai sumbernya. 534 lebih panjang (1112) tapi belum dibandingkan.

### Pelajaran

*   Widget principle memakai teaser view: simpan esai di body\_value + ringkas di body\_summary — satu node untuk dua halaman.
*   Klaim "identik" harus dari diff inventory nyata, bukan dari ukuran file yang kebetulan sama.
*   File gambar harus divalidasi magic bytes, bukan status 200.