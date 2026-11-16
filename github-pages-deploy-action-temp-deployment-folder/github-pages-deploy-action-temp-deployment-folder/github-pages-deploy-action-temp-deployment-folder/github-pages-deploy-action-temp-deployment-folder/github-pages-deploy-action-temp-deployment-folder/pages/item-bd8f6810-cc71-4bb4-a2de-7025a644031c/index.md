Perbaikan lesson "Templat halamannya" (node 410, webapps.pesanweb.my.id)
------------------------------------------------------------------------

Laporan perbaikan halaman lesson hasil kloning dari `https://webapps.skilling.us/lesson/page-template`. Target yang dilaporkan rusak: `/lesson/templat-halaman-error` (satu node 410, empat alias menunjuk ke node yang sama: `templat-halaman-error`, `templat-halamannya`, `page-template`, `templat-halaman`).

Kondisi sebelum perbaikan
-------------------------

Halaman menjawab HTTP 200 tetapi body setengah jadi (body tersimpan hanya 22.884 karakter vs sumber 40.176). Temuan inventori:

*   Semua 10 heading `<h3>` ter-render sebagai `<h4>` — marker `skilling-parse-unit-decrease-heading-offset` tidak ada di body.
*   Seluruh 20 blok kode hilang wrapper `skilling-code-container` (list kode dirender sebagai bullet list biasa; 24 container seharusnya, termasuk 2 di dalam tabel dan 1 di dalam dialog karakter).
*   Tabel perbandingan GOATS (render vs kode) hilang total.
*   Widget `mcq.` tidak ter-render (tag ada di body tetapi dibungkus `<p>` dan join-nya kacau).
*   4 widget `reflect.` hilang seluruhnya.
*   1 iframe YouTube hilang; 12 dari 22 gambar body hilang.
*   Judul node salah: "Templat halaman Error".

Apa yang dilakukan
------------------

*   Rebuild body dari sumber dengan token walk per posisi dokumen (141 token): 16 `pause.`, 1 `toc.`, 8 blok `character.`, 4 `reflect.` (internal\_name tanpa prefix `25-`, shape mengikuti node 509 yang proven di situs ini), 24 code container diserialisasi verbatim (entitas `</li>` dipertahankan), tabel diterjemahkan dengan code container di-stash, iframe YouTube dipancarkan sebagai blok sendiri, 12 gambar konten di-absolutisasi ke `https://webapps.skilling.us/.../page-structure/*.png`.
*   Terjemahan EN→ID via endpoint `clients5.google.com` dengan placeholder `⟪n⟫` untuk tag inline; cache MT disimpan di scratch.
*   Marker `decrease-heading-offset` ditempatkan tepat sebelum `<h3>` pertama (bukan di awal body — blok pertama adalah `mcq.`; marker di awal body mematikan tag pertama).
*   Node 410 di-update (bukan dibuat ulang): body format `skilling`, title diganti "Templat halamannya", alias `/lesson/templat-halaman-error` dipin di field `path` dengan `pathauto=0`, duplikat baris alias di-dedupe, `cache:rebuild`.
*   MCQ `mcq_get_file_on_web` memakai node 349 yang sudah ada (pertanyaan, respons, dan penjelasan sudah berbahasa Indonesia); pattern `pattern_basic_page_template` memakai node 363.

Jebakan yang ditemukan (untuk runbook)
--------------------------------------

*   `re.split()` dengan capture group menyisipkan nilai group ('0') sebagai potongan terpisah — gunakan mapping berbasis alternasi text/inline dan buang indeks ganjil.
*   Regex terjemahan `[^<>]+` ikut mencocokkan nama tag dan atribut — hanya node teks yang boleh dikirim ke MT.
*   Node teks whitespace-only (tail antar token) membuat adjacency dan memicu mismatch mapping — buang dari acc, biarkan utuh di tree.
*   Endpoint MT kadang memangkas spasi di tepi potongan — simpan dan tempelkan kembali lead/trail whitespace.
*   Handler dialog karakter harus membawa seluruh anak content div (ada code container di dalam dialog), bukan hanya `<p>`.
*   iframe YouTube di sumber berada di dalam `<p>` — handler paragraf harus mengecek iframe.

Verifikasi (setelah cache rebuild)
----------------------------------

*   4 alias semua HTTP 200: `templat-halaman-error`, `templat-halamannya`, `page-template`, `templat-halaman`.
*   Render: h3=10, h4=0, pause=16, karakter=8, mcq=1, pattern=1, reflect=4, toc=1, iframe=1, tabel=1, code container=24, img konten 12/12 paritas dengan sumber.
*   Error render: `custom-tag-error`\=0, `parser-error`\=0, `skilling-parsing-error`\=0; tidak ada markup mentah yang bocor.
*   Probe 19 URL gambar eksternal (browser UA): semua 200, termasuk 10 PNG konten, 8 foto karakter, dan throbber MCQ.

Catatan akses
-------------

`webapps.pesanweb.my.id` masih NXDOMAIN di DNS publik (sama seperti `vba` dan `python`). Halaman sehat di server; akses browser via tunnel SSH (`ssh -L 80:127.0.0.1:8087`) + entri hosts `127.0.0.1 webapps.pesanweb.my.id`, atau tambah public hostname di dashboard Cloudflare (service `http://localhost:8087`).