Clone lesson when-ranges-have-blank-data ke vba.pesanweb.my.id
--------------------------------------------------------------

Tanggal: 14 November 2026. Sumber: `https://vba.skilling.us/lesson/when-ranges-have-blank-data` → target node **465**, alias `/lesson/when-ranges-have-blank-data`, judul _Ketika Range punya data kosong_, bab _Analisis Data_ (pid 452, weight 2, antara _Range_ dan _Processing a Range_ yang belum di-clone).

### Inventaris vs render

*   Lesson kecil tanpa widget: 0 h3 / 3 pause / 5 blok kode / 1 gambar — render sama persis.
*   Tidak ada MCQ/FIB/pattern/exercise/karakter di sumber, jadi tidak ada yang dibuat di target.
*   Komentar kode dan baris pseudocode diterjemahkan; string literal VBA ("Are goattos good?", "Last data row:") tetap Inggris.
*   HTTP 200 (cek dua kali); `errors=0`; tidak ada tag mentah bocor; gambar blank-cells.png probe 200.
*   Book nav benar: prev = `/lesson/ranges` (Range), Up = `/lesson/data-analysis` (Analisis Data), next = Tips (chapter berikutnya di target; di sumber next-nya processing-range yang belum di-clone).
*   Baris book terverifikasi dari awal: `depth=3, p1=330, p2=452, p3=465` — pelajaran dari nid 464 (pid stale) langsung diterapkan: cek chapter nid aktif dulu (dari judul chapter), delete row lama, insert, verifikasi p1..p3.

### Catatan

*   Outline + pin alias + dedupe dilakukan dalam SATU script idempoten (create skip jika alias ada, delete baris book lama sebelum insert) — cukup sekali jalan tanpa deadlock.
*   Runbook `drupal-lesson-authoring` diperbarui dengan pola create+outline+pin satu script idempoten.