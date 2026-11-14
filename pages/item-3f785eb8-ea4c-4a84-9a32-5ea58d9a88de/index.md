Kloning lesson "Adela's turn" ke vba.pesanweb.my.id
---------------------------------------------------

Source: `https://vba.skilling.us/lesson/adelas-turn` → target `https://vba.pesanweb.my.id/lesson/adelas-turn` (node 349, judul "Giliran Izza"). Dibuat dengan runbook `references/clone-runbook.md` dari skill `drupal-lesson-authoring`.

### Inventaris source & hasil render

*   Pause: source 9, render 9
*   h3: source 2 ("Debugging" → "Debugging", "Indenting" → "Indentasi"), h2 = 0
*   Character container: source 16, render 16
*   Note box (annotation.): source 4, render 4, judul "Catatan"
*   Code block: source 11, render 11
*   Parser error: 0; tidak ada markup mentah (pause./character.) yang bocor ke halaman
*   Sequence widget (urutan pause/char/code/h3/note): identik dengan source

### Pemetaan karakter (Adela/Georgia → Izza/Fina)

*   Adela smile1/2/5/8/yay1 → `character_izza_senang` (5 kontainer)
*   Adela thinking1 → `character_izza_kenapa`; Adela stressed1 → `character_izza_sedih`; Adela oh\_no1 → `character_izza_heran`
*   Georgina smile5/smile4 → `character_fina_senang` (2); Georgina neutral1 → `character_fina_masuk`
*   Ethan (talking5, smile14, smile17) dan Ray (neutral1, smiling2) dipertahankan sesuai source

### Temuan infra 1: internal\_name karakter "Izza sedih" ada leading space

Saat render pertama, container izza\_sedih gagal (15/16) dengan error `Character with internal_name character_izza_sedih not found`, padahal nilainya ada di DB. Penyebab: nilai tersimpan `' character_izza_sedih'` (ada spasi di depan) pada `node__field_internal_name` nid 63. Dicek dengan `CONCAT('[',field_internal_name_value,']')` → `[ character_izza_sedih]`. Dibersihkan lewat `$node->set('field_internal_name', ...)` dalam render context, lalu 16/16 kontainer render.

### Temuan infra 2: alias manual ikut terhapus oleh save node lain

Alias `/lesson/adelas-turn` dibuat terakhir sesuai urutan aman, tapi setelah perbaikan node karakter 63 + `cache:rebuild`, alias itu terhapus lagi oleh pathauto (halaman 404 "Page not found"). Perbaikan alias diulang setelah save gelombang terakhir apapun; pathauto juga meninggalkan dua baris duplikat untuk alias hasil generasinya (`/lesson/giliran-izza`), yang lalu dideduplikasi. Akhirnya kedua alias (manual dan hasil pathauto) masing-masing tepat satu baris dan keduanya HTTP 200.

### Format body yang terbukti

*   Dialog karakter dan isi annotation menerima HTML mentah per baris logis: `<p>…</p>` beruntun, `<img class="skilling-parser-image figure">` dengan src absolut (alt dan title diterjemahkan), bahkan blok kode `<div class="skilling-code-container">` utuh di dalam dialog (karakter menampilkan pseudo-kode).
*   Hitungan keseimbangan tag harus per baris logis, bukan substring: `annotation.<br />` ikut terhitung dobel karena `/annotation.<br />` memuat substring yang sama (8 vs 4 saat pertama dicek).
*   Path image source relatif (`/sites/default/files/.../write-first-program/*.png`) dijadikan absolut ke `https://vba.skilling.us`; seluruh 6 URL unik dicek HTTP 200 dengan UA browser (curl tanpa UA kena 404/406).

### Verifikasi akhir

*   `/lesson/adelas-turn` dan `/lesson/giliran-izza`: HTTP 200, judul "Giliran Izza | Pemrograman dengan Excel VBA"
*   Book outline: nid 349, bid 330, pid 343 ("Simple Programs"), weight -13, depth 3; navigasi prev "Menulis Program Pertama" aktif (next "Generalize" memang belum dikloning)
*   11 gambar eksternal di halaman: 0 error

Temuan sudah dicatat ke skill `drupal-lesson-authoring` (SKILL.md + references/clone-runbook.md).