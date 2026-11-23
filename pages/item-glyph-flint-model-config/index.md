# Laporan Konfigurasi Model Hermes

## Tanggal Perubahan
- 2026-11-23

## Perubahan Utama pada Konfigurasi Model

### 1. Primary Model Ditingkatkan ke Free Tier
- Model utama diubah dari `glm-5.3-flash` ke `upstage/solar-mini4:free`
- Provider: `nous`
- Berjalan pada tier gratis (free tier)

### 2. Dua Alias Model Baru di Provider CommandCode
Dua alias model baru ditambahkan di bawah provider `commandcode`:

| Alias | Format | Provider |
|-------|--------|----------|
| `glyph-cluster` | `commandcode/glyph-cluster:free` | commandcode |
| `flint-v1.0` | `commandcode/flint-v1.0:free` | commandcode |

### 3. Penyesuaijan `.env` API Key
- Perlu diverifikasi ulang `COMMAND_CODE_API_KEY` di `/opt/data/.env`
- Pastikan key tidak menggunakan prefix `user_` (CommandCode menolak `user_` prefix, memerlukan `sk_-` atau `nvapi-` prefix)

### 4. Struktur `config.yaml` yang Diperbaru
```yaml
model:
  provider: nous
  base_url: https://inference-api.nousresearch.com/v1
  default: upstage/solar-mini4:free
  aliases:
    glyph-cluster: commandcode/glyph-cluster:free
    flint-v1.0: commandcode/flint-v1.0:free
terminal:
  backend: local
agent:
  max_turns: 150
  reasoning_effort: medium
telemetry:
  shared_metrics:
    enabled: false
    send: false
    offer_version: 2
```

### 5. Catatan Validasi
- Model `glyph-cluster` dan `flint-v1.0` sekarang diakses melalui CommandCode API (`https://api.commandcode.ai/provider/v1`)
- Pastikan `COMMAND_CODE_API_KEY` di `.env` valid (test dengan curl ke `/models` endpoint)
- Glm-5.3/5.3-flash masih ada di cache `ollama_cloud_models_cache.json` tetapi bukan default aktif

### 6. Ringkasan Daftar Model Free yang Tersedia
| Nr | Model Command Code | Source | Harga |
|----|-------------------|--------|-------|
| 1 | `upstage/solar-mini4:free` | local | $0.00/1M |
| 2 | `poolside/laguna-xs-2.1:free` | openrouter | $0.00/1M |
| 3 | `poolside/laguna-s-2.1:free` | openrouter | $0.00/1M |
| 4 | `stepfun/step-3.7-flash:free` | local | $0.00/1M |
| 5 | `reflection/beam-1.0-501b-a23b:free` | local | free |
| 6 | `ornith/ornith-1.5-122b:free` | local | free |

**Sumber:** `nous_recommended_cache.json` (dihasilkan 2026-10-26)
