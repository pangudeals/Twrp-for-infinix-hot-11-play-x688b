<div align="center">

# 🔥 X688B Goated TWRP + DPM

**Custom TWRP recovery for the Infinix Hot 11 Play (X688B / MT6765)**
**with a full DPM (Dynamic Partition Manager) suite built right in.**

![Device](https://img.shields.io/badge/device-X688B-ff6b00?style=for-the-badge)
![Chipset](https://img.shields.io/badge/chipset-MT6765%20Helio%20G35-blue?style=for-the-badge)
![Android](https://img.shields.io/badge/android-11-3ddc84?style=for-the-badge)
![Status](https://img.shields.io/badge/status-stable-success?style=for-the-badge)

**Recovery image:** `goated_twrp.img`

</div>

---

## 📑 Table of Contents

- [What's Different in This Build](#-whats-different-in-this-build)
- [Disclaimer](#️-disclaimer)
- [Device Info](#-device-info)
- [What's Working](#-whats-working)
- [Files You Need](#-files-you-need)
- [Docs](#-docs)
- [Quick Flash](#-quick-flash)
- [Troubleshooting](#-troubleshooting)
- [Credits](#-credits)
- [License](#-license)

---

## 🆕 What's Different in This Build

RO2RW and DFE used to be separate zips you'd push over ADB and flash manually. **Not anymore.**

| Before | Now |
|---|---|
| Push zip over ADB, flash manually | Built into `DPM Tools → First Time Boot` |
| Two extra files to manage | Nothing extra needed |
| Easy to forget a step | One menu, one order |

DPM Tools also adds: partition backup, create/resize, logical partition flashing, GSI post-flash fixes, and stock/full-super restore — all from recovery.

> 💡 **GSI "not enough space" error?** Check [`DPM_TOOLS.md`](./DPM_TOOLS.md) → *Create / Resize*. Resize partitions there first — never flash directly into the raw super partition.

---

## ⚠️ Disclaimer

Not responsible for bricked devices or anything else that goes wrong. You're choosing to modify your device — **backup your data first**, this process wipes it.

Touchscreen sometimes stops responding after flashing — turning the display off and on with the power button fixes it.

**Tested on:** Infinix Hot 11 Play X688B, firmware V865
**Requirements:** unlocked bootloader, ADB + Fastboot on your PC

---

## 📱 Device Info

| | |
|---|---|
| **Device** | Infinix Hot 11 Play |
| **Codename** | X688B |
| **SoC** | MediaTek Helio G35 (MT6765) |
| **Android** | 11 |
| **Partitions** | Dynamic (Super) |
| **Encryption** | FBE v2 |

---

## ✅ What's Working

- [x] Touchscreen
- [x] Fastboot & Fastbootd
- [x] ADB sideload & shell
- [x] MTP / USB storage
- [x] Flashing zips & images
- [x] Backup / Restore (after DFE Patch)
- [x] DPM Tools (partition mgmt, GSI fixes)
- [x] Decryption disabled via DFE Patch, as intended

---

## 📦 Files You Need

```
goated_twrp.img
vbmeta_disabled.img
vbmeta_system_disabled.img
vbmeta_vendor_disabled.img
```

---

## 📄 Docs

| File | What's in it |
|---|---|
| [`RECOVERY_FLASH_INSTRUCTIONS.txt`](./RECOVERY_FLASH_INSTRUCTIONS.txt) | Flashing recovery + first boot setup |
| [`DPM_TOOLS.md`](./DPM_TOOLS.md) | Full DPM Tools reference |
| [`DPM_TOOLS_GUIDE.txt`](./DPM_TOOLS_GUIDE.txt) | Same, plain text |

---

## 🚀 Quick Flash

```bash
adb reboot bootloader
fastboot flash vbmeta vbmeta_disabled.img
fastboot flash vbmeta_system vbmeta_system_disabled.img
fastboot flash vbmeta_vendor vbmeta_vendor_disabled.img
fastboot flash recovery goated_twrp.img
fastboot reboot recovery
```

> ## 🚨 CRITICAL — First Time Boot (do not skip this)
>
> This is the step that actually makes the recovery usable. Skip it or do it out of order and you'll hit an encryption bootloop.
>
> **In recovery, run this exact order:**
>
> ```
> DPM Tools → First Time Boot → RO2RW → Reboot Recovery → DFE Patch → Format Data
> ```
>
> - Do **NOT** boot to system before this is fully done.
> - Do **NOT** reorder these steps.
> - Full details, timing notes, and what each step does: [`RECOVERY_FLASH_INSTRUCTIONS.txt`](./RECOVERY_FLASH_INSTRUCTIONS.txt)

Full steps and warnings: [`RECOVERY_FLASH_INSTRUCTIONS.txt`](./RECOVERY_FLASH_INSTRUCTIONS.txt)

---

## 🆘 Troubleshooting

<details>
<summary><b>Stuck at logo / bootloop</b></summary>

Usually means you booted to system before DFE Patch.

```bash
adb reboot bootloader
fastboot format:ext4 userdata
fastboot reboot recovery
```
Then run First Time Boot again, in order.
</details>

<details>
<summary><b>DPM Tools not showing up</b></summary>

An external TWRP theme is probably overriding the UI:

```bash
adb shell 'for f in /data/media/0/TWRP/theme/ui.zip /sdcard/TWRP/theme/ui.zip /TWRP/theme/ui.zip; do [ -f "$f" ] && mv "$f" "$f.bak_no_dpm"; done; killall recovery 2>/dev/null || true'
```
</details>

<details>
<summary><b>"No OS Installed" warning</b></summary>

Normal after formatting — ignore it if you've already flashed a ROM.
</details>

<details>
<summary><b>ADB not detecting device</b></summary>

Check drivers, try another cable/port, then:
```bash
adb kill-server && adb start-server
```
</details>

<details>
<summary><b>Fastbootd not working</b></summary>

Make sure all 3 vbmeta files got flashed.
</details>

<details>
<summary><b>Storage shows 0MB or encrypted</b></summary>

Run DFE Patch, then Format Data.
</details>

More in [`DPM_TOOLS.md`](./DPM_TOOLS.md).

---

## 🙏 Credits

- **TeamWin** — TWRP
- **topjohnwu** — Magisk
- Everyone whose original RO2RW/DFE work this build's DPM Tools are based on

---

## 📜 License

GPL v3, same as TWRP. Use it, modify it, fork it — just don't sell it and don't strip the license. See [LICENSE](LICENSE).
