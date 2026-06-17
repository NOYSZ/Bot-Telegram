# FEXCore — Translation Params (GameHub-type ONLY)

⚠ EMU SCOPE: Berlaku di **GameHub/BannerHub/Mobox/WinNative** (FEX-based).
**TIDAK berlaku** di Winlator (pake Box64 — lihat `box64-envs.md`).

FEXCore translation params ADA DI SETTINGS GUI emulator (Translation Params section),
plus subset di-set lewat env var `FEX_*`.

---

## FEX_TSOENABLED / "TSO Memory"
WHAT: emulasi Total Store Ordering x86. ON / OFF.
WHY: x86 punya TSO — semua store antar core selalu observable in-order. ARM weak ordering. Game multithread yg rely ke TSO bisa crash/desync kalau di-skip. FEX nge-emulate via memory barrier per store di translation.
TRADE-OFF: ON = aman tapi overhead barrier mahal di ARM (~15-25% FPS loss). OFF = lebih cepet tapi risk crash/desync di game multi-thread.
DEFAULT-IF: ON kalau ga yakin.

### Matrix per-engine (TSOEnabled):
- **ON** wajib: RAGE Engine (GTA V), AnvilNext (Assassin's Creed), RE Engine (RE2/3/4/Village), UE4/UE5, Crystal Engine (Tomb Raider), FoxEngine (MGS V).
- **OFF** aman & boost FPS: Diesel Engine (Payday 2), Sleeping Dogs (DE & original).

## FEX_VECTORTSOENABLED / "Vector TSO"
WHAT: TSO khusus SIMD/vector store. ON / OFF.
WHY: subset TSO — fokus ke vector instruction (SSE/AVX store).
TRADE-OFF: bisa OFF terpisah dari TSOEnabled kalau game cuma rely scalar.
DEFAULT-IF: ikutin TSOEnabled.
Matrix:
- **ON**: GTA V, AC series, RE Engine.
- **OFF**: Sleeping Dogs, Payday 2, PES series.

## HIDEHYPERVISORBIT / "Hide Hypervisor Bit"
WHAT: spoof CPUID flag — sembunyiin info kalau jalan di emulator. ON / OFF.
WHY: banyak game/DRM cek bit ini → kalau ke-detect = refuse to launch atau loop intro.
TRADE-OFF: ON = lebih kompatibel. OFF = aman buat game yg ga peduli (lebih cepet startup).
DEFAULT-IF: ON.
Matrix:
- **ON** wajib: AC series, GTA V, RE Engine, Konami games (PES, MGS), game dengan EA/Denuvo.
- **OFF**: indie / game tanpa DRM check.

## FEX_MULTIBLOCK
WHAT: JIT compile multi-block (mirip BOX64_BIGBLOCK).
WHY: lebih banyak instruksi per blok = optimize cross-branch.
TRADE-OFF: kompilasi awal lebih lama.
DEFAULT-IF: 1.

## FEX_SILENTLOG
WHAT: matikan log FEX.
WHY: log spam = waste RAM + I/O.
DEFAULT-IF: 1.

## FEX_CORE
WHAT: pilih backend JIT. Value: irjit (default), interpreter, host.
WHY: irjit = IR-based JIT, paling cepet. Interpreter buat debug. Host buat dev.
DEFAULT-IF: irjit. JANGAN ganti kecuali ada alasan kuat.

## ENV WAJIB GAMEHUB
- `WINEDEBUG=-all`
- `WINEESYNC=1` (atau `WINEFSYNC=1`, jangan dua-duanya)
- `FEX_TSOENABLED` sesuai engine game

## ❌ JANGAN PAKE DI GAMEHUB
- `BOX64_*` apapun — FEX engine berbeda, env var Box64 di-ignore.
