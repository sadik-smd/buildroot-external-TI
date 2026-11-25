# TI Buildroot External — Upstream Backlog & Status

> **Purpose:** Track what exists in `buildroot-external-TI` vs. what is already upstream in buildroot, and identify the upstreaming backlog for the TI maintainers call.
> **Repo:** https://github.com/TexasInstruments/buildroot-external-TI
> **External tree tag:** 12.01.00.05.03 | **Buildroot base:** 2026.05
> **Last updated:** 2026-11-16

---

## Table of Contents

1. [Repository Overview](#1-repository-overview)
2. [All Platforms in buildroot-external-TI](#2-all-platforms-in-buildroot-external-ti)
3. [Upstream Status Matrix](#3-upstream-status-matrix)
4. [Upstreaming Backlog](#4-upstreaming-backlog)
5. [Package Upstreaming Backlog](#5-package-upstreaming-backlog)
6. [Key Technical Differences: External vs Upstream](#6-key-technical-differences-external-vs-upstream)
7. [Discussion Topics](#7-discussion-topics)

---

## 1. Repository Overview

`buildroot-external-TI` is a `BR2_EXTERNAL` tree that extends upstream Buildroot with:
- TI-specific defconfigs using **TI SDK component versions** (ti-linux-kernel, ti-u-boot, TI TF-A fork)
- RT-Linux variants of all platforms
- TI-proprietary GPU packages (PowerVR Rogue)
- Board rootfs overlays (Weston, networking, SSH)
- Custom boot firmware integration (`ti-k3-boot-firmware`)

**Key distinction from upstream Buildroot configs:** upstream uses mainline/community component versions; this external tree uses the TI SDK (`ti-linux-kernel`, `ti-u-boot`, TI-fork of ATF).

### Component Versions (SDK 12.01.00.05.03 / 12.02.00.04)

| Component | External Tree Version | Upstream Buildroot Equivalent |
|---|---|---|
| Kernel | `ti-linux-kernel` 12.02.00.04 (6.18.x TI) | Mainline 6.18.x |
| U-Boot | `ti-u-boot` 12.02.00.04 | Mainline 2026.07 |
| ARM TF (K3) | ARM software/arm-trusted-firmware LTS 2.12 | Same (ARM upstream) |
| ARM TF (AM62L) | TexasInstruments/arm-trusted-firmware (TI fork) | Not in upstream |
| OP-TEE | Custom commit `f54190f` | Upstream optee-os |
| K3 Boot Firmware | `ti-k3-boot-firmware` 12.02.00.04 | v11.01.12 in upstream |

---

## 2. All Platforms in buildroot-external-TI

### 2.1 Defconfig Inventory (12 total)

| # | Defconfig | Platform | SoC | Arch | Kernel Type |
|---|---|---|---|---|---|
| 1 | `ti_release_am62x_sk_defconfig` | SK-AM62 / SK-AM62B / SK-AM62B-P1 | AM625 | aarch64 | Standard |
| 2 | `ti_release_am62x_sk_rt_defconfig` | SK-AM62 / SK-AM62B / SK-AM62B-P1 | AM625 | aarch64 | RT-Linux |
| 3 | `ti_release_am62x_sip_defconfig` | SK-AM62-SIP | AM6254ATL | aarch64 | Standard |
| 4 | `ti_release_am62x_sip_rt_defconfig` | SK-AM62-SIP | AM6254ATL | aarch64 | RT-Linux |
| 5 | `ti_release_am62p_sk_defconfig` | SK-AM62P-LP | AM62P5 | aarch64 | Standard |
| 6 | `ti_release_am62p_sk_rt_defconfig` | SK-AM62P-LP | AM62P5 | aarch64 | RT-Linux |
| 7 | `ti_release_am62lx_evm_defconfig` | AM62L-EVM (TMDS62LEVM) | AM62L3 | aarch64 | Standard |
| 8 | `ti_release_am62lx_evm_rt_defconfig` | AM62L-EVM | AM62L3 | aarch64 | RT-Linux |
| 9 | `ti_release_am64x_evm_defconfig` | AM64X-EVM | AM642 | aarch64 | Standard |
| 10 | `ti_release_am64x_evm_rt_defconfig` | AM64X-EVM | AM642 | aarch64 | RT-Linux |
| 11 | `ti_release_beaglebone_green_eco_defconfig` | BeagleBone Green Eco | AM335x | arm (Cortex-A8) | Standard |
| 12 | `ti_release_beaglebone_green_eco_rt_defconfig` | BeagleBone Green Eco | AM335x | arm (Cortex-A8) | RT-Linux |

### 2.2 Custom Packages (4 total)

| Package | Purpose | Source |
|---|---|---|
| `ti-img-rogue-driver` | PowerVR Rogue GPU kernel module | git.ti.com/graphics/ti-img-rogue-driver |
| `ti-img-rogue-umlibs` | PowerVR Rogue GPU userspace libs (EGL/GLESv2/GBM) | git.ti.com/graphics/ti-img-rogue-umlibs |
| `ti-img-rogue-mesa` | Mesa gallium "pvr" driver shim for Rogue GPU | git.ti.com/graphics/ti-img-rogue-mesa |
| `ti-lvgl-demo` | LVGL graphics demo app (CMake, v11.00.05.02) | github.com/TexasInstruments/ti-lvgl-demo |

### 2.3 Board Support Directories

```
board/ti/
├── am62x-sk/           rootfs-overlay, post-build-ti.sh (Weston, networking, GPU)
├── am62p-sk/           rootfs-overlay, post-build-ti.sh
├── am62lx-evm/         rootfs-overlay, post-build-ti.sh, genimage_ti.cfg, uEnv.txt
├── am64x-sk/           rootfs-overlay, post-build-ti.sh, genimage.cfg
├── beaglebone-green-eco/  rootfs-overlay, post-build.sh, extlinux.conf, uEnv.txt
└── common/
    ├── am6xx/          genimage_ti.cfg (shared K3 SD card layout)
    ├── am33xx/         genimage_ti.cfg (AM335x SD card layout)
    └── patches/        hash files for ATF, linux, optee-os, uboot,
                        ti-k3-boot-firmware, ti-k3-r5-loader
```

---

## 3. Upstream Status Matrix

### 3.1 Defconfigs

| External Defconfig | Upstream Equivalent | Status | Gap |
|---|---|---|---|
| `ti_release_am62x_sk_defconfig` | `ti_am62x_sk_defconfig` | **Partial** — upstream uses mainline components | Mainline vs TI SDK versions; no GPU, no Weston overlay |
| `ti_release_am62x_sk_rt_defconfig` | _(none)_ | **Not upstream** | RT variant missing entirely |
| `ti_release_am62x_sip_defconfig` | _(none)_ | **Not upstream** | AM62X SIP (AM6254ATL) not represented at all |
| `ti_release_am62x_sip_rt_defconfig` | _(none)_ | **Not upstream** | — |
| `ti_release_am62p_sk_defconfig` | `ti_am62px_sk_defconfig` | **Partial** — upstream uses mainline, U-Boot 2024.07 lag | No GPU, no Weston, outdated U-Boot |
| `ti_release_am62p_sk_rt_defconfig` | _(none)_ | **Not upstream** | RT variant missing |
| `ti_release_am62lx_evm_defconfig` | _(none)_ | **Not upstream** | Entirely new platform, no upstream coverage |
| `ti_release_am62lx_evm_rt_defconfig` | _(none)_ | **Not upstream** | — |
| `ti_release_am64x_evm_defconfig` | `ti_am64x_sk_defconfig` | **Partial** — EVM vs SK board, mainline components, U-Boot 2024.07 | Different board (EVM≠SK), no GPU |
| `ti_release_am64x_evm_rt_defconfig` | _(none)_ | **Not upstream** | RT variant missing |
| `ti_release_beaglebone_green_eco_defconfig` | `beaglebone_defconfig` (closest) | **Partial** — different board variant (Green Eco specific DTs) | No Green Eco DT selection, no RT |
| `ti_release_beaglebone_green_eco_rt_defconfig` | _(none)_ | **Not upstream** | RT variant missing |

**Summary:**
- 0 of 12 external defconfigs have full upstream parity
- ~5 have partial coverage (different component versions / missing features)
- 7 have no upstream equivalent at all

### 3.2 Packages

| Package | In Upstream Buildroot | Status |
|---|---|---|
| `ti-img-rogue-driver` | No | **Not upstream** |
| `ti-img-rogue-umlibs` | No | **Not upstream** |
| `ti-img-rogue-mesa` | No | **Not upstream** |
| `ti-lvgl-demo` | No | **Not upstream** |

---

## 4. Upstreaming Backlog

Prioritized list of work needed to get external-TI coverage into upstream Buildroot.

---

### BACKLOG ITEM 1 — AM62L EVM Platform Support
**Priority: High | Effort: High**

| Field | Detail |
|---|---|
| **Defconfigs** | `ti_release_am62lx_evm_defconfig`, `ti_release_am62lx_evm_rt_defconfig` |
| **Board** | AM62L-EVM (TMDS62LEVM) |
| **SoC** | AM62L3 (K3-based, low-power variant) |
| **Status** | No upstream coverage at all |
| **Blockers** | Uses TI-fork of ARM TF (`TexasInstruments/arm-trusted-firmware`), not upstream ARM TF — needs either mainline ATF support or a new package |
| **Unique aspects** | Uses `BR2_TARGET_TI_K3_BOOT_FIRMWARE` (bundled firmware) instead of separate R5 loader; OP-TEE platform `k3-am62lx`; different ATF memory layout (`BL32_BASE`, `PRELOADED_BL33_BASE`); custom `genimage_ti.cfg` |
| **What needs to happen** | 1. Add `board/ti/am62lx-evm/` to upstream buildroot<br>2. Add `ti_am62lx_evm_defconfig` using mainline ATF/u-boot<br>3. Verify `k3-am62lx` OP-TEE platform is in upstream optee-os<br>4. Ensure `ti-k3-boot-firmware` package supports AM62L firmware blobs |

---

### BACKLOG ITEM 2 — AM62X SIP (System-in-Package) Platform
**Priority: High | Effort: Medium**

| Field | Detail |
|---|---|
| **Defconfigs** | `ti_release_am62x_sip_defconfig`, `ti_release_am62x_sip_rt_defconfig` |
| **Board** | SK-AM62-SIP |
| **SoC** | AM6254ATL (AM62X in SIP package) |
| **Status** | No upstream coverage |
| **Blockers** | Requires separate u-boot defconfigs (`am6254atl_evm_a53`, `am6254atl_evm_r5`), different ATF memory parameters |
| **Unique aspects** | Uses `am6254atl_evm` board configs for U-Boot and R5 loader; different ATF/BL32 memory addresses (`BL32_BASE=0x9e800000`, `PRELOADED_BL33_BASE=0x80080000`); LVGL demo disabled |
| **What needs to happen** | 1. Add `board/ti/am62x-sip/` to upstream buildroot<br>2. Add `ti_am62x_sip_defconfig` to upstream configs<br>3. Verify `am6254atl_evm` U-Boot defconfig is in mainline U-Boot<br>4. Document SIP-specific memory constraints |

---

### BACKLOG ITEM 3 — RT-Linux Variants for All Existing Upstream Configs
**Priority: Medium | Effort: Low-Medium per config**

RT variants exist in external-TI for all platforms but are absent from upstream.

| Config Needed in Upstream | Based On |
|---|---|
| `ti_am62x_sk_rt_defconfig` | `ti_am62x_sk_defconfig` + `ti_rt.config` fragment |
| `ti_am62px_sk_rt_defconfig` | `ti_am62px_sk_defconfig` + `ti_rt.config` fragment |
| `ti_am64x_sk_rt_defconfig` (or evm) | `ti_am64x_sk_defconfig` + `ti_rt.config` fragment |

The `ti_rt.config` kernel config fragment is the main addition for RT. The question is whether to:
- Use upstream PREEMPT_RT kernel patches/config
- Mirror the TI SDK RT kernel approach
- Add a note in defconfig pointing to RT fragment

---

### BACKLOG ITEM 4 — BeagleBone Green Eco Defconfig
**Priority: Low-Medium | Effort: Low**

| Field | Detail |
|---|---|
| **Defconfigs** | `ti_release_beaglebone_green_eco_defconfig`, `ti_release_beaglebone_green_eco_rt_defconfig` |
| **Board** | BeagleBone Green Eco |
| **SoC** | AM335x (Cortex-A8, 32-bit ARMv7) |
| **Status** | Partial — `beaglebone_defconfig` upstream covers BeagleBone family but not Green Eco specific DTs |
| **Unique aspects** | Adds `am335x-bonegreen-eco` DT; 512 MB ext4 rootfs (vs ~ext2 upstream); `uEnv.txt` based boot; `extlinux.conf`; RT variant |
| **What needs to happen** | 1. Add `am335x-bonegreen-eco` DT selection to `beaglebone_defconfig` or create `beaglebone_green_eco_defconfig`<br>2. Add RT variant<br>3. Coordinate with BeagleBoard.org community |

---

### BACKLOG ITEM 5 — Update Upstream AM64x and AM62Ax/AM62Px to Current Component Versions
**Priority: High | Effort: Medium**

The existing upstream configs for these boards lag behind:

| Config | Current Upstream U-Boot | External Tree U-Boot | Delta |
|---|---|---|---|
| `ti_am64x_sk_defconfig` | 2024.07 | SDK 12.02.00.04 | ~2 years |
| `ti_am62ax_sk_defconfig` | 2024.07 | SDK 12.02.00.04 | ~2 years |
| `ti_am62px_sk_defconfig` | 2024.07 | SDK 12.02.00.04 | ~2 years |

Same lag for ATF (v2.11 vs LTS 2.12) and kernel (6.10 vs 6.12/6.18).

**What needs to happen:**
1. Bump U-Boot to current mainline (2026.07 or later)
2. Bump ATF to LTS 2.12
3. Bump kernel to 6.12.x or 6.18.x with correct DTS
4. Verify R5 loader version compatibility

---

### BACKLOG ITEM 6 — Align AM64X EVM vs AM64X SK in Upstream
**Priority: Medium | Effort: Low**

| Issue | Detail |
|---|---|
| External tree targets | **AM64X-EVM** (AM642 EVM, `k3-am642-evm` DT) |
| Upstream targets | **AM64X-SK** (SK-AM64, `k3-am642-sk` DT) |
| Impact | Different board variants; EVM users cannot use upstream defconfig directly |
| Resolution | Either add separate `ti_am64x_evm_defconfig` upstream, or document the DT change needed |

---

## 5. Package Upstreaming Backlog

### 5.1 TI IMG Rogue GPU Stack

The PowerVR Rogue GPU stack consists of three linked packages that are TI-proprietary/vendor packages:

```
ti-img-rogue-driver  (kernel module)
        ↓ depends on
ti-img-rogue-umlibs  (EGL/GLESv2/GBM userspace)
        ↓ depends on
ti-img-rogue-mesa    (Mesa gallium "pvr" driver shim)
```

| Package | Upstreamable? | Concern |
|---|---|---|
| `ti-img-rogue-driver` | Possibly | License is MIT/GPLv2 dual; source on git.ti.com; buildroot community may accept if properly maintained |
| `ti-img-rogue-umlibs` | Possibly | Same license; binary blobs may be an issue |
| `ti-img-rogue-mesa` | Possibly | Provides EGL/GBM/GLES via Mesa shim; needs to coexist with existing mesa3d package |

**Upstreaming challenges:**
- Packages currently point to TI-internal git repos (`git.ti.com`); buildroot prefers packages with upstream public releases
- The GPU stack is tightly version-locked to specific kernel versions (currently locked to `linuxws/scarthgap/k6.12/25.2.6850647`)
- Mesa shim (`ti-img-rogue-mesa`) provides virtual packages `libegl`, `libgbm`, `libgles` — conflicts with upstream `mesa3d`
- aarch64-only restriction may be acceptable (similar to other platform-specific packages in buildroot)

**Recommendation:** These GPU packages are strong upstream candidates if:
1. TI publishes tagged releases on a public git forge (GitHub/GitLab)
2. Version locking to specific kernel branches is relaxed
3. The virtual package conflict with `mesa3d` is resolved (e.g., mutual exclusivity)

### 5.2 ti-lvgl-demo

| Field | Detail |
|---|---|
| **Source** | github.com/TexasInstruments/ti-lvgl-demo (public GitHub) |
| **License** | MIT |
| **Build** | CMake, depends on libdrm, mosquitto, alsa-lib, SDL2, SDL2_image |
| **Upstreamable?** | **Yes, strong candidate** — public GitHub, MIT license, CMake build, standard dependencies |
| **Concern** | Demo applications are sometimes declined by buildroot upstream as too hardware-specific; must demonstrate general utility |

---

## 6. Key Technical Differences: External vs Upstream

### 6.1 Component Source URLs

| Component | External Tree | Upstream Buildroot |
|---|---|---|
| Kernel | `git.ti.com/ti-linux-kernel/ti-linux-kernel` | kernel.org |
| U-Boot | `git.ti.com/ti-u-boot/ti-u-boot` | denx.de / github.com/u-boot |
| ATF (AM62x/AM64x) | `github.com/ARM-software/arm-trusted-firmware` | Same |
| ATF (AM62L) | `github.com/TexasInstruments/arm-trusted-firmware` | Not present |
| OP-TEE | Pinned commit `f54190f` | Tagged upstream release |
| Boot firmware | `ti-k3-boot-firmware` (TI binary blobs) | v11.01.12 in upstream |

### 6.2 Rootfs Overlays (not in upstream)

The external tree adds per-board rootfs overlays that upstream configs lack:

| Overlay | Contents | Boards |
|---|---|---|
| `etc/default/dropbear` | SSH config (empty password with `-B`) | All |
| `etc/network/interfaces` | DHCP on eth0 | All |
| `etc/init.d/S99weston` | Auto-start Weston compositor | K3 boards |
| `etc/profile.d/set_xdg_runtime_dir.sh` | XDG runtime dir setup | K3 boards |
| `etc/xdg/weston/` | Weston compositor config | K3 boards |

**These overlays are needed for a working desktop experience** — upstream configs produce minimal console-only systems.

### 6.3 Boot Firmware Approach

| Approach | External Tree | Upstream |
|---|---|---|
| K3 (AM62x/AM64x) | `BR2_TARGET_TI_K3_R5_LOADER` — builds R5 SPL from source | Same |
| K3 (AM62L) | `BR2_TARGET_TI_K3_BOOT_FIRMWARE` — uses pre-built TI binary firmware | Upstream supports `ti-k3-boot-firmware` package (v11.01.12) |
| Firmware version | 12.02.00.04 | 11.01.12 (older) |

### 6.4 GPU Support

Upstream Buildroot configs for TI have no GPU support. The external tree adds full GPU stack for AM62x:
- `BR2_PACKAGE_TI_IMG_ROGUE_DRIVER=y`
- `BR2_PACKAGE_TI_IMG_ROGUE_UMLIBS=y`
- `BR2_PACKAGE_TI_IMG_ROGUE_MESA=y`
- `BR2_PACKAGE_GLMARK2=y`
- `BR2_PACKAGE_KMSCUBE=y`
- `BR2_PACKAGE_VULKAN_TOOLS=y`

This is a significant feature gap for the AM62x SK defconfig in upstream.

### 6.5 SD Card Layout

| Partition | External Tree | Upstream |
|---|---|---|
| Boot (FAT) | 64 MB | 64 MB (similar) |
| Rootfs | 256–512 MB ext4 | 120–256 MB ext2 |
| Filesystem type | **ext4** | **ext2** |

The external tree uses ext4 for rootfs; upstream uses ext2. This is a minor difference but affects journaling/reliability.

---

## 7. Discussion Topics

### 7.1 Upstreaming Priority — Which Platforms First?

| Platform | Business Priority | Technical Complexity | Recommendation |
|---|---|---|---|
| AM62L EVM | High (new platform) | High (TI ATF fork) | Start planning; needs ATF upstream work first |
| AM62X SIP | Medium | Medium (memory layout changes) | Queue after AM62L |
| RT variants | High (customer ask) | Low (kernel fragment only) | Quick win — add to existing configs |
| BeagleBone Green Eco | Low | Low | Low hanging fruit |
| AM64x EVM vs SK alignment | Medium | Low | Clarify upstream strategy |

### 7.2 GPU Package Upstreaming Decision

Need a decision from the group:
- **Option A: Upstream the Rogue GPU packages** — requires public releases on GitHub/GitLab, buildroot community review, resolving mesa3d conflict
- **Option B: Keep GPU in external tree only** — simpler, but users need external tree for GPU; upstream config remains minimal
- **Option C: Upstream a minimal GPU config marker** — add GPU=y option to upstream defconfigs but resolve packages via BR2_EXTERNAL

### 7.3 TI SDK vs Mainline Component Versions

The fundamental tension:
- Upstream buildroot uses mainline U-Boot/kernel — better for community but may lack TI-specific features
- External tree uses TI SDK versions — matches TI Processor SDK releases but diverges from upstream

**Question:** For the upstream configs, should we periodically sync to TI-recommended versions, or stay on mainline and document the trade-off?

**Current state:**
- `ti_am62x_sk_defconfig` upstream: kernel 6.12.95, U-Boot 2026.07 — **reasonably current**
- `ti_am64x_sk_defconfig` upstream: kernel 6.10, U-Boot 2024.07 — **~2 years stale**

### 7.4 RT-Linux Strategy

RT variants are in high demand for industrial customers using AM62x/AM64x. Options:
- **Upstream RT defconfigs** using mainline `PREEMPT_RT` (available as of kernel 6.12)
- **Document** how to enable RT from existing upstream defconfigs
- **Keep RT in external tree** only

Given that PREEMPT_RT is now mainline in 6.12+, this is a good time to add upstream RT defconfigs.

### 7.5 Recent Activity in External Tree (last 20 commits)

```
d42e880  weston: add render mode for weston
43637cd  Merge PR #53
36f0ae1  defconfig: add am335 rt variant and bump version    ← BeagleBone Eco RT added
1d39b41  Merge PR #52
61410bf  configs: update defconfigs and hashes
d386d0c  Merge PR #51
c2e1757  platform: add support for am335 (beaglebone green eco)  ← New platform added
1f1b880  Merge PR #50 (gpu-oot-rogue-mesa)
b704a3e  updated changes for 12.01
d934384  Update Rogue GPU package hashes and versions (6.18 kernel)
99b8f82  Add TI PowerVR Rogue GPU packages (driver, umlibs, mesa)  ← GPU stack added
```

**Notable:** GPU support and BeagleBone Green Eco are very recent additions — not yet mature enough for upstream proposals.

### 7.6 Maintainer Coverage

| Platform | Identified Maintainer | Upstreamed Config? |
|---|---|---|
| AM62x SK | TI (sadik-smd) | Yes (`ti_am62x_sk_defconfig`) |
| AM62P SK | TI | Yes (`ti_am62px_sk_defconfig`) |
| AM64x SK/EVM | TI | Partial (`ti_am64x_sk_defconfig`) |
| AM62L EVM | TI (vinuchandran-av, sadik-smd) | No |
| AM62X SIP | TI | No |
| BeagleBone Green Eco | TI (sadik-smd) | No |
| Rogue GPU packages | TI (ssurt2, sadik-smd) | No |

---

## Appendix A: Build Commands (External Tree)

```bash
# Clone both repos side by side
git clone -b 2026.05 https://github.com/buildroot/buildroot
git clone -b 12.01.00.05.03 https://github.com/TexasInstruments/buildroot-external-TI.git

cd buildroot/

# AM62X SK
make BR2_EXTERNAL=../buildroot-external-TI ti_release_am62x_sk_defconfig && make

# AM62X SIP
make BR2_EXTERNAL=../buildroot-external-TI ti_release_am62x_sip_defconfig && make

# AM62P SK
make BR2_EXTERNAL=../buildroot-external-TI ti_release_am62p_sk_defconfig && make

# AM62L EVM
make BR2_EXTERNAL=../buildroot-external-TI ti_release_am62lx_evm_defconfig && make

# AM64X EVM
make BR2_EXTERNAL=../buildroot-external-TI ti_release_am64x_evm_defconfig && make

# BeagleBone Green Eco
make BR2_EXTERNAL=../buildroot-external-TI ti_release_beaglebone_green_eco_defconfig && make

# RT variants (append _rt_ to any of the above)
make BR2_EXTERNAL=../buildroot-external-TI ti_release_am62x_sk_rt_defconfig && make

# Flash
sudo dd if=output/images/sdcard.img of=/dev/sdX
```

## Appendix B: TI Processor SDK Links

| Platform | SDK Link |
|---|---|
| AM62X | https://www.ti.com/tool/PROCESSOR-SDK-AM62X |
| AM62P | https://www.ti.com/tool/PROCESSOR-SDK-AM62P |
| AM62L | https://www.ti.com/tool/AM62L-PROCESSOR-SDK |
| AM64X | https://www.ti.com/tool/PROCESSOR-SDK-AM64X |
