# CORE_AFFINITY_MASK per chipset

CORE_AFFINITY_MASK = bitmask hex yg ngebatesin CPU core mana yg dipake game.
Tujuan: pin game ke big core only → thermal lebih stabil + frequency lebih konsisten + ga ke-bounce ke little core (yg bikin frame drop).

Format: bit 0 = Core 0, bit 1 = Core 1, dst.
- `0xC0` = bit 6+7 = Core 6 dan 7.
- `0xF0` = bit 4-7 = Core 4 sampai 7.
- `0x80` = bit 7 doang = Core 7.
- `0x00` = disable pinning (semua core).

---

## 0xC0 (2 big core: Core 6 & 7)
Chipset:
- Helio G99
- Dimensity 6020 / 6080 / 7020 / 7030
- Snapdragon 6 Gen 1, Gen 2, Gen 3

WHY: arsitektur 2 big core + 6 little. Pin ke 2 big core paling kenceng.
TRADE-OFF: 2 core kadang kurang buat game multi-thread modern (UE4/5 minta 4 core paralel).

## 0xF0 (4 big-class core: Core 4-7)
Chipset:
- Dimensity 7300 series
- Dimensity 8020 / 8050 / 8100 / 8200
- Snapdragon 7 Gen 1, 7s Gen 2, 7 Gen 3, 7s Gen 3

WHY: arsitektur 4 big + 4 little (atau 1 prime + 3 big + 4 little). 4 big bandwidth lebih bagus buat multi-thread.
TRADE-OFF: 4 core jalan barengan = panas naik lebih cepet, throttle lebih rentan.

## 0x80 (1 prime core: Core 7 doang)
Chipset:
- Dimensity 8300 / 8350
- Dimensity 9000 / 9200 / 9300 / 9400 / 9500
- Snapdragon 6 Gen 4, Gen 5
- Snapdragon 7+ Gen 2, 7 Gen 4, 7s Gen 4, 7+ Gen 3
- Snapdragon 8 Gen 1 → 8s Gen 4

WHY: prime core (X4/X925) frequency tinggi banget (>3GHz). Game single-thread heavy (emulasi x86 di Box64/FEX = banyak single-thread bottleneck) lebih cepet di prime saja.
TRADE-OFF: game multi-thread ke-bottleneck di 1 core. Pakai kalau game lu CPU-bound single-thread.

## 0x00 (disable pinning, semua core setara)
Chipset:
- Dimensity 8400 Ultra (8× Cortex-A725, all-big design)
- Snapdragon 8 Elite / Gen 5 (Oryon CPU, all-performance)

WHY: arsitektur all-big — kagak ada little core lambat yg perlu dihindari. Pinning malah kontraproduktif (cegah scheduler optimal).
TRADE-OFF: ga ada. Disable = best.

---

## Kalau ga yakin
Default safest: `0x00` (no pinning, biarin scheduler kerja). Pinning baru ngaruh kalau lu tau pasti core layout chipset.

Cek core layout: pasang **CPU-Z** atau **DevCheck** → tab CPU → lihat jumlah Big/Mid/Little.
