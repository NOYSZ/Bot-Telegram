# Box64 env vars (Winlator-type ONLY)

⚠ EMU SCOPE: Berlaku di **Winlator + semua fork** (brunodev85, coffincolors, Ludashi/StevenMXZ, Pipetto, REF4IK, Ajay, Cmod, Frost).
**TIDAK berlaku** di GameHub/BannerHub/Mobox (pake FEXCore — lihat `fex-translation.md`).

---

## BOX64_DYNAREC
WHAT: enable JIT (Dynamic Recompiler) Box64. 0=off (interpreter), 1=on.
WHY: tanpa JIT, x86 di-eksekusi instruksi-per-instruksi (10-30x lebih lambat). JIT compile blok jadi native ARM64.
TRADE-OFF: 0 cuma buat debug. Production: wajib 1.
DEFAULT-IF: 1.

## BOX64_DYNAREC_BIGBLOCK
WHAT: ukuran blok JIT. 0=Disabled, 1=Normal, 2=BigBlock, 3=VeryBigBlock.
WHY: blok lebih gede = JIT bisa optimize lintas branch, register allocation lebih bagus → speedup 5-15% di game CPU-bound (Unity, UE4/5).
TRADE-OFF: compile time first-load lebih lama (stuttering 2-5 detik di awal). Blok kegedean kadang trigger crash di game multi-thread.
DEFAULT-IF: 2 buat umum. 3 buat UE4/UE5/Unity heavy. 1 kalau ada crash random.

## BOX64_DYNAREC_STRONGMEM
WHAT: emulasi strong memory ordering x86 (TSO). 0=weak (ARM native), 1=strong.
WHY: x86 punya Total Store Ordering — store antar core selalu observable in-order. ARM weak ordering. Game multithread yg ngerely TSO crash/desync kalau ga di-emulate.
TRADE-OFF: 1 = aman tapi lambat (~10-20% slower karena tambah memory barrier). 0 = cepet tapi risk crash di game multithread.
DEFAULT-IF: 1 buat game AAA modern. 0 buat game tua single-thread.

## BOX64_DYNAREC_CALLRET
WHAT: optimasi CALL/RET via jump table.
WHY: tiap CALL+RET di x86 normalnya butuh 2 lookup di JIT cache. Jump table langsung jump → ~3-8% speedup.
TRADE-OFF: hampir ga ada. Aman.
DEFAULT-IF: 1.

## BOX64_DYNAREC_FASTNAN
WHAT: fast NaN handling SSE/AVX. 0=strict, 1=fast.
WHY: x86 punya rule presisi NaN propagation. ARM beda. Strict = emulate persis (slow), fast = skip = faster.
TRADE-OFF: 1 kadang bikin glitch visual di game yg rely ke NaN (rare).
DEFAULT-IF: 1.

## BOX64_DYNAREC_SAFEFLAGS
WHAT: keamanan emulasi flag x86 untuk instruksi RET.
WHY: tanpa ini, beberapa kombinasi flag setelah RET bisa salah di-track → crash random.
TRADE-OFF: dikit overhead.
DEFAULT-IF: 1.

## BOX64_DYNAREC_WEAKBARRIER
WHAT: pake memory barrier ringan (dmb ishld) bukan full barrier.
WHY: full barrier = stall pipeline. Weak barrier cukup buat banyak case.
TRADE-OFF: kalau STRONGMEM=1, weak barrier kadang ga cukup. Pair: STRONGMEM=1 + WEAKBARRIER=1 cocok buat banyak game; kalau crash → WEAKBARRIER=0.
DEFAULT-IF: 1.

## BOX64_DYNAREC_BLEEDING_EDGE
WHAT: optimasi konservatif khusus Unity.
WHY: Unity punya pattern memory access spesifik yg bisa di-opt.
TRADE-OFF: aman buat Unity, kadang merusak game non-Unity.
DEFAULT-IF: 1 buat Unity. 0 buat selainnya.

## BOX64_MMAP32
WHAT: batas address mapping memory 32-bit. 0=off, 1=on.
WHY: game 32-bit butuh alamat <4GB. Tanpa ini, mmap bisa ngasih alamat >4GB → crash.
TRADE-OFF: **BUKAN tentang vendor GPU**. Berlaku di Adreno & Mali. Game 64-bit ga butuh.
DEFAULT-IF: 1 buat game 32-bit; 0 buat 64-bit.
CATATAN: Mali kalau error `vkMapMemory -5` → coba MMAP32=0.

## BOX64_AVX
WHAT: ekspos kapabilitas AVX ke game. 0=off, 1=AVX, 2=AVX2/BMI2.
WHY: game modern (sejak ~2020) sering require AVX. Tanpa ekspos = game refuse to start.
TRADE-OFF: emulasi AVX di ARM ada overhead. AVX2 lebih berat.
DEFAULT-IF: 2 buat game baru, 1 buat 2015-2020, 0 buat lawas.

## BOX64_NOSIGSEGV
WHAT: abaikan Segmentation Fault signal.
WHY: beberapa game (anti-cheat lawas, copy protection) ngarep segfault sebagai legit signal. Tanpa skip = instant crash.
TRADE-OFF: bisa nutupin bug asli. Pake cuma kalau game spesifik butuh.
DEFAULT-IF: 0. Naikin ke 1 kalau game crash di intro tanpa error jelas.

## BOX64_NOSIGILL
WHAT: abaikan Illegal Instruction signal.
WHY: anti-tamper lawas (StarForce, SecuROM) sengaja inject INT3/UD2 → segfault. Skip = bypass.
TRADE-OFF: sama kayak NOSIGSEGV, bisa hide bug nyata.
DEFAULT-IF: 0. Naikin ke 1 buat game dengan DRM lawas yg crash di startup.

## BOX64_SSE_FLUSHTO0
WHAT: flush denormal SSE register ke nol.
WHY: denormal number bikin SSE jalan 20-100x lebih lambat di banyak CPU. Flush = perform optimization.
TRADE-OFF: dikit loss precision (irrelevant buat gaming).
DEFAULT-IF: 1.

## BOX64_DYNACACHE
WHAT: simpan hasil JIT ke disk antar sesi.
WHY: first launch JIT semua blok = stuttering. Cache di-load di run berikutnya = launch lebih cepet & smoother.
TRADE-OFF: makan storage 50-200MB per game.
DEFAULT-IF: 1.

## BOX64_PROFILE
WHAT: preset env var Box64. Value: default / compatibility / intermediate / performance.
WHY: shortcut buat ga set 30+ var manual.
TRADE-OFF: preset performance kadang trigger crash di game tertentu.
DEFAULT-IF: intermediate. Naik ke performance kalau stabil; turun ke compatibility kalau crash.

## ENV WAJIB SEMUA GAME (no exception)
- `WINEDEBUG=-all` — matikan log Wine, hemat RAM.
- `WINEESYNC=1` — event-based sync (eventfd), gantiin Wine sync default yg lambat.
- `BOX64_DYNAREC=1` — jelas.
- `BOX64_DYNACACHE=1` — cache JIT antar sesi.
