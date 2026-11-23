Penjelasan de-link + audit setia-tautan (rencana perbaikan sesuai sumber)
-------------------------------------------------------------------------

### 1\. Apa itu "404 pasti → de-link" dan ada di tautan apa

Kemarin saya menghapus 5 tautan yang mati (404) dari body, karena tampak buntu. Lokasinya (5 lesson):

Lesson

Tautan yang dihapus

Fakta dari sumber

vba — [Range](http://web.pesanweb.my.id/lesson/ranges) (nid 464)

twitter.com/SkylarkHolly**%E2%80%8B** — URL berisi karakter _lebar-nol_ (karakter tak terlihat yang ikut menempel saat URL disalin-tempel), bikin Twitter 404

**Sumber juga punya URL yang sama rusaknya** — tapi URL bersih tanpa karakter itu **hidup (200)**

vba — [Profesormu](http://web.pesanweb.my.id/lesson/profesormu) (nid 453)

maximumfun.org/shows/nobody-listens…

Sumber punya tautan yang sama (404 juga di sumber)

webapps — [Tentang saya](http://webapps.pesanweb.my.id/lesson/tentang-saya) (nid 340)

maximumfun.org (sama)

Sumber punya tautan yang sama

webapps — [M:N](http://webapps.pesanweb.my.id/lesson/mn) (nid 548)

en.wikipedia.org/wiki/**Numismatis** — ejaan salah

Sumber memakai **Numismatist** (ejaan benar) — dan hidup (200)

web — [Berkomunikasi](http://web.pesanweb.my.id/lesson/berkomunikasi) (nid 67)

support.gather.town (tidak jadi dihapus — ternyata teks dalam contoh kode)

Sumber menampilkannya sebagai tautan sungguhan

**Koreksi kebijakan diterima**: tautan luar (halaman) tidak masalah asal bukan gambar. Maka rencananya bukan menghapus, melainkan **memulihkan sesuai sumber dengan URL yang diperbaiki**: twitter tanpa karakter lebar-nol, wikipedia ejaan Numismatist, maximumfun tetap sesuai sumber, gather sebagai tautan sungguhan.

### 2\. Audit setia-tautan: contoh proyek-akhir dan temuan penuh

Contoh yang Anda berikan benar: body [proyek-akhir](http://web.pesanweb.my.id/exercise/proyek-akhir) kehilangan tautan dari sumber `project-final-project` — di sumber ada tautan bootswatch, blog shopify, creativecommons, dan 3 tautan ke situs contoh pupperware (yang kini punya mirror lokal `/examples/pupperware/final/`).

Audit penuh (halaman sumber di-fetch ulang, body dibandingkan, 348 lesson):

Situs

Lesson bermasalah

vba

23

python

68

web

81

webapps

30

**Total**

**202 lesson, 269 tautan hilang**

#### Rincian kategori

Kategori

Jumlah

Sifat

Footer creativecommons (boilerplate akhir body) hilang

141

mekanis — tambah paragraf sama ke semua

Tautan webappexamples → kini `/phpexamples/` lokal (paragraf "Coba URL ini…" hilang)

56

paragraf hilang — sisip sesuai sumber

Tautan luar lain (shopify, bootswatch, unity, dsb.)

50

paragraf hilang — sisip sesuai sumber (kebijakan Anda: halaman luar boleh)

Tautan ke lesson sumber yang _alias EN-nya belum terdaftar_ (set-excel, when-you-get-stuck, submitting-exercises, generalize, loops-vba-vs-javascript, taste-test-stats, filtering-invalid-data, receivables, writing-first-program, structure-diagrams, keep-asking-numbers, links-and-urls)

13

daftarkan alias EN di node yang sudah ada

Tautan ke lesson sumber yang alias lokalnya sudah ada

9

sisip anchor

webexamples → /examples

0

sudah bersih (M2/F3)

Catatan: pemetaan saat ini belum mencakup semua node `exercise` — proyek-akhir sendiri di luar sampel; audit eksekusi akan memperluas peta ke semua exercise sehingga jumlahnya sedikit lebih besar dari 269.

### Rencana (belum dieksekusi)

*   **H1 — Pulihkan 4-5 tautan de-link sesuai sumber**: twitter bersih (200), maximumfun ×2 sesuai sumber, wikipedia Numismatist (200), gather sebagai tautan sungguhan di nid 67.
*   **H2 — Sisip paragraf tautan yang hilang** (A+C+F ≈ 115 tautan di ~120 lesson): paragraf sumber disalin dengan URL diterjemahkan ke lokal (/phpexamples/, /examples/) dan alias lokal; kalimat pengantar sumber dipertahankan/terjemah ringkas. Contoh proyek-akhir: 3 tautan pupperware → /examples/pupperware/final/.
*   **M1 — Daftarkan 13 alias EN** yang hilang (set-excel, dst.) supaya tautan sumber punya padanan lokal.
*   **M2 — Tambah footer creativecommons** ke ~141 body (boilerplate sumber).
*   **M3 — Perluas peta ke semua exercise** lalu verifikasi ulang sampai 0 tautan hilang.
*   **R — TRUNCATE cache (termasuk cache\_render) + verifikasi render + publikasi laporan.**

Semua perubahan body reversibel (backup per node). Kebijakan yang berlaku: tautan halaman luar **boleh** sesuai sumber; yang dimigrasi hanya gambar/media/lampiran.