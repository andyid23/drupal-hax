R1: Probe 194 tautan eksternal — hasil
--------------------------------------

Eksekusi R1: semua tautan keluar (domain luar skilling.us/pesanweb) di-probe dua kali (curl + retry, lalu verifikasi ulang dari jaringan kedua untuk kasus meragukan). Tanggal 2026-11.

### Ringkasan

*   **Hidup: 155 tautan** — dibiarkan apa adanya (dokumentasi, bot-wall, hiburan).
*   **403/401 anti-bot: ~20 tautan** (unsplash, monster.com, moodle.org, allrecipes, w3.org, pngwing, godaddy, merriam-webster, stackoverflow, ditto, pureinfotech…) — situs _hidup_, hanya memblokir bot. Verifikasi ulang dengan header browser lengkap tetap 403 → **tidak di-de-link**.
*   **000 (tak terjangkau dari dua jaringan): 8 tautan** (redsome, tonianitahull, forums.reclaimhosting, namakamu/untukmu.html, portableapps ×2, linuxpip, simulator.seenow) — portableapps.com adalah situs besar yang jelas hidup, jadi 000 ≠ pasti mati (kemungkinan blokir IP datacenter) → **dibiarkan**, dicatat.
*   **500 paulapoundstone.com/podcast/ ×2** — server error persisten tapi situs induk hidup → dibiarkan.
*   **localhost ×2** (instruksi XAMPP siswa) — tidak relevan di-probe, dibiarkan.

### De-link dilakukan (404 pasti, 5 kemunculan)

URL mati

Kode

Node

Tindakan

twitter.com/SkylarkHolly%E2%80%8B (URL berisi karakter lebar-nol)

404

vba 464

anchor dibuang, teks tetap

maximumfun.org/shows/nobody-listens-to-paula-poundstone

404

vba 453, webapps 340

de-link

en.wikipedia.org/wiki/Numismatis (artikel dipindah)

404

webapps 548

de-link

support.gather.town/help/av-troubleshooting

404

web 67

ternyata _teks escaped_ (contoh kode, bukan tautan) — dibiarkan sebagai teks

Verifikasi pasca-de-link: 3 dari 4 URL mati kini 0 baris di DB; yang tersisa 1 (escaped text).

### Perbaikan bonus saat R1 (dari laporan "carousel tidak bisa")

*   **Akar masalah carousel1.html: bukan CDN** — M1 tidak pernah dijalankan; file dari andy1.rf.gd ternyata halaman anti-hotlink InfinityFree (882 byte, aes.js). Konten asli diambil dari webexamples.skilling.us (carousel + cards + forms + cryptid beserta 15 asetnya), timpa stub.
*   3 halaman andy1-only (basic-tags, mooya, kity2) diambil lewat browser nyata (lolos challenge InfinityFree) + asetnya (kucing-moya.webp, styles.css, moze.png) — semuanya 200 di web-contoh dan web.
*   **CDN stackpath.bootstrapcdn.com (ditutup Bootstrap 2024)** yang dipakai file course aktif (pupperware/final ×10, user-accounts/products, app books/nobody yang ditulis) diganti ke **jsdelivr — tetap CDN, tidak dilokalkan**; CDN jsdelivr/jquery yang hidup dibiarkan. Sisa stackpath aktif: 0.