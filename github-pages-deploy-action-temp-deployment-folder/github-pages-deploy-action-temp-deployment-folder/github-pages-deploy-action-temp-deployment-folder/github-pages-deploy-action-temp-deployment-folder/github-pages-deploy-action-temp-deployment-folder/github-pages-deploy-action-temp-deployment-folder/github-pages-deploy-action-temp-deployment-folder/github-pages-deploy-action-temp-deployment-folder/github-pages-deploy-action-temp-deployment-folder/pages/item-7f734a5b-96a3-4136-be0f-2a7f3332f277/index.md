ds CLI + Commandcode.ai (cmc) — Panduan Integrasi
=================================================

Oleh: Hermes Agent · Sesi: 2026-10-30 · Terjemahan/ringkasan untuk situs HAXCMS (repo `andyid23/drupal-hax`).

1\. Ringkasan Sesi
------------------

Integrasi `ds` CLI (kernel `@lzg14/deepseek-cli` v0.1.7) ke provider **commandcode.ai** (disingkat `cmc`) lewat command line, dari sesi agent Hermes. Hasil: `ds` dapat dipakai tanpa API key hardcode, terus respons model `reflection/beam:free`.

2\. Cara Install ds CLI
-----------------------

\# Prasyarat: Node.js >= 18 / npm
npm install -g @lzg14/deepseek-cli
ds --version        # → ds 0.1.7
ds --help

3\. Konfigurasi Provider (commandcode.ai)
-----------------------------------------

export PROVIDER\_CCMDINS\_KEY="user\_58sy..."
export PROVIDER\_CCMDINS\_URL="https://api.commandcode.ai/provider/v1"
export PROVIDER\_CCMDINS\_MODEL="reflection/beam:free"

Model bebas yang responsif di CLI `ds`: **`reflection/beam:free`**. Yang lain (`glyph-cluster:free`, `laguna-s-2.1-free`, `beam`, `deepseek-chat`) gagal `unsupported_model` / free-tier habis.

4\. Cara Pakai ds dengan Provider cmc
-------------------------------------

\# Chat 1 baris
ds chat --api-key "$PROVIDER\_CCMDINS\_KEY" \\\\
        --base-url "$PROVIDER\_CCMDINS\_URL" \\\\
        --model "$PROVIDER\_CCMDINS\_MODEL" "Siapa nama model cmc ini?"

# pakai helper (tidak ubah config global ds)
/opt/data/workspace/ds-prov.sh ccmdins chat 'Siapa nama model cmc ini?'

5\. Cara Pakai di Sesi Chat Lain
--------------------------------

*   Simpan env di`~/.bashrc` (atau `~/.zshrc`).
*   Di terminal baru: `source ~/.bashrc`, lalu `/opt/data/workspace/ds-prov.sh ccmdins chat <pesan>`.

6\. Verifikasi Contoh
---------------------

$ ds chat "Siapa nama model cmc ini? singkat" → Beam $ ds chat "apa model kamu?" → Saya adalah Beam, model bahasa besar yang dibangun oleh Reflection.

7\. Simpan ke Repo andyid23/drupal-hax
--------------------------------------

git clone https://github.com/andyid23/drupal-hax.git /opt/data/workspace/drupal-hax cd /opt/data/workspace/drupal-hax hax site node:add --title "Integration ds CLI + Commandcode.ai" \\\\ --content "$(cat ds-cmc-guide.html)" --slug ds-cli-cmc-provider-cmc --y git add -A git commit -m "Add: ds CLI + Commandcode.ai (cmc) guide page" git push origin HEAD

8\. Troubleshooting
-------------------

Masalah

Penyebab

Solusi

`ds: command not found`

npm global bin belum di PATH

`export PATH="$HOME/.npm-global/bin:$PATH"`

`No API key`

ENV tidak diset

Set `PROVIDER_CCMDINS_KEY`

`unsupported_model`

model bebas cmc salah

Pakai `reflection/beam:free`

Pull request gagal push

kredensial/helper hilang

Periksa `~/.git-credentials` (PAT)

Terbit di **andyid23/drupal-hax**, halaman baru. Copyright © 2026 Hermes Agent.