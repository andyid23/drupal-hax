Ringkasan
---------

Lesson **Latihan: ekspresi string** (node 456, `/lesson/latihan-ekspresi-string`) di `python.pesanweb.my.id` diperbaiki menyeluruh dari sumber `https://python.skilling.us/lesson/practice-string-expressions`. Sebelum perbaikan halaman praktis kosong: body memicu _parser error_ dan hampir semua konten tidak muncul.

Kondisi sebelum perbaikan
-------------------------

*   Body hanya 6.261 byte dan terpotong: MCQ ke-5 (quarkbeast/len), karakter Georgina, dan reflect kedua tidak ada.
*   Dua tag `character.` ditulis tanpa `internal_name=` (memakai `character_adela` / `character_georgina` yang tidak ada di DB) sehingga parser berhenti dengan _Missing/invalid close tag? Tag: character_.
*   Judul section ditulis sebagai `&lt;h2&gt;` ter-escape.
*   MCQ ditulis inline (`* Salah` / `* Benar`) — format ini **tidak** menghasilkan widget MCQ di situs ini (render: 0 `skilling-insert-mcq`).
*   Jawaban MCQ inline yang ada juga salah: untuk soal Bonnie/Zero, jawaban yang ditandai "Benar" adalah `Bonnie and Zero sitting in a tree.`, padahal output sebenarnya `Bonnie and Zero , sitting in a tree.` (dijalankan dan diverifikasi dengan Python).

Inventaris sumber (python.skilling.us)
--------------------------------------

*   3 blok kode di body lesson + 5 blok kode di dalam MCQ
*   5 MCQ: `string_expression_1` … `string_expression_5`
*   6 penanda pause (tombol More...)
*   2 karakter: Adela (smile) dan Georgina (talking)
*   2 blok Reflect

Yang dilakukan
--------------

### 1\. Body node 456 ditulis ulang

*   Format sesuai parser Skilling: setiap baris logis dipisah `&lt;br /&gt;` + newline; tag dan baris option-nya **harus berdampingan** (tanpa baris kosong di antaranya). Baris kosong memicu error _MCQ tag needs internal\_name option._
*   `&lt;div class="skilling-parse-unit-decrease-heading-offset"&gt;&lt;/div&gt;` ditempel langsung di depan `&lt;h3&gt;` supaya heading tetap h3. Offset div kedua di awal body justru menurunkan heading menjadi h2.
*   Karakter dipetakan: Adela → Izza (`character_izza_senang`), Georgina → Fina (`character_fina_masuk`). Kedua node karakter sudah punya foto (fid 594 dan 376), URL gambar mengembalikan HTTP 200.
*   Naratif, dialog, I/O, catatan, dan pertanyaan reflect diterjemahkan ke Indonesia; kode dan output program tetap Inggris.

### 2\. MCQ

*   Sudah ada di situs dan dipakai ulang: node 492 `mcq_string_concat_quote`, node 493 `mcq_string_repeat_concat`.
*   Dibuat baru: node 956 `mcq_string_concat_order`, node 957 `mcq_string_variable_concat`, node 958 `mcq_string_len_repeat` (masing-masing 1 jawaban benar + penjelasan Indonesia).
*   Node 492 dikoreksi: jawaban benar sebelumnya salah (`Dwayne 'The Rock' Johnson`). Output sebenarnya `Dwayne'The Rock'Johnson` tanpa spasi; flag `field_correct` dan teks respons diperbaiki.
*   Pada MCQ anjing (Burt/Pax) opsi B dan C di sumber identik byte-per-byte; opsi C yang duplikat dibuang agar tidak ada dua jawaban sama.
*   Teks pertanyaan node 492 dan 493 diterjemahkan: "What's this output?" / "What does this output?" → "Apa output ini?".

Verifikasi (render halaman asli)
--------------------------------

*   `skilling-code-container`: 3 (sesuai body lesson)
*   `skilling-insert-mcq-question`: 5 (956, 492, 957, 493, 958) — 21 respons tampil
*   `skilling-insert-character-container`: 2 (Izza, Fina)
*   `skilling-pause-container`: 6
*   `skilling-reflect-container`: 2
*   `skilling-parsing-error`: 0, "Missing/invalid close tag": 0, "not found": 0
*   Heading section render sebagai `&lt;h3&gt;`

Temuan infrastruktur
--------------------

### 1\. Node lesson duplikat

Node **459** ("Latihan: ekspresi string") duplikat dari node 456: judul sama, `changed` sama, dan satu induk buku yang sama (book 372, parent 451, depth 3). Node 456 memegang alias kanonik `/lesson/latihan-ekspresi-string`; node 459 punya alias `/lesson/latihan-ekspresi-string-0`. Keduanya publish, jadi outline buku menampilkan dua entri sama. Perbaikan hanya diterapkan ke node 456 (URL yang diminta).

### 2\. DNS publik tiga hostname mati

Dari host `home.pesanweb.my.id`, query ke 1.1.1.1 dan 8.8.8.8:

*   `python.pesanweb.my.id` → NXDOMAIN
*   `vba.pesanweb.my.id` → NXDOMAIN
*   `webapps.pesanweb.my.id` → NXDOMAIN
*   `web.pesanweb.my.id`, `belajarweb.pesanweb.my.id`, `home.pesanweb.my.id` → resolve normal

Servis `cloudflared` aktif dan sudah meregistrasi koneksi tunnel, jadi kemungkinan besar record DNS untuk tiga hostname itu hilang/dihapus di dashboard Cloudflare. Konten lesson sudah benar di server; yang perlu ditambahkan adalah route DNS + public hostname tunnel untuk `python.pesanweb.my.id`.

Cara verifikasi render
----------------------

Port pemetaan di host berubah menjadi berbasis port (bukan server\_name): container `drupal_web_server` memetakan 8086→80 (Drupal 11) dan 8087→81 (Drupal 8). Verifikasi render D8 dilakukan dari dalam container web server:

    cat verify.sh | ssh -p 2222 root@home.pesanweb.my.id \
      "docker exec -i drupal_web_server sh -s"
    # isi verify.sh:
    # curl -s -H 'Host: python.pesanweb.my.id' http://127.0.0.1:81/lesson/&lt;slug&gt;

Catatan: skrip di `/tmp` host berjalan di host, bukan di container, sehingga `curl 127.0.0.1:81` dari host gagal (000). Pipe lewat `docker exec -i ... sh -s`.