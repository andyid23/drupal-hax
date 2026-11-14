Clone lesson Guard pattern ke vba.pesanweb.my.id
------------------------------------------------

Sumber: [vba.skilling.us/lesson/guard-pattern](https://vba.skilling.us/lesson/guard-pattern) → target `/lesson/guard-pattern` (nid 414). Target dicek 404 sebelum dibuat (belum ada node).

### Inventaris sumber vs hasil render

Item

Sumber

Render

h3

2

2 (0 h2)

pause

4

4

karakter

3

3

code container

4

4

MCQ

0

0

FIB

0

0

pattern tag

1

1

reflect

1

1

parser error

—

0

### Karakter

*   Georgina neutral1 → `character_izza_masuk` (aturan Adela→Fina, Georgina→Izza).
*   Ethan smile2 dan yay1 **dipertahankan** karena node-nya ada di vba (nid 288, 297); dialog diterjemahkan.
*   Urutan widget render sama dengan urutan sumber (dicek per posisi, bukan hanya jumlah).

### Gambar selfie-holder

Gambar kambing memakai format yang sudah terbukti di node 335-337: `<p class="selfie-holder"><img ... /> ... </p>` — render persis `<p class="selfie-holder">` dengan alt/title diterjemahkan ("Tumpukan kambing"). Semua 4 URL gambar eksternal di-HEAD-probe: HTTP 200.

### Dependency baru: pattern\_guard (nid 410)

Node pattern `pattern_guard` belum ada di vba, dibuat lebih dulu (situation/needs/provides/summary/action dari halaman sumber, diterjemahkan; komentar kode jadi `' Lindungi dari tanpa kambing.`). Alias pathauto `/pattern/guard-pattern` mengembalikan 200.

### Book outline + tabrakan weight

Lesson masuk bab Keputusan (nid 392, bid 330). Sesi paralel sempat menambah node 409 (nested-ifs) dan 415 (multiway) sehingga tiga node bertabrakan di weight=2 — prev/next sempat salah arah. Diperbaiki sesuai urutan sumber (multiway → nested-ifs → guard-pattern): 415 w=2, 409 w=3, 414 w=4. Navigasi akhir: prev = If bersarang, Up = Keputusan, tanpa next (basic-validation-strings belum dikloning).

### Alias

Satu baris alias untuk nid 414: `/lesson/guard-pattern` (status=1, langcode=en). Alias manual yang terhapus oleh save tetangga dipulihkan: `/lesson/program-pertama`, `/lesson/dialogs`, `/lesson/nested-ifs`, `/lesson/if-bersarang`; duplikat `/lesson/dialog` didedup. Cek akhir: 36 URL lesson/pattern semua HTTP 200.

### Pelajaran untuk runbook

*   Jangan re-run script update PHP untuk "mengecek" — setiap save memicu pathauto lagi; setelah re-run, jalankan ulang alias-fix + cache:rebuild + curl ulang.
*   Query weight book _tepat sebelum_ set weight; sesi paralel bisa menambah node di antara query.
*   Loop curl per-URL harus jalan di host, bukan di dalam container (docker tidak tersedia di dalam).