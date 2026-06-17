# Per-game Tweak

Game spesifik yg punya quirk verified.

---

## GTA V
- Engine: RAGE.
- DX: 11.
- **Launcher**: pakai `PlayGTAV.exe` (BUKAN `GTA5.exe`).
  WHY: `GTA5.exe` jalan via Rockstar launcher overlay yg sering crash di Wine. PlayGTAV langsung skip launcher.
- VSync **WAJIB OFF** di in-game setting. WHY: bug lock 10fps di Wine kalau VSync ON.
- Box64: `BOX64_DYNAREC_BIGBLOCK=3`, `BOX64_DYNAREC_STRONGMEM=1`.
- FEX (GameHub): `FEX_TSOENABLED=ON`, `FEX_VECTORTSOENABLED=ON`, `HIDEHYPERVISORBIT=ON`.
- DXVK: `d3d11.relaxedBarriers=True`, `dxvk.enableAsync=True`.
- RAM management: streaming intensif. Pakai RamBooster interval 20-25 menit kalau RAM <12GB.
- Driver: Adreno 7XX+, Mali G610+. Mali G57 = struggle.

## RE4 Remake (Resident Evil 4 Remake, 2023)
- Engine: RE Engine.
- DX: **12 ONLY**.
- **WAJIB VKD3D-Proton**. JANGAN DXVK.
- `WINEDLLOVERRIDES="d3d12=n;dxgi=n"`
- `VKD3D_CONFIG=dxr11` wajib (matiin RT).
- FEX (GameHub): `FEX_TSOENABLED=ON`, `HIDEHYPERVISORBIT=ON`.
- Driver: minimal Adreno 740 (SD8 Gen 2/3) buat playable. Mali = no.
- Texture quality LOW. RT OFF. Shadow MEDIUM max.

## Silent Hill 2 & 3 (Classic, 2002-2003)
- DX: **8** (bukan DX9!).
- DXVK ga handle DX8 directly. **Wajib pake d3d8to9.dll wrapper** di folder game.
- `WINEDLLOVERRIDES="d3d8=n,d3d9=n"`.
- d3d8to9 source: github.com/crosire/d3d8to9 (releases).
- Setelah wrap, DXVK handle DX9-nya normal.
- VSync force in-game (game tua, 30/60 fps cap).

## Splinter Cell Blacklist
- Engine: Unreal Engine 2.5 (heavily modded).
- DX: 9 atau 11 (toggle in-game).
- **NoDynamicLights=False** wajib di config.
  WHY: True bikin black screen di Wine (dynamic lighting path bug).
- **EnableOcclusion=True** — counterintuitive, occlusion culling **lebih ringan** di setup ini.
- **FPS cap 60 WAJIB**. WHY: physics bug di >60fps (door clip, ragdoll crazy).
- `StartupGraphicsApi=0` buat force DX9.

## Payday 2
- Engine: Diesel Engine.
- DX: 9.
- FEX (GameHub): `FEX_TSOENABLED=OFF` (Diesel engine ga butuh TSO).
- dxvk.conf: **`d3d9.deferSurfaceCreation=False`** (True bikin glitch merah-cyan).
- RamBooster: `ZRAM_GUARD_LEVEL=85`, `PRE_CRISIS_LEVEL=0` atau 86.
  WHY: PD2 idle RAM ~80%, threshold harus jauh di atas idle.
- Box64: `BOX64_DYNAREC_BIGBLOCK=2`.

## Sleeping Dogs / Sleeping Dogs Definitive Edition
- Engine: bespoke (mirip Just Cause engine).
- DX: 11 (DE) / 9 (original).
- FEX (GameHub): `FEX_TSOENABLED=OFF`, `FEX_VECTORTSOENABLED=OFF`.
  WHY: rendering single-thread, TSO overhead percuma → off bikin ~15-25% FPS boost.
- Anti-aliasing: SMAA aman, MSAA crash.
- DXVK: `d3d11.relaxedBarriers=True`.
- Driver: Adreno 7XX OK, Mali G610+ OK, Mali G57 struggle.

## Tomb Raider 2013 (Crystal Dynamics)
- Engine: Crystal Engine.
- DX: 9 atau 11.
- **Force DX9** via registry Wine:
  `HKCU\Software\Crystal Dynamics\Tomb Raider\Graphics → RenderAPI = 9`
  WHY: DX11 path butuh feature yg DXVK mobile kadang miss. DX9 lebih stabil.
- FEX: `TSOENABLED=ON` (Crystal Engine TSO-dependent).
- Tressfx (hair physics): OFF di mobile.

## Halo CE (Combat Evolved)
- Engine: Blam!
- DX: 9.
- dxvk.conf: `d3d9.forceSamplerTypeSpecConstants=True`.
  WHY: tanpa ini crash di shader compilation di banyak Vulkan driver mobile.

## Assassin's Creed (any AnvilNext era)
- Engine: AnvilNext.
- DX: 11 (kebanyakan) / 12 (Valhalla, Mirage).
- FEX: `TSOENABLED=ON`, `VECTORTSOENABLED=ON`, `HIDEHYPERVISORBIT=ON` (anti-emu detection).
- Driver: AC Origins+ butuh Adreno 730+ atau Mali G610+.

## Cyberpunk 2077
- Engine: RED Engine.
- DX: 12.
- Mobile: super berat, ga praktis di bawah SD 8 Elite.
- VKD3D + DXR off + texture lowest.

## Hogwarts Legacy
- DX: 12 default, **ada DX11 launch flag** `-dx11`.
- Prefer DX11 di mobile (DXVK lebih stabil ketimbang VKD3D di game ini).

## Elden Ring / Dark Souls 3 / Sekiro
- FromSoft games. Physics bound.
- `dxvk.maxFrameRate = 60` WAJIB.
- ER kompatibel DXVK 1.10.3 lebih dari 2.x di banyak case.
- Anti-cheat Easy AC kadang trigger di Wine → boot offline mode.

---

## Pattern umum game lawas (pre-2010)
- DX 9 atau ke bawah.
- 32-bit binary → butuh `WINE_LARGE_ADDRESS_AWARE=1` + `BOX64_MMAP32=1`.
- VSync sering bug di Wine → force OFF in-game, cap via DXVK.
- DRM lawas (StarForce, SecuROM) → `BOX64_NOSIGSEGV=1`, `BOX64_NOSIGILL=1`.
