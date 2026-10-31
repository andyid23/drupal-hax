# ds CLI + Commandcode.ai (cmc) Provider — Panduan Praktis

- Oleh: Hermes Agent
- Sesi: 2026-10-30
- Tujuan: simpan panduan integrasi `ds` CLI (deepseek) dengan provider **commandcode.ai** (`cmc`)

---

## 1. Ringkasan Sesi

Tujuan chat ini: instalasi + konfigurasi `ds` CLI (deepseek-cli, paket `@lzg14/deepseek-cli`) agar bisa dipakai lewat **command line (terminal)** dari sesi agent Hermes, mengarah ke provider **commandcode.ai** (disingkat `cmc`) menggunakan API key.

### Yang selesai dilakukan
1. **Install ds CLI** lewat npm: `@lzg14/deepseek-cli` → versi **0.1.7** (`ds`).
2. **Konfigurasi provider** via ENV: `PROVIDER_CCMDINS_KEY` (API key), `PROVIDER_CCMDINS_URL` (base URL commandcode.ai), `PROVIDER_CCMDINS_MODEL` (model bebas default).
3. **Uji coba** dengan clonasi repo GitHub `andyid23/drupal-hax` lalu diminta ringkasan/descripti ke model cmc.
4. **Simpan panduan** ini ke repo `andyid23/drupal-hax` (branch `main`).

Fakta penting yang terbukti dari eksekusi:
- `npm install -g @lzg14/deepseek-cli` → `ds` v0.1.7.
- Base URL default `ds` = `https://api.ds.com` (tidak diubah global; menggunakan `--base-url` per command).
- `ds chat` **wajib command di awal**, baru flag (`--api-key`, `--base-url`, `--model`).
- Model bebas yang responsif ke `ds` CLI di commandcode.ai: **`reflection/beam:free`**.
- Model lain (`glyph-cluster:free`, `laguna-s-2.1-free`, `beam`, `deepseek-chat`) **gagal/endpoint unsupported** atau free-tier sudah habis.

---

## 2. Cara Install (`ds` CLI)

### Prasyarat
- Node.js >= 18 (`node -v` → v26.12.1)
- npm >= 9 (`npm -v` → 12.0.2)

### 1) Install paket global
```bash
npm install -g @lzg14/deepseek-cli
```

### 2) Verifikasi instalasi
```bash
ds --version        # → ds 0.1.7
ds --help           # lihat subcommands: chat, models, balance, complete, auth, config
```

### 3) (Opsional) Tambahkan ke PATH saat login
`dist/ds.js` sudah berupa symlink/binary global; kalau `ds` belum protein di PATH baru:
```bash
export PATH="$HOME/.npm-global/bin:$PATH"
```
(untuk npm global, bin biasanya di `~/.npm-global/bin`.)

---

## 3. Konfigurasi Provider (commandcode.ai / "cmc")

`ds` CLI membaca API key lewat flag `--api-key` dan base URL lewat `--base-url`. Untuk provider `cmc` (commandcode.ai) gunakan ENV berikut (tanpa mengubah config global `ds`):

```bash
export PROVIDER_CCMDINS_KEY="user_58sy..."          # API key commandcode.ai (Bearer)
export PROVIDER_CCMDINS_URL="https://api.commandcode.ai/provider/v1"
export PROVIDER_CCMDINS_MODEL="reflection/beam:free"   # model bebas default
```

> Catatan: commandcode.ai punya_free_ model seperti `glyph-cluster:free`,
> `laguna-s-2.1-free`, `beam`. Yang **berhasil merespon chat `ds`** adalah
> `reflection/beam:free` (model safe/open, cepat, cocok analisa kode). Model lain
> (`glyph-cluster:free`, `laguna-s-2.1-free`, `beam`, `deepseek-chat`) gagal
> `unsupported_model` / free-tier habis.

### (Opsional) Konfigurasi persisten shell
Supaya env otomatis di setiap login, simpan baris di atas ke `~/.bashrc` atau `~/.zshrc`:
```bash
cat >> ~/.bashrc <<'EOF'
export PROVIDER_CCMDINS_KEY="user_58sy..."
export PROVIDER_CCMDINS_URL="https://api.commandcode.ai/provider/v1"
export PROVIDER_CCMDINS_MODEL="reflection/beam:free"
EOF
source ~/.bashrc
```

---

## 4. Cara Pakai `ds` dengan Provider cmc

### 4a. Chat (satu baris)
```bash
ds chat --api-key "$PROVIDER_CCMDINS_KEY" \
        --base-url "$PROVIDER_CCMDINS_URL" \
        --model "$PROVIDER_CCMDINS_MODEL" \
        "explain HAXcms micro-site"
```

Atau pakai helper `ds-prov.sh` (rekomendasi, tidak ubah global config):
```bash
export PROVIDER_CCMDINS_KEY="..."
export PROVIDER_CCMDINS_URL="https://api.commandcode.ai/provider/v1"
export PROVIDER_CCMDINS_MODEL="reflection/beam:free"

/opt/data/workspace/ds-prov.sh ccmdins chat "jelaskan repo drupal-hax ini"
```

### 4b. Daftar model / saldo
```bash
ds --api-key "$PROVIDER_CCMDINS_KEY" --base-url "$PROVIDER_CCMDINS_URL" models
ds --api-key "$PROVIDER_CCMDINS_KEY" --base-url "$PROVIDER_CCMDINS_URL" balance
```

### 4c. Contoh: analisa repo GitHub (learned dari session ini)
```bash
git clone https://github.com/andyid23/drupal-hax.git /opt/data/workspace/drupal-hax
cd /opt/data/workspace/drupal-hax
export PROVIDER_CCMDINS_KEY="..."   # atau export di atas
/opt/data/workspace/ds-prov.sh ccmdins chat \
  --max-tokens 700 \
  "Repo drupal-hax: 3 peran README.md, package.json, Dockerfile. 6 bullet ringkasan. README clone-ready."
```

Output (contoh yang berhasil):
```
Repo drupal-hax adalah proyek integrasi yang menghubungkan Drupal dengan HAX...
```

---

## 5. Pakai di Sesi Chat Lain (multi-session)

### Caranya:
1. **Env harus ada di shell kamu**. Kalau mau persisten:
   ```bash
   cat >> ~/.bashrc <<'EOF'
   export PROVIDER_CCMDINS_KEY="user_58sy..."
   export PROVIDER_CCMDINS_URL="https://api.commandcode.ai/provider/v1"
   export PROVIDER_CCMDINS_MODEL="reflection/beam:free"
   EOF
   source ~/.bashrc
   ```
   (Ganti `user_58sy...` dengan key asli milikmu.)

2. **Import env ke sesi baru**:
   ```bash
   source ~/.bashrc
   ds-prov.sh ccmdins chat "tanya apa saja"
   ```
   atau tanpa wrapper:
   ```bash
   ds chat --api-key "$PROVIDER_CCMDINS_KEY" \
          --base-url "$PROVIDER_CCMDINS_URL" \
          --model "$PROVIDER_CCMDINS_MODEL" "tanya"
   ```

3. **Script reuse**: simpan `ds-prov.sh` di `$HOME/bin` atau `$WORKSPACE`, lalu panggil kapan saja.

> Alasan `ds-prov.sh`: `ds` CLI butuh **command di awal** (`chat models`), tidak boleh flag dulu; wrapper ini menyusun urutan yang benar dan tidak menimpa config global `ds`.

---

## 6. Simpan ke Repo `andyid23/drupal-hax`

```bash
# 1. Repo sudah clone & bersih di workspace (dari langkah sebelumnya):
cd /opt/data/workspace/drupal-hax

# 2. Tulis panduan ini (contoh simpan ke file di repo)
cat > docs/ds-cli-cmc.md <<'EOF'
<isi panduan di atas>
EOF

# 3. Commit & push
git add docs/ds-cli-cmc.md
git commit -m "Add: ds CLI + commandcode.ai (cmc) provider guide"
git push origin main

# 4. Verifikasi
git log --oneline -2
```

Folder/lokasi yang disarankan:
```
/opt/data/workspace/drupal-hax/docs/ds-cli-cmc.md
```

---

## 7. Troubleshooting Singkat

| Masalah | Penyebab | Solusi |
|---|---|---|
| `ds: command not found` | npm global bin belum di PATH | `export PATH="$HOME/.npm-global/bin:$PATH"` |
| `No API key` | ENV `PROVIDER_CCMDINS_KEY` belum diset | Set ENV atau `ds auth login` |
| `Model … not supported on endpoint` | model bebas cmc salah | Pakai `reflection/beam:free` |
| `400: Missing required parameter …` | body curl tanpa conversation history padahal model butuh reasoning | gunakan `ds` CLI (bukan curl mentah) |
| `ENOTFOUND` / `ECONNREFUSED` | jaringan/proxy | set `HTTPS_PROXY` / `HTTP_PROXY` |

---

> **Catatan keamanan**: jangan taruh API key di file teks yang bisa dibaca orang lain. Gunakan `ds auth login` atau env di shell, bukan hard-code key di script.
