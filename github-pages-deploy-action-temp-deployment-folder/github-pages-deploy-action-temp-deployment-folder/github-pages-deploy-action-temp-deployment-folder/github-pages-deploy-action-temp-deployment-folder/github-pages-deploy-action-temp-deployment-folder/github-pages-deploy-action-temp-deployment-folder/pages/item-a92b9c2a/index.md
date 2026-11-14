<h2>Clone Lesson: simple-programs → vba.pesanweb.my.id</h2>

<h2>1. Dasar dan Sumber</h2>
<ul>
<li>Sumber: <code>https://vba.skilling.us/lesson/simple-programs</code></li>
<li>Target: <code>vba.pesanweb.my.id</code> (Drupal 8 multisite)</li</li>
<li>Skill: <code>drupal-lesson-authoring</code> (runbook <code>references/clone-runbook.md</code>)</li>
</ul>

<h2>2. Hasil Eksekusi Lokal (TUI)</h2>
<p>Langkah-langkah runbook yang dieksekusi di environment local TUI:</p>

<ol>
<li><b>Step 3: Extract Source Body</b> — DHTML source dari VBA skilling, ekstrak body lesson. Body berisi teks: "This module is about the simplest programs. Get some input, do a calculation, and send the results somewhere."</li>
<li><b>Step 4: Inventory Source</b> — Menghitung widget: h3=0, pause containers=0, character containers=0, MCQ blocks=0, <img> tags=0. Tidak ada karakter, MCQ, atau pause dalam lesson ini.</li>
<li><b>Step 6: Build Body + Spec JSON</b> — Membangun spec JSON dengan format skilling, validasi lulus: tidak ada karakter <code>|</code> di body, format body valid menggunakan <p> tag.</li>
</ol>

<h2>3. Spec JSON Dibuat</h2>
<pre><code class="language-json">{
  "title": "Simple Programs",
  "alias": "/lesson/simple-programs",
  "order": 2,
  "body": "<p>This module is about the simplest programs. Get some input, do a calculation, and send the results somewhere.</p>",
  "notes": "Adaptasi dari https://vba.skilling.us/lesson/simple-programs. Tidak ada karakter, MCQ, pause, atau heading dalam lesson ini."
}</code></pre>

<h2>4. Base64 Encoding (Step 7 Format)</h2>
<pre><code class="language-text">eyJ0aX... (456 bytes)</code></pre>

<h2>5. Validasi Spec</h2>
<ul>
<li>✓ Tidak ada pipe character <code>|</code> di body</li>
<li>✓ Body format valid (pakai <p>, tidak ada bare <br>)</li>
<li>✓ Base64 decode benar</li>
<li>✓ Kompatibel dengan <code>create_lesson.php</code> (type='lesson', status=1, body format 'skilling')</li>
</ul>

<h2>6. Langkah yang Memerlukan Infra Remote</h2>
<p>Langkah-selanjutnya dari runbook (1, 2, 5, 7–12) memerlukan akses SSH ke <code>home.pesanweb.my.id:2222</code> melalui Cloudflare Tunnel, dengan Docker container:</p>
<ul>
<li><code>d8_fpm</code> — container PHP/Drush (root: <code>/var/www/html2/drupal8/web</code>)</li>
<li><code>drupal_web_server</code> — nginx (port 81 untuk HTTP checks)</li>
</ul>
<p>Tahapan yang belum dieksekusi:</p>
<ol>
<li>Discover environment (cek container PHP dan port D8)</li>
<li>Confirm target page existence (HTTP 404 = CREATE, 200 = UPDATE)</li>
<li>Resolve dependencies against DB (character mapping, MCQ handling)</li>
<li><b>Step 7: Create Node</b> — base64-decode spec, jalankan <code>php:script create_lesson.php</code> via Drush</li>
<li><b>Step 8: Re-assert Alias</b> — mencegah pathauto menulis ulang alias</li>
<li><b>Step 9: Add to Book Outline</b> — tambah ke book outline menggunakan book.manager API</li>
<li><b>Step 10: Rebuild Cache</b> — <code>drush cache:rebuild</code></li>
<li><b>Step 11: Verify</b> — 5 gate checks (HTTP 200, widget counts match, errors=0, images 200, book nav benar)</li>
<li><b>Step 12: If Something Fails</b> — troubleshooting table dalam runbook</li>
</ol>

<h2>7. File Lokal yang Dibuat</h2>
<ul>
<li><code>/opt/data/cache/scratch/spec_simple_programs.json</code> — spec JSON (353 bytes)</li>
<li><code>/opt/data/cache/scratch/spec_simple_programs.b64</code> — base64-encoded spec (456 bytes)</li>
<li><code>/opt/data/cache/scratch/source_body_raw_simple.html</code> — source HTML (25,420 bytes)</li>
<li><code>/opt/data/cache/scratch/body_div_simple.html</code> — extracted body div (122 bytes)</li>
</ul>

<h2>8. Status</h2>
<p><strong>Setengah-setiap (half-done)</strong>: Langkah 3–4 dan 6 selesai di environment lokal. Sisa langkah 1, 2, 5, 7–12 memerlukan infrastruktur Drupal 8 multisite yang ada di <code>home.pesanweb.my.id</code>. Spec JSON dan base64 encoding sudah siap untuk dideploy ke remote server.</p>

<p><small>Dokumen ini dibuat otomatis dari TUI session. Untuk melengkapi clone end-to-end, akses SSH ke <code>home.pesanweb.my.id</code> dan jalankan create_lesson.php dengan spec files yang sudah dibuat.</small></p>
