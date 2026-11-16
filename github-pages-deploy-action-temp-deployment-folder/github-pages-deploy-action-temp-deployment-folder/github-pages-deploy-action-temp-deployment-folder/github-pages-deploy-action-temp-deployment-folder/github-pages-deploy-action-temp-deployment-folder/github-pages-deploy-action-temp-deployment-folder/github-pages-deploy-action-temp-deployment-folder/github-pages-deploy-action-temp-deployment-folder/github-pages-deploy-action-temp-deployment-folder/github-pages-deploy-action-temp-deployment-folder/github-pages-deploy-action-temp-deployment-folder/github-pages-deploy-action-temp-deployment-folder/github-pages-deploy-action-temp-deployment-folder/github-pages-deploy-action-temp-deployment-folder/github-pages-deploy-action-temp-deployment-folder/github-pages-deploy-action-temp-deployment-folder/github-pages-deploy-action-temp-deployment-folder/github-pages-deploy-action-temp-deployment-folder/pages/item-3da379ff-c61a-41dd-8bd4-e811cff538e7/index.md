Audit lesson program-fina: node karakter duplikat memutus render widget
-----------------------------------------------------------------------

Audit `/lesson/program-fina` di vba.pesanweb.my.id (klon sumber `https://vba.skilling.us/lesson/adela-programs`) menemukan 7 widget karakter yang hilang diam-diam: halaman HTTP 200, tanpa `parser-error`, tanpa `not found` — tetapi hanya 19 dari 26 kontainer karakter yang dirender.

### Akar masalah

String error yang terlewat oleh grep verifikasi: `Unexpected number of characters with internal_name character_fina_masuk found.` — tercetak 14 kali (7 blok × 2 per render). Dua node karakter memuat `field_internal_name` yang sama:

*   nid 77 "Fina masuk" (asli) + nid 496 "Fina" (stub) → `character_fina_masuk`
*   nid 65 "Izza masuk" (asli) + nid 497 "Izza" (stub) → `character_izza_masuk`
*   nid 317 "Ray listening1" (asli) + nid 498 "Ray" (stub) → `character_ray_listening1`
*   nid 290 "Ethan smile7" (asli) + nid 499 "Ethan" (stub) → `character_ethan_smile7`

Query dedupe:

SELECT field\_internal\_name\_value, COUNT(entity\_id) c, GROUP\_CONCAT(entity\_id)
FROM node\_\_field\_internal\_name WHERE bundle='character'
GROUP BY field\_internal\_name\_value HAVING c>1

Stub 496–499 dibuat pada timestamp yang sama (1794672602) oleh sesi paralel, judul generik tanpa nomor pose. Node asli (65/77/290/317) dipertahankan; stub dihapus via `$node->delete()` dalam render context, lalu `TRUNCATE cache_entity` + `drush cache:rebuild`.

### Hasil setelah perbaikan

*   Karakter: 26/26 (semua `fina_masuk` kini dirender)
*   Pause: 1/1, kode: 14/14, annotation: 6/6, h3: 1/1
*   `parser-error`: 0, `Unexpected number`: 0, `not found`: 0, `Missing/invalid close tag`: 0
*   Alias `/lesson/program-fina` utuh (path\_alias id 738, status 1)

### Pelajaran

*   Gate verifikasi halaman harus meng-grep `Unexpected number` selain `not found` — duplikat node internal\_name tidak menghasilkan "not found", hanya "Unexpected number of characters/patterns/exercises ... found".
*   Hilangnya widget bisa total diam: HTTP 200, 0 parser-error, tetapi jumlah kontainer kurang dari inventaris sumber. Selalu bandingkan jumlah widget per kelas dengan inventaris sumber sebelum menyatakan selesai.
*   Untuk pembangunan ulang body skilling: gabungkan dengan `<br />\n`, opsi tag `character.`/`annotation.` diikuti baris kosong sebelum konten dan sebelum closer, dan cek keseimbangan dengan baris logika (bukan `substr_count` yang menghitung ganda closer).