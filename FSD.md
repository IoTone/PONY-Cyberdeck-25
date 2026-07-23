# PONY Cyberdeck — Functional Specification Document (FSD)

**Project:** CASE-8 / PONY Cyberdeck
**Last updated:** 2026-07-23 (generated from GitHub issues; NixOS status from [nix4mtk](https://github.com/RazortoothRTC/nix4mtk))

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

The reference software platform is the NixOS port of the MediaTek Genio 720 BSP for the Globalscale Cortadodeck (CASE8), developed in [RazortoothRTC/nix4mtk](https://github.com/RazortoothRTC/nix4mtk). It is at **feature-complete alpha (`v25.05-alpha1`)** on NixOS 26.05, and several features below now build on top of a working device rather than a paper design.

| Capability | State | Evidence |
|---|---|---|
| Bit-identical vendor firmware (TF-A, U-Boot, kernel, fitImage, modules, boot fw) | ✅ Validated | Phase 1 complete; 8/8 artifacts hash-match the Yocto oracle |
| NixOS console + networking + SSH, hardened | ✅ Validated on HW | Serial login, DHCP/internet, 0 failed units |
| Display — DSI panel, KMS, Plymouth boot splash | ✅ Validated on HW | Picture and splash on the 7" panel |
| GPU — vendor mali_kbase + libmali, Weston 13 desktop with touch | ✅ Validated on HW | Power-on → desktop, no manual steps |
| Wi-Fi / Bluetooth (MT7961) | ✅ Validated on HW | `iw scan`, BT discovery |
| NPU — apusys + NeuroPilot 8.2.16 | ✅ Validated on HW | `neuronrt` ran MobileNet on the APU |
| Build & flash UX | ✅ One command | `nix build .#case8-720-flash --impure` → `aiot-flash -P result`; 2.93 GB image, rootfs auto-grows |
| Cameras, audio, WWAN/5G | 🔲 Remaining | Tier B remainder |
| Public CI port (Jenkins gates, S3 vendor bundle) | 🔨 In progress | Firmware gate proven 8/8 from the bundle; standup pending |

All three compute engines (CPU/GPU/NPU) are validated with no open architectural risks. See nix4mtk `STATUS.md` for the authoritative, current state.

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
| [#29](../../issues/29) | NixOS builds on public CI | 🔨 In progress | NixOS port lives in [nix4mtk](https://github.com/RazortoothRTC/nix4mtk): feature-complete alpha on Cortadodeck (Genio 720) — bit-identical vendor firmware, GPU-accelerated Weston desktop with touch, Wi-Fi/BT, and NPU inference all validated on hardware; NixOS 26.05, one-command build+flash. CI port itself is in progress (Jenkins gates for firmware bit-identity, config delta, and image builds; vendor bundle de-vendored to S3). Remaining: cameras, audio, WWAN, CI standup |
| [#25](../../issues/25) | Document NIRI build for Debian/Ubuntu | 🔨 In progress | Dependency list captured in issue |
| [#26](../../issues/26) | Document NIRI for Yocto or NixOS | 📋 Concept | Follows on from #25. Note: the NixOS target currently ships **Weston 13** (built against libmali) as its compositor — adopting NIRI there is not yet started |
| [#41](../../issues/41) | Android compatibility (Waydroid, Valve "Lepton" Android layer) | 📋 Concept | Run Android apps on the deck; options being collected |

### 3.3 UX, Display Modes & Device Configuration

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#9](../../issues/9) | PONY-UX Concept 1 — "hold the future in your hands" | 🔨 In progress | edex-ui and other options evaluated; assigned |
| [#33](../../issues/33) | Device modes & display configurations | 🔍 Investigating | Four configurations: true headless, LM-managed PTY tmux (AI mode), cyberdeck, cyberdeck LM-managed; pairs with #34 |
| [#35](../../issues/35) | Arduino/Pi-compatible avatar screen / indicators (OLED, round display) | 🔨 In progress | Recent commits reference this issue |
| [#11](../../issues/11) | XR/AR concept design (XReal/Rokid glasses, Linux XR mirroring) | 🔍 Investigating | Prior experimentation validated driver feasibility |

### 3.4 AI Capabilities

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#8](../../issues/8) | "AI" capability demo on MTK Genio (TTS, light LLM, voice/vision, assistant) | 🔨 In progress | NPU path is proven: NeuroPilot 8.2.16 + apusys running MobileNet inference on the APU on real hardware (nix4mtk Tier B3). Feature scoping (TTS, light LLM, voice/vision) still open; assigned |
| [#34](../../issues/34) | Voice & AI integration architecture (AI as interaction layer driving the WM) | 🔍 Investigating | Pairs with #33; voice-driven, on-device LM |
| [#39](../../issues/39) | Generative graphics use cases (Blender AI, [Burisu](https://github.com/IoTone/burisu)) | 📋 Concept | AI-assisted / generative visual tooling; ideas being gathered |

### 3.5 Connectivity & Communications

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#7](../../issues/7) | "OFFLINE" P2P mesh prototype (OLPC-mesh / Meshtastic inspired) | 🔨 In progress | Core differentiator; assigned |
| [#20](../../issues/20) | BLE audio use cases (device as audio pass-through source) | 📋 Concept | Needs elaboration with JCB |
| [#18](../../issues/18) | Passwordless login with NFC (FIDO2 / YubiKey-style) | 📋 Concept | Reference links collected |

### 3.6 Education (EDU SKU) Software & Content

| Issue | Feature | Status | Notes |
|---|---|---|---|
| [#10](../../issues/10) | EDU/STEM software installer & launcher (categories, filters) | 📋 Concept | Core EDU SKU experience |
| [#12](../../issues/12) | Programming tools / EDU AI content (tinylisp, Scratch, Processing, Pyret…) | 🔍 Investigating | Candidate package list being built |
| [#17](../../issues/17) | EDU integrations (e.g. Reachy Mini robot) | 📋 Concept | Idea collection |
| [#37](../../issues/37) | Robotics integrations for EDU SKU (Vector/Anki, Reachy, micro:bit robot) | 📋 Concept | Ideas being gathered; related to #17 |
| [#19](../../issues/19) | NFC gaming concepts for EDU SKU | 📋 Concept | — |
| [#21](../../issues/21) | "Music instrument" device use cases (MIDI-BLE, ORCA, DAWs) | 📋 Concept | Wishlist in progress; may combine with #20 |
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
| [#40](../../issues/40) | Gaming support (FEX-Emu x86 translation, emulators, Minecraft installer) | 📋 Concept | Spun out of the #3 closure — "minimum we should support some gaming"; options being examined |
| [#5](../../issues/5) | Arcade console emulator | 📋 Concept | ARM compatibility of Lutris/PlayOnLinux unverified |

## 4. Status Summary

| Status | Count |
|---|---|
| ✅ Done | 2 |
| ⛔ Closed (not pursued) | 2 |
| 🔨 In progress | 8 |
| 🔍 Investigating | 6 |
| 📋 Concept | 22 |

*(40 issues total: 36 open, 4 closed. Excludes PR #38.)*

Active fronts right now: the **EDU SKU** (#36 spec + BOM, with #35 avatar screen seeing recent commits), the **NixOS port** (#29, feature-complete alpha in [nix4mtk](https://github.com/RazortoothRTC/nix4mtk) with the CI port underway), the **AI mode architecture** pair (#33/#34), and the enclosure/ergonomics track (#13).

Recent movement: the three original prototype builds closed on 2026-07-23 — #1 (Orange Pi) as a completed concept test, while #2 and #3 (both Cortadobin) were closed as not pursued. The gamer prototype's closure (#3) redirected gaming into a lighter-weight software effort, #40. Four new concept issues opened: robotics for EDU (#37), generative graphics (#39), gaming support (#40), and Android compatibility (#41).

## 5. Source of Truth

This matrix is a snapshot generated from GitHub issues on 2026-07-23. The issues themselves remain the source of truth for detailed requirements; regenerate this document as issue states change.
