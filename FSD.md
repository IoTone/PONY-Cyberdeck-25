# PONY Cyberdeck — Functional Specification Document (FSD)

**Project:** CASE-8 / PONY Cyberdeck
**Last updated:** 2026-08-14 (generated from GitHub issues; NixOS status from [nix4mtk](https://github.com/RazortoothRTC/nix4mtk))

## 1. Overview

CASE-8 PONY Cyberdeck is a futuristic portable computer for learning to code, working with AI, and communication. The user builds it, modifies it, and owns it. Core value propositions: portability, a stable NixOS-based platform, a privacy-focused AI-native design, mesh-networking communication features, and long-term support with no forced EOL cycle.

Hardware is based on Globalscale Technologies CASE-8 designs (MTK Genio class compute, e.g. Cortadodeck 720 / Cortadobin), with an Orange Pi Zero 3 first prototype. Planned SKUs include an Education (STEM) SKU and a Gamer SKU.

This document summarizes the functional scope of the project as tracked in GitHub issues, grouped by functional area, with current status.

**Status legend:**

| Status | Meaning |
|---|---|
| ✅ Done | Issue closed as completed / delivered |
| ⛔ Closed (not pursued) | Closed without delivering — decided against or superseded |
| 🔨 In progress | Active work, recent commits, or assignee driving it |
| 🔍 Investigating | Research / design exploration underway |
| 📋 Concept | Defined idea, not yet started (body is TODO/TBD or wishlist) |

## 2. Platform Baseline (validated on hardware)

The reference software platform is the NixOS port of the MediaTek Genio 720 BSP for the Globalscale Cortadodeck (CASE8), developed in [RazortoothRTC/nix4mtk](https://github.com/RazortoothRTC/nix4mtk). It reached **feature-complete alpha (`v25.05-alpha1`)** on NixOS 26.05, with essentially the whole Tier B feature set validated on real silicon.

> **⛔ Hardware status — the sole prototype board died 2026-08-08.** The `cortadodeck` board suffered an apparent power-input fault (burnt smell at the PSU input) while **idle**, hours after its last successful run — read as a hardware failure, not a workload kill. **No board is running today**, and replacement/RMA hardware is now the critical path for anything hardware-gated (WWAN/5G, the Diana AEC work). Crucially, **nothing but the hardware is lost**: all software is committed, and every ✅ below was proven on silicon *before* the loss — the table is a record of what was validated, not a claim about what is powered on now. There are no open architectural risks. Full post-mortem: `docs/incidents/2026-08-08-cortadodeck-loss.md` in nix4mtk.

| Capability | State | Evidence (validated on HW before 2026-08-08) |
|---|---|---|
| Bit-identical vendor firmware (TF-A, U-Boot, kernel, fitImage, modules, boot fw) | ✅ Validated | Phase 1 complete; 8/8 artifacts hash-match the Yocto oracle |
| NixOS console + networking + SSH, hardened | ✅ Validated on HW | Serial login, DHCP/internet, 0 failed units |
| Display — DSI panel, KMS, Plymouth boot splash | ✅ Validated on HW | Picture and splash on the 7" panel |
| GPU — vendor mali_kbase + libmali (GLES + Vulkan 1.3 ICD), touch | ✅ Validated on HW | `/dev/mali0`; kmscube spins; Mali-G57 Vulkan 1.3 |
| Desktop — **niri** (scrollable-tiling Wayland, ReGreet greeter, `count0` theme, login-time profile switcher) | ✅ Validated on HW | Graphical login → themed niri session on the panel (Weston retained for GPU diagnostics only) |
| Vulkan + WebGPU | ✅ Validated on HW | libmali is a Vulkan ICD; Chromium WebGPU pages render via the GLES compat adapter |
| Wi-Fi / Bluetooth (MT7961) | ✅ Validated on HW | `iw scan`, BT discovery |
| NPU — apusys + NeuroPilot 8.2.16 | ✅ Validated on HW | `neuronrt` ran MobileNet on the APU |
| Cameras (SENINF→CAMSV, imx258 + ov5640, `cam-capture`) | ✅ Validated on HW | Real photos decoded from both sensors |
| Audio — Tier B (shared playback + capture, realtime limits, ALSA-seq/MIDI) | ✅ Validated on HW | 2 concurrent opens with Diana live; `fluidsynth` as ALSA-seq client 128 |
| App packs (installer engine, meta-only catalog, tier 1/2, disk pre-flight) | ✅ Shipped | 9 packs incl. `embedded` (tier 2, PlatformIO/VSCodium) and `music-creator` |
| Diana — always-on voice assistant (llama.cpp, cross-built) | ✅ Validated on HW | ~45 live inference turns; cross build ≡ native store path (AEC barge-in still open) |
| Build & flash UX | ✅ One command | `nix build .#case8-720-flash --impure` → `aiot-flash -P result`; 2.93 GB image, rootfs auto-grows |
| CI port (Jenkins gates, de-vendored from S3) | ✅ Green end-to-end | Full pipeline SUCCESS: eval + firmware 8/8 + config-delta + image gates, built from the S3 bundle |
| WWAN / 5G | 🔲 Remaining | The last unproven Tier B peripheral; hardware-gated on a replacement board |

All three compute engines (CPU/GPU/NPU) are validated. The only true blocker is the absence of a board; see nix4mtk `STATUS.md` (on the `dev` branch, dated 2026-08-14) for the authoritative, current state.

## 3. Feature Matrix

### 3.1 Hardware Platform & Prototypes

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#1](../../issues/1) | PONY-opi-proto1 — first prototype build (Orange Pi Zero 3) | ✅ Done | Closed 2026-07-23 — concept testing complete; the Orange Pi version won't be maintained going forward |
| [#2](../../issues/2) | PONY-cbin-stem-proto1 — STEM/EDU prototype (Cortadobin, MTK Genio) | ⛔ Closed (not pursued) | Closed 2026-07-23 — a Cortadobin-based prototype will not be pursued |
| [#3](../../issues/3) | PONY-cbin-gamer-proto1 — gamer-focused prototype (Cortadobin) | ⛔ Closed (not pursued) | Closed 2026-07-23 — the design was an Android reference that doesn't match the current effort; minimal gaming support instead tracked in #40 |
| [#13](../../issues/13) | Hardware enclosure / ergonomics POC1 | 🔨 In progress | Multiple enclosure concepts prototyped (clamshell soft case, etc.); assigned |
| [#14](../../issues/14) | Power management design (PSU, battery, portability) | 📋 Concept | Write-up pending |
| [#16](../../issues/16) | Maker-friendly Arduino / Pi-compatible pin header proto | 📋 Concept | Direct hardware access for developers |
| [#36](../../issues/36) | Education SKU design + BOM (Cortadodeck 720, 5" MIPI-DSI, 1S LiPo) | 🔨 In progress | Spec drafted in `specs/EDU_SKU_DESIGN_AND_BOM.md`; recent commits |

### 3.2 Operating System & Build Infrastructure

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#28](../../issues/28) | Yocto builds | ✅ Done | Closed 2026-07-09 |
| [#29](../../issues/29) | NixOS builds on public CI | 🔨 In progress | NixOS port lives in [nix4mtk](https://github.com/RazortoothRTC/nix4mtk): feature-complete alpha on Cortadodeck (Genio 720) — bit-identical vendor firmware, GPU-accelerated Weston desktop with touch, Wi-Fi/BT, and NPU inference all validated on hardware; NixOS 26.05, one-command build+flash. **CI is now green end-to-end** — the full Jenkins pipeline (eval + firmware 8/8 + config-delta + image gates) passes, built from the de-vendored S3 bundle. Issue stays open pending formal closure / public exposure; platform remainder is cameras, audio, WWAN |
| [#25](../../issues/25) | Document NIRI build for Debian/Ubuntu | 🔨 In progress | Dependency list captured in issue |
| [#26](../../issues/26) | Document NIRI for Yocto or NixOS | 🔨 In progress | **niri is now the shipped desktop** on the nix4mtk NixOS image — graphical ReGreet login, `count0` theme, login-time profile switcher, validated on hardware (Weston is now retained only for GPU diagnostics). Configs also live in this repo (`ponypi/.config/niri`, via PR #38). Remaining for this issue: fold the Nix flake/config into this project + write up quirks |
| [#41](../../issues/41) | Android compatibility (Waydroid, Valve "Lepton" Android layer) | 📋 Concept | Run Android apps on the deck; options being collected |

### 3.3 UX, Display Modes & Device Configuration

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#9](../../issues/9) | PONY-UX Concept 1 — "hold the future in your hands" | 🔨 In progress | edex-ui and other options evaluated; a themed **niri** desktop (`count0` theme, custom ReGreet greeter) is now the platform default and HW-validated — a concrete realization of the concept. Test chime audio selected; assigned |
| [#33](../../issues/33) | Device modes & display configurations | 🔍 Investigating | Four configurations: true headless, LM-managed PTY tmux (AI mode), cyberdeck, cyberdeck LM-managed; pairs with #34 |
| [#35](../../issues/35) | Arduino/Pi-compatible avatar screen / indicators (OLED, round display) | 🔨 In progress | Recent commits reference this issue |
| [#11](../../issues/11) | XR/AR concept design (XReal/Rokid glasses, Linux XR mirroring) | 🔍 Investigating | Prior experimentation validated driver feasibility |

### 3.4 AI Capabilities

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#8](../../issues/8) | "AI" capability demo on MTK Genio (TTS, light LLM, voice/vision, assistant) | 🔨 In progress | Beyond the proven NPU path (NeuroPilot 8.2.16 + apusys, MobileNet on the APU), the **Diana** always-on voice assistant is validated on hardware (llama.cpp cross-build ≡ native store path; ~45 live inference turns) with a multi-tool router and vision via YuNet face detection + a camera-aim wrapper. Covers the "Diana AI" and voice/vision items in the issue. Open: full-duplex barge-in (AEC deferred — measured 545× speaker→mic coupling) and remaining scoping; assigned |
| [#34](../../issues/34) | Voice & AI integration architecture (AI as interaction layer driving the WM) | 🔨 In progress | Pairs with #33. The Diana stack (see #8) is a concrete instance: a voice-driven, on-device LM with a two-tier tool router that can take actions — the interaction-layer pattern this issue describes. Full-duplex (barge-in/AEC) not yet landed |
| [#39](../../issues/39) | Generative graphics use cases (Blender AI, [Burisu](https://github.com/IoTone/burisu)) | 📋 Concept | AI-assisted / generative visual tooling; ideas being gathered. Enabling substrate now exists on the platform: Vulkan 1.3 + Chromium WebGPU validated on hardware |

### 3.5 Connectivity & Communications

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#7](../../issues/7) | "OFFLINE" P2P mesh prototype (OLPC-mesh / Meshtastic inspired) | 🔨 In progress | Core differentiator; assigned |
| [#20](../../issues/20) | BLE audio use cases (device as audio pass-through source) | 📋 Concept | Needs elaboration with JCB |
| [#18](../../issues/18) | Passwordless login with NFC (FIDO2 / YubiKey-style) | 📋 Concept | Reference links collected |

### 3.6 Education (EDU SKU) Software & Content

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#10](../../issues/10) | EDU/STEM software installer & launcher (categories, filters) | 🔨 In progress | Working implementation in nix4mtk: an app-pack installer engine (`deck/packs/*`, meta-only catalog, tier 1/2, pre-flight disk check) with an "App Packs" touch UI. **9 packs shipped** — incl. `embedded` (tier 2, PlatformIO/VSCodium), `python-edu`, and `music-creator` — backed by a persistent self-hosted binary cache. Individual `nix profile install` on top of packs (nix-software-center) being scoped in the issue thread |
| [#12](../../issues/12) | Programming tools / EDU AI content (tinylisp, Scratch, Processing, Pyret…) | 🔍 Investigating | Candidate package list being built; first concrete landings via deck-packs — a **poplog** (Pop-11) port now building on aarch64 and a python-edu pack |
| [#17](../../issues/17) | EDU integrations (e.g. Reachy Mini robot) | 📋 Concept | Idea collection |
| [#37](../../issues/37) | Robotics integrations for EDU SKU (Vector/Anki, Reachy, micro:bit robot) | 📋 Concept | Ideas being gathered; related to #17 |
| [#19](../../issues/19) | NFC gaming concepts for EDU SKU | 📋 Concept | — |
| [#21](../../issues/21) | "Music instrument" device use cases (MIDI-BLE, ORCA, DAWs) | 🔨 In progress | First capability landed and HW-validated: the **`music-creator`** app pack over full Tier B audio — ALSA-seq/MIDI live (`fluidsynth` as seq client 128), `lilypond` engraving to PDF+MIDI rendered to audio. Wishlist (ORCA, DAWs, MIDI-BLE) still open; may combine with #20 |
| [#4](../../issues/4) | Pybricks / LEGO support | 🔍 Investigating | Assigned to Globalscale; references collected |
| [#6](../../issues/6) | "Home Assistant" / IoT software stack | 📋 Concept | TBD |

### 3.7 Security & Accessibility

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#23](../../issues/23) | Security features (e.g. ToothPaste secure copy-paste) | 📋 Concept | Candidate list started |
| [#27](../../issues/27) | Accessibility use cases for deaf users (phone↔deck text/voice relay) | 📋 Concept | Use cases sketched |

### 3.8 Documentation, Community & Program

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#15](../../issues/15) | One Cyberdeck Per Child (OCPC) digital-inclusion initiative | 📋 Concept | Vision statement drafted |
| [#22](../../issues/22) | Open hardware publication (OSHWLab or similar) | 📋 Concept | Beyond GitHub hosting |
| [#24](../../issues/24) | Sponsorship opportunities | 📋 Concept | — |
| [#30](../../issues/30) | Japanese documentation for NixOS | 📋 Concept | — |
| [#31](../../issues/31) | Japanese documentation for CASE8 / PONY deck | 📋 Concept | — |
| [#32](../../issues/32) | Naming / branding | 🔍 Investigating | Horse breeds vs. horse names; trademark risk noted |

### 3.9 Gaming & Emulation

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#40](../../issues/40) | Gaming support (FEX-Emu x86 translation, emulators, Minecraft installer) | 📋 Concept | Spun out of the #3 closure. Design taken in nix4mtk (`x86-compat-pack-plan.md`): an `x86compat` FEX-Emu app pack for x86_64/Windows **tools** (6 decisions recorded, nothing built yet, desktop-only validation). Key finding: **3D gaming is ruled out on the Mali-G57 driver** — `vulkaninfo` shows missing D3D10/11-mandated features; the blocker is the GPU driver, not the emulator |
| [#5](../../issues/5) | Arcade console emulator | 📋 Concept | ARM compatibility of Lutris/PlayOnLinux unverified |

## 4. Status Summary

| Status | Count |
|---|---|
| ✅ Done | 2 |
| ⛔ Closed (not pursued) | 2 |
| 🔨 In progress | 12 |
| 🔍 Investigating | 5 |
| 📋 Concept | 19 |

*(40 issues total: 36 open, 4 closed. Excludes PR #38.)*

**Headline:** the NixOS platform reached near-complete Tier B validation on hardware — then the **sole prototype board died on 2026-08-08** (see the §2 banner). No software was lost; the immediate critical path is replacement hardware, after which WWAN/5G and Diana's barge-in (AEC) are the remaining hardware-gated items.

Active fronts right now: the **on-device AI** (#8/#34 — the Diana assistant: local LLM + tool router + vision, ~45 live turns validated on hardware), the **EDU software stack** (#10 app-pack installer + touch UI with 9 packs shipped, #21 music-creator, #12 packs, #36 SKU spec + BOM with #35 avatar screen), the **NixOS port** (#29, feature-complete alpha in [nix4mtk](https://github.com/RazortoothRTC/nix4mtk) — CI green, cameras + audio + niri desktop + Vulkan/WebGPU all HW-validated), and the mesh/enclosure tracks (#7, #13).

Recent movement (upstream nix4mtk STATUS.md updated 2026-08-14 on the `dev` branch; no PONY-repo commits since the last refresh): before the board loss, a wave of Tier B features were validated on silicon — **niri** became the shipped desktop (#9/#26), **Vulkan 1.3 + WebGPU** came up (#39), **both cameras** (imx258 + ov5640) and **full Tier B audio** (ALSA-seq/MIDI) landed, the **Diana** voice assistant hit ~45 live inference turns (#8/#34), and the **app-pack** system shipped 9 packs (#10) — including **music-creator**, which lifts #21 off the concept list. Design work (not yet built) landed for an `x86compat` FEX-Emu pack, with the finding that **3D gaming is ruled out on the Mali-G57 driver** (#40). Correction to the prior refresh: Diana does **not** yet barge-in (AEC deferred). GitHub issue states are unchanged (36 open, 4 closed); the shifts above are status re-scores from work in progress.

## 5. Source of Truth

This matrix is a snapshot generated from GitHub issues on 2026-08-14, with platform state from nix4mtk `STATUS.md` (`dev` branch, dated 2026-08-14). The issues themselves remain the source of truth for detailed requirements; regenerate this document as issue states change.
