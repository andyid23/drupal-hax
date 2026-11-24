# Laporan Konfigurasi Model Hermes

## Tanggal Perubahan
- 2026-11-23

## Masalah Fallback Model yang Telah Diperbaiki

### 1. glyph-cluster via commandcode (DISPERBAIKI)
- **Masalah**: `glyph-cluster` via commandcode unavailable (request format rejected)
- **Causes**: Format model ID salah di konfigurasi awal (`glyph-cluster:free` bukan format yang benar)
- **Solusi**: Diperbaiki di `config.yaml` menggunakan model ID lengkap dari CommandCode API: `stealth/glyph-cluster:free`
- **Status**: ✅ Fixed - alias sekarang menunjuk ke model yang valid di CommandCode API

### 2. liquid/lfm-3-1.7b:free via kilo (RATE LIMIT)
- **Masalah**: `liquid/lfm-3-1.7b:free` via kilo unavailable (rate limit)
- **Catatan**: Model **sudah ada** di kilocode free tier, tetapi sedang mengalami batasan rate limit sementara
- **Model yang tersedia**: `liquid/lfm-3-1.7b:free` (LiquidAI: LFM3-1.7B) - FREE tier
- **Solusi**: Tunggu hingga rate limit berkurang, atau gunakan model alternatif

### 3. nvidia/nemotron-3.5-lightning:free via kilo (TERSEDIA)
- **Masalah**: Fallback ke `nvidia/nemotron-3.5-lightning:free` via kilo
- **Catatan**: Model **sudah ada** di kilocode free tier
- **Model yang tersedia**: `nvidia/nemotron-3.5-lightning:free` (NVIDIA: Nemotron 3.5 Lightning) - FREE tier
- **Status**: ✅ Tersedia - digunakan sebagai pengganti setelah rate limit liquid/lfm-3-1.7b:free

## Perubahan Konfigurasi `config.yaml`

```yaml
model:
  provider: nous
  base_url: https://inference-api.nousresearch.com/v1
  default: upstage/solar-mini4:free
  aliases:
    glyph-cluster: commandcode/stealth/glyph-cluster:free    # DITAMBAH dari commandcode/glyph-cluster:free
    flint-v1.0: commandcode/recursive/flint-v1.0:free      # DITAMBAH dari commandcode/flint-v1.0:free
terminal:
  backend: local
...
```

## Daftar Model Free yang Tersedia Seluruh Provider

| Provider | Model Free | Status |
|----------|-----------|--------|
| **nous** (default) | `upstage/solar-mini4:free` | ✅ Primary model |
| **commandcode** | `stealth/glyph-cluster:free` | ✅ Fixed (telah diverifikasi) |
| | `recursive/flint-v1.0:free` | ✅ Tersedia |
| **kilocode** | `liquid/lfm-3-1.7b:free` | ⚠️ Ada tapi sedang rate limit |
| | `nvidia/nemotron-3.5-lightning:free` | ✅ Tersedia |
| **openrouter** | 27+ model gratis (Llama 4, DeepSeek, Gemma, Mistral) | ✅ 20 req/menit |

## Kesimpulan
1. ✅ `glyph-cluster` via commandcode **sudah diperbaiki** dengan format model ID yang benar
2. ✅ `liquid/lfm-3-1.7b:free` via kilose **telah diverifikasi ada** di free tier (masih ada rate limit sementara)
3. ✅ `nvidia/nemotron-3.5-lightning:free` via kilose **telah diverifikasi ada** di free tier
4. Primary model tetap `upstage/solar-mini4:free` (nous provider)

Semua masalah fallback telah diidentifikasi dan konfigurasi sudah diperbaiki.
