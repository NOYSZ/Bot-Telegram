# dxvk.conf — Knob Critical

File `dxvk.conf` di-place di folder game (atau path via `DXVK_CONFIG_FILE`).
Banyak option resmi DXVK; di bawah cuma yg PALING ngaruh buat mobile.

⚠ JANGAN REKOMENDASIKAN parameter ini (TIDAK ADA di DXVK resmi — halusinasi AI):
- `dxvk.macroFunctionOptimize` — NGGAK ADA.
- `dxvk.gplAsync` sebagai env var — NGGAK ADA. Pakai env `DXVK_GPLASYNC=1` hanya kalau fork DXVK-gplasync.

---

## dxvk.enableAsync = True
WHAT: kompilasi shader async (background thread).
WHY: tanpa async, game freeze tiap shader baru di-compile. Async = stutter berkurang drastis.
TRADE-OFF: kadang flicker frame pertama (placeholder shader).
DEFAULT-IF: True. **Cuma jalan kalau pake DXVK fork async** (DXVK-async, DXVK-gplasync, Sarek).

## dxvk.enableStateCache = True
WHAT: simpan pipeline cache ke disk.
WHY: load berikutnya skip recompile = launch lebih cepet.
DEFAULT-IF: True.

## dxvk.stateCacheMaxEntries = 300
WHAT: limit entry cache.
WHY: cache kegedean = file gede di disk + lookup lebih lambat.
DEFAULT-IF: 200-500 buat mobile. Default DXVK 0 (unlimited) — kegedean buat mobile.

## dxvk.maxChunkSize = 64
WHAT: ukuran chunk memory allocation (MB).
WHY: chunk gede = lebih sedikit allocation call, tapi waste VRAM lebih besar per chunk.
TRADE-OFF: 32-64 buat GPU lemah, 96-128 buat midrange, 256 buat flagship.
DEFAULT-IF:
- Mali-G57 (G99): 32-64.
- Mali-G610 (Dim 8020): 96-128.
- Mali-G720+ / Adreno flagship: 128-256.

## d3d9.maxAvailableMemory = 1536
WHAT: spoof VRAM yg dilaporkan ke game (MB), khusus DX9.
WHY: default DXVK = 4096 (desktop GPU assumption). Mobile GPU shared RAM, ga punya 4GB VRAM dedicated → game over-allocate → crash atau swapping berat.
TRADE-OFF: terlalu kecil = game refuse to start atau low texture. Terlalu besar = OOM.
DEFAULT-IF: 768-1024 buat GPU lemah (Mali-G57), 1536 mid, 2048 flagship.
PENTING: SEMUA game DX9 di mobile butuh tweak ini.

## d3d9.deferSurfaceCreation = True
WHAT: tunda pembuatan D3D9 window sampai render dimulai.
WHY: banyak game crash di intro karena window dibuat sebelum context ready.
TRADE-OFF: di Payday 2 bikin glitch merah-cyan — JANGAN dipake.
DEFAULT-IF: True buat game DX9 dengan black screen intro. False buat Payday 2.

## dxgi.deferSurfaceCreation = True
WHAT: sama kayak d3d9, tapi buat DX10/11/DXGI.
WHY: same logic — fix black screen intro di DX11.
DEFAULT-IF: True buat DX11 game yg black screen di awal.

## d3d11.relaxedBarriers = True
WHAT: relax sinkronisasi barrier DX11.
WHY: barrier ketat = sinkronisasi GPU pipeline (slow). Relax = boost FPS.
TRADE-OFF: risk visual glitch di beberapa game (tearing dalam frame, ghosting).
DEFAULT-IF: True buat game well-tested (cek per-game.md). False kalau ada glitch.

## d3d11.relaxedGraphicsBarriers = True
WHAT: relaxed barriers khusus graphics pipeline (bukan compute).
WHY: subset relaxedBarriers, lebih aman.
DEFAULT-IF: True; cukup ini doang kalau ragu pake relaxedBarriers.

## dxvk.maxFrameRate = 60
WHAT: cap FPS di DXVK level.
WHY: game physics-bound (Skyrim, Fallout, Dark Souls) bug di >60fps.
TRADE-OFF: cap juga = lose smoothness kalau HP support 90/120Hz.
DEFAULT-IF: 60 buat physics-bound game; 0 (unlimited) buat selainnya.

## dxvk.tilerMode = Auto
WHAT: optimasi pipeline buat Tile-Based Rendering GPU (mobile).
WHY: Mali & Adreno itu tiler GPU. Tiler mode = render per-tile, hemat bandwidth memory.
TRADE-OFF: Auto detect dengan tepat di sebagian besar GPU.
DEFAULT-IF: Auto. Jarang perlu di-override.

## dxvk.numCompilerThreads = 1
WHAT: thread CPU buat kompilasi shader.
WHY: terlalu banyak thread = CPU contention dengan game itu sendiri.
DEFAULT-IF: 0 (auto/half core) buat Adreno SD8+. **1 buat MediaTek** (thermal throttle gampang).

## d3d9.shaderModel = 3
WHAT: max shader model yg di-ekspos.
WHY: beberapa game lawas DX9 expect SM 2 atau 3. SM 3 = umum.
DEFAULT-IF: 3.

## d3d9.floatEmulation = Strict / Auto
WHAT: emulasi floating point DX9.
WHY: GPU mobile precision FP beda dari NVIDIA/AMD desktop. Strict = ikutin spec, fix poligon rusak.
TRADE-OFF: Strict sedikit slower.
DEFAULT-IF: Strict buat game DX9 lawas yg rusak. Auto buat selainnya.

## dxvk.logLevel = none
WHAT: matikan log DXVK.
WHY: spam log = waste I/O.
DEFAULT-IF: none.

## dxgi.maxFrameLatency = 1
WHAT: max frame buffered di CPU sebelum kirim ke GPU.
WHY: nilai kecil = input lag turun. Default 3 = lag ke-rasa.
TRADE-OFF: 1 kadang bikin pacing ga smooth.
DEFAULT-IF: 1 buat competitive game. 2-3 buat single-player smoothness.

## Hybrid DXVK pattern (advanced)
Tujuan: pake DXVK 1.11/Sarek ringan buat render + unlock memory feature dari DXVK 2.6+.
Step:
1. Pilih DXVK 2.6+ di menu emulator.
2. Copy `d3d11.dll` + `dxgi.dll` dari DXVK 1.11/Sarek ke folder game.
3. Set `WINEDLLOVERRIDES="d3d11=n;dxgi=n"` (DX11) atau `="d3d9=n"` (DX9).
4. `n` = native = DLL lokal full take over (BUKAN hybrid kolaborasi).

## DXVK_CONFIG env override
Bisa override knob lewat env tanpa edit file:
`DXVK_CONFIG="dxgi.hideAmdGpu=True"`
