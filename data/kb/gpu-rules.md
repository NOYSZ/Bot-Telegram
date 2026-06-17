# GPU Rules (HARD)

## Driver per vendor — JANGAN KEBOLAK
- **Adreno (Snapdragon)** → **Turnip** driver + DXVK. BUKAN Vortek/VirGL/WineD3D.
- **Mali (MediaTek/Exynos)** → **Vortek** driver (atau VirGL/WineD3D/ExynosTools). BUKAN Turnip.
- **PowerVR (rare di Android)** → support tipis, mostly Vortek/WineD3D.

## DirectX translation layer — JANGAN KEBOLAK
- DX9 / DX10 / DX11 → **DXVK**.
- DX12 → **VKD3D-Proton**. BUKAN DXVK.
- DX8 (SH2/3 classic, era 2001-2003) → **d3d8to9 wrapper** → DXVK.
- OpenGL → tergantung; sebagian Wine OpenGL, sebagian via Zink (OGL → Vulkan).

---

## Mali Vulkan limitation (KENAPA SUSAH)
Mali GPU Vulkan TIDAK punya:
1. **BCn texture compression natively** — DXVK butuh BC1-BC7 (texture format kompres DX). Tanpa native, harus emulate via CPU (lambat) atau swap ke uncompressed (VRAM bengkak).
2. **gl_ClipDistance built-in** — DXVK pake ClipDistance buat clipping plane. Mali Vulkan skip ini → shader crash di `vkCreateShaderModule`.

Konsekuensi:
- Mali ga sanggup DX10/11/12 game high-end "out of the box".
- Workaround: pake **Vortek** driver (nambal SPIR-V buang ClipDistance + emulate BCn via CPU)
  ATAU pake fork DXVK khusus Mali (mis. **dxvk-sarek** di Winlator-Ludashi).
- Mali realistis cuma kuat **DX9 ke bawah** smooth. DX10+ = struggle.

## Exynos / Xclipse specifically
Sub-family Mali tapi punya quirk sendiri. Pake layer **ExynosTools** buat BCn virtualization.
Repo: `github.com/WearyConcern1165/ExynosTools`.

---

## GPU spoofing (DXVK)
Vendor ID:
- NVIDIA: `10de`
- AMD: `1002`
- Intel: `8086`

Beberapa game cek vendor → refuse to launch di "unknown vendor". Spoof = bypass.

PENTING: spoofing **CUMA ubah identitas yg dilaporkan**, BUKAN naikin performa fisik.

## Spoofing rekomendasi per tier mobile GPU:
- Helio G99 / Dim 6k-7k → RTX 2060 SUPER (vendor 10de, device 1f06)
- Dim 8020-8200 → RTX 3060 (10de, 2503)
- Dim 8300-8350 → RTX 3070 (10de, 2484)
- Dim 8400 Ultra → RTX 3080 (10de, 2206)
- SD 8 Elite → RTX 4080 (10de, 2704)
- Adreno 7XX flagship → RTX 3070-4070 series

Set di dxvk.conf:
```
dxgi.customVendorId = 10de
dxgi.customDeviceId = 2484
dxgi.customDeviceDesc = "NVIDIA GeForce RTX 3070"
```

## Hide GPU vendor real
Game kadang refuse Mali/Adreno. Sembunyiin:
```
dxgi.hideAmdGpu = True       # kalau spoof = AMD
dxgi.hideNvidiaGpu = False   # mau ke-detect sbg NVIDIA
```

## Common error patterns
- `vkCreateShaderModule failed` di Mali → BCn/ClipDistance issue. Switch ke Vortek/dxvk-sarek.
- `vkMapMemory -5` di Mali → coba `BOX64_MMAP32=0`.
- `VK_ERROR_OUT_OF_DEVICE_MEMORY` → turunin `d3d9.maxAvailableMemory` atau `dxvk.maxChunkSize`.
- Black screen intro DX9/11 → `deferSurfaceCreation=True` (kecuali Payday 2).
- Game refuse to launch karena cek GPU → spoof vendor.
