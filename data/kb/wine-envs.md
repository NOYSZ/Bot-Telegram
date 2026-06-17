# Wine env vars

Berlaku di **SEMUA emulator** (Winlator-type & GameHub-type — keduanya pake Wine).

---

## WINEDEBUG = -all
WHAT: matikan SEMUA log Wine.
WHY: tanpa ini, Wine nulis ribuan baris log per detik ke buffer memory → habiskan RAM, slow I/O.
TRADE-OFF: ga ada log buat debugging. Tapi gaming = matiin.
DEFAULT-IF: -all. WAJIB SELALU.
Sub-opsi: `WINEDEBUG=-all,+d3d` = matikan semua kecuali d3d (buat debug DXVK).

## WINEESYNC = 1
WHAT: event-based sync via eventfd Linux.
WHY: Wine default pake server roundtrip buat sinkronisasi thread (slow). ESYNC pakai eventfd kernel = jauh lebih cepet, stabil buat DX11/12.
TRADE-OFF: hampir ga ada.
DEFAULT-IF: 1. WAJIB SELALU.

## WINEFSYNC = 1
WHAT: futex-based sync, alternatif ESYNC.
WHY: futex lebih low-level dari eventfd. Beberapa game lebih stabil di FSYNC.
TRADE-OFF: JANGAN aktifkan bareng ESYNC. Pilih salah satu.
DEFAULT-IF: 0 (pake ESYNC dulu). Switch ke FSYNC kalau ESYNC unstable.

## WINEDLLOVERRIDES
WHAT: override DLL Wine. Format `nama=mode`.
Mode:
- `n` = native (pake DLL dari file di folder game) — buat DXVK/VKD3D.
- `b` = builtin (pake Wine internal) — buat WineD3D.
- `n,b` = native dulu, fallback builtin.
- (kosong) = disable DLL itu.

WHY: bot harus tau game pake DX berapa dulu sebelum set ini.
Contoh:
- DX11 + DXVK: `d3d11=n;dxgi=n`
- DX9 + DXVK: `d3d9=n`
- DX12 + VKD3D: `d3d12=n;dxgi=n`
- DX8 lama (SH2/SH3): `d3d8=n,d3d9=n` (pake d3d8to9 wrapper)
- Bypass Steam DRM: `steam_api64=n`

## WINE_LARGE_ADDRESS_AWARE = 1
WHAT: extend address space app 32-bit dari 2GB → 4GB.
WHY: banyak game 32-bit lawas OOM karena limit 2GB. Flag ini unlock 4GB pakai PE LAA flag.
TRADE-OFF: ga ada — pure benefit buat 32-bit. Ga ngaruh buat 64-bit game.
DEFAULT-IF: 1.

## STAGING_SHARED_MEMORY = 1
WHAT: optimasi shared memory dari Wine Staging.
WHY: speedup IPC antar proses Wine (game ↔ wineserver).
DEFAULT-IF: 1.

## STAGING_RT_PRIORITY_SERVER / _BASE
WHAT: prioritas realtime untuk wineserver / Wine thread (1-99).
WHY: thread Wine yg ngurusin sync sering kena starve. Naikin prio = response time turun.
TRADE-OFF: prio kegedean = ganggu thread lain di HP (termasuk audio).
DEFAULT-IF: 90 buat keduanya. Turunin kalau audio crackling.

## PULSE_LATENCY_MSEC = 60
WHAT: latency buffer audio PulseAudio (ms).
WHY: kecil = responsif tapi risk crackling. Besar = stabil tapi audio delay.
TRADE-OFF: range aman 30-120ms.
DEFAULT-IF: 60. Naikin ke 90-120 kalau audio crackling.

## Sync wajib SEMUA game
- `WINEDEBUG=-all`
- `WINEESYNC=1` ATAU `WINEFSYNC=1` (pilih satu)
- `WINE_LARGE_ADDRESS_AWARE=1` (kalau game 32-bit)
