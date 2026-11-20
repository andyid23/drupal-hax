Laporan: Audit web.pesanweb.my.id vs learnweb.skilling.us + perbaikan
---------------------------------------------------------------------

Audit penuh sumber `learnweb.skilling.us` ("Learn the web", 76 lesson, 11 top-level) vs target, inventaris DB, audit render 81 halaman, census gambar wrapper, dan eksekusi rencana perbaikan. Tanggal 2026-11.

### Temuan utama

*   Struktur sumber: Get ready (9 anak), Task sample, Your learning style, Start the final project (3), How the web works (8), Final project: skeleton, Basic hand-made sites (24), Final project: prototype, Professional sites (16 + cabang SEO depth-4: how-google-works, types-seo, keyword-research, writing-content-seo), Finish the final project, Tips (5).
*   Target: 10/11 bab terpetakan dengan urutan anak benar (termasuk SEO depth-4); `parser-error` = 0 di 81 halaman.
*   **Node yang belum ada: `domain-locked-verification`** — satu-satunya lesson sumber hilang.
*   Duplikat: 187 "Menggunakan gaya BS" (published, di luar book) vs 186; 212 "Baris Navigasi" (unpublished) vs 213.
*   **Node uji 31–36 ("Lesson 1/1.1/…") berada DI DALAM book utama (bid 30)** pada bobot 0/10 — tampil di navigasi pembaca.
*   Alias node 73 salah: `/buat-akun-hosting-go` tanpa prefix `/lesson/` → 404.
*   Bab 50: using-ai(89) dan about-me(66) tabrakan bobot w=3, urutan tertukar.
*   Konvensi gambar kursus ini: slot foto `student-image` di sumber → `selfie-holder` di target; screenshot/diagram memakai `figure-indent`.

### Perbaikan yang dieksekusi

*   **domain-locked-verification dibuat** (nid 247 "Domain terkunci untuk verifikasi", outline di Tips, bobot -14; nav terverifikasi: prev=domain-tambahan, next=penaksir-nilai; gambar suspended.png absolut).
*   **Alias 73 diperbaiki**: alias salah dihapus, `/lesson/lets-go` di-pin (200).
*   **Node uji 31–36 dikeluarkan dari book** (0 baris tersisa; node tetap unpublished).
*   **Duplikat 187 dan 212 di-unpublish** (kanonik 186, 213 tetap).
*   **Bab 50 di-re-space** urutan sumber (51:1 … 73:9), tabrakan w=3 hilang; alias di-pin ulang.
*   **Wrapper gambar**: 147 (student-image → selfie-holder, kittens.jpg), 104 (sub-kucing.jpg dibungkus), 124 (web-kucing.jpg dibungkus), 227 (3 gambar dibungkus figure-indent). Verifikasi render: semua gambar slot kini terbungkus kelas yang benar.
*   Dedupe `path_alias` global + pin alias manual utama.

### Verifikasi akhir

*   10 URL yang disentuh semuanya 200; `custom-tag-error`/`parser-error` = 0.
*   Book root kini: Bersiap, Contoh tugas, Gaya Belajar, Mulai proyek, Cara kerja web, Kerangka, Situs buatan tangan, Prototipe, Situs profesional, Selesaikan proyek, Kiat-kiat — sesuai sumber, tanpa node uji.

### Sisa (tidak dieksekusi / opsional)

*   design\_page junk 45–47 dengan book tree sendiri (bid 45) — perlu konfirmasi pemilik situs.
*   Audit perbandingan inventaris widget per-lesson penuh (render 0-error sudah lulus).
*   Mirror webexamples sudah selesai terpisah — lihat halaman "mirror-webexamples".