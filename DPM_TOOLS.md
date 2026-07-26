<div align="center">

# ⚙️ DPM Tools Guide
### X688B Goated TWRP + DPM

**Device:** Infinix Hot 11 Play X688B (MT6765) &nbsp;•&nbsp; **Recovery:** `goated_twrp.img`

</div>

---

## 📑 Table of Contents

- [Main Sections](#-main-sections)
- [⚠️ Important Rules](#️-important-rules)
- [Info / Backup](#-info--backup)
- [Create / Resize](#-create--resize)
- [Flash Partitions](#-flash-partitions)
- [Flash Boot / VBMeta](#-flash-boot--vbmeta)
- [Metadata / Full Super](#-metadata--full-super)
- [🧩 Vanilla GSI + Separate GApps](#-vanilla-gsi--separate-gapps-guide)
- [📲 GApps-Included GSI](#-gapps-included-gsi-guide)
- [🏭 Stock Restore](#-stock-restore-guide)
- [🩹 GSI Post-Flash Fixes](#-gsi--post-flash-fixes)
- [🚀 First Time Boot](#-first-time-boot)
- [💻 ADB Commands](#-useful-adb-commands)
- [🆘 Troubleshooting](#-troubleshooting)

---

## 📂 Main Sections

Open from the recovery home screen: **`DPM Tools`**

| # | Section |
|---|---|
| 1 | Info / Backup |
| 2 | Create / Resize |
| 3 | Flash Partitions |
| 4 | Flash Boot / VBMeta |
| 5 | Metadata / Full Super |
| 6 | GSI / Post-Flash Fixes |
| 7 | First Time Boot |

> 💡 RO2RW and DFE are built directly into **First Time Boot** — there's nothing external to flash for either one.

---

## ⚠️ Important Rules

- [x] Always backup Super Metadata before resizing or flashing logical partitions
- [x] Never flash the wrong image to the wrong partition
- [x] Vanilla GSI + separate GApps → resize `system` manually first, flash the GSI from normal TWRP Install Image — **do not** use DPM Flash Partitions
- [x] GApps-included GSI or no-GApps GSI → DPM Flash Partitions works directly
- [x] Restoring stock → flash `system`, `vendor`, `product`, `system_ext` to their correct targets
- [x] Full Super flash is dangerous — only if you know exactly what you're doing
- [x] Create/Resize only changes size/metadata, it does **not** create a filesystem — blank partitions may show "unknown filesystem" until flashed

---

## 📥 Info / Backup

**Options:** Partition Info · Validate Geometry · Raw Backups · Logical Backups

Backups save to: `/sdcard/backups/dpm/`

| Raw Backups | Logical Backups |
|---|---|
| Super Metadata | System |
| Boot | Vendor |
| Recovery | Product |
| vbmeta | System_ext |
| vbmeta_system | |
| vbmeta_vendor | |

> 📌 Copy important backups to your PC after making them.

---

## 📐 Create / Resize

**Options:** System / Vendor / Product / System_ext size in MB

- Partition exists → DPM **resizes** it
- Partition missing → DPM **creates** it
- Only changes size/metadata — does **not** create a filesystem
- Blank product/system_ext may show "unknown filesystem" until flashed

**Example — vanilla GSI + separate GApps:**

```
Product      = 1 MB
System_ext   = 1 MB
System       = 5120 MB (or as needed)
```

Keep some free super space if possible.

---

## ⚡ Flash Partitions

**Flow:** select target → select image → confirm flash

**Targets:** System Image · Vendor Image · Product Image · System_ext Image

DPM will: read image size → create partition if missing → resize if existing → flash → refresh mapper state.

**✅ Use for:** stock images, restoring stock, GApps-included GSI, no-GApps GSI

**❌ Don't use for:** vanilla GSI if you want separate GApps — it resizes system to the image size, leaving no room for GApps. Use TWRP Install Image after manually resizing.

---

## 🔧 Flash Boot / VBMeta

**Targets:** Boot · Recovery · vbmeta · vbmeta_system · vbmeta_vendor

Only flash correct, matching raw partition images.

---

## 💣 Metadata / Full Super

**Options:** Restore Metadata Raw · Flash Full Super Image

> ⚠️ **Danger:** Full Super flash can overwrite system/vendor/product/system_ext. Always backup Super Metadata first.

---

## 🧩 Vanilla GSI + Separate GApps Guide

Do **not** use DPM Flash Partitions here — it resizes system to the image size and leaves no room for GApps.

1. `DPM Tools → Create / Resize` → `Product = 1 MB`, `System_ext = 1 MB`, `System = 5120 MB` (or as needed)
2. Back to recovery home screen
3. `Install → Install Image → select GSI .img → System Image → Swipe`
4. `DPM Tools → GSI / Post-Flash Fixes → Boot Time + GApps + System Space Fix`
5. Flash GApps normally from TWRP Install
6. If needed: `DPM Tools → First Time Boot → DFE Patch`
7. Format Data if needed
8. Reboot System

---

## 📲 GApps-Included GSI Guide

1. `DPM Tools → Flash Partitions → System Image`
2. Select the GSI image → confirm flash
3. If needed: `RO2RW → Reboot Recovery → DFE Patch → Format Data`
4. Reboot System

---

## 🏭 Stock Restore Guide

```
DPM Tools → Flash Partitions → System Image      → system.img
DPM Tools → Flash Partitions → Vendor Image      → vendor.img
DPM Tools → Flash Partitions → Product Image     → product.img
DPM Tools → Flash Partitions → System_ext Image  → system_ext.img
```

Keeping your current vendor? Skip Vendor Image.

---

## 🩹 GSI / Post-Flash Fixes

**Option:** `Boot Time + GApps + System Space Fix`

- Runs `e2fsck` / unshare blocks / `resize2fs` on system
- Fixes system showing "full" after GSI flash
- Creates GApps config: `/sdcard/NikGapps/nikgapps.config` → `InstallPartition=/system_root/system`
- Comments out `resize2fs` lines in `rw-system.sh` to reduce PHH/TrebleDroid boot delay
- Disables stock recovery restore scripts

---

## 🚀 First Time Boot

**Order:** RO2RW / Rebuild Super → Reboot Recovery → DFE Patch → Format Data

- RO2RW can take time — run once, retry once if it fails
- Always reboot recovery after RO2RW, before DFE Patch
- DFE Patch removes encryption flags
- Format Data required if data was already encrypted
- Nothing external needed — both RO2RW and DFE are built in

---

## 💻 Useful ADB Commands

<details>
<summary>Click to expand</summary>

**DPM info:**
```sh
adb shell '/sbin/dpm_actions.sh info | head -100'
```

**Check encryption flags:**
```sh
adb shell 'grep -RIn "fileencryption|forceencrypt|forcefdeorfbe|metadata_encryption|inlinecrypt|wrappedkey|keydirectory|encryptable" /vendor/etc/fstab* /system_root/system/etc/fstab* /product/etc/fstab* /system_ext/etc/fstab* 2>/dev/null || echo "No encryption flags found"'
```

**Check recovery fstab:**
```sh
adb shell 'grep -nE " /metadata | /data |fileencryption|checkpoint" /etc/recovery.fstab /system/etc/recovery.fstab'
```
Expected: `/metadata → /dev/block/by-name/md_udc`, `/data → f2fs, no fileencryption/checkpoint flags`

**Check rw-system resize2fs patch:**
```sh
adb shell 'for f in /system_root/system/bin/rw-system.sh /system/bin/rw-system.sh /product/bin/rw-system.sh /system_ext/bin/rw-system.sh; do [ -f "$f" ] && echo "--- $f ---" && grep -n "resize2fs|DPMFIX boot-delay" "$f" | head -30; done'
```

</details>

---

## 🆘 Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| Recovery stuck on logo | Encrypted `/data` | `adb reboot bootloader` → `fastboot format:ext4 userdata` → `fastboot reboot recovery`, then re-run First Time Boot |
| DPM Tools not visible | External TWRP theme overriding UI | See command below |
| System doesn't boot after RO2RW | — | Reboot recovery once → DFE Patch → Format Data. Still stuck? `adb shell 'cat /sys/fs/pstore/*'` |
| Touchscreen not responding | — | Press power button once, off then on |
| Storage shows 0MB / encrypted | — | Run DFE Patch, then Format Data |
| Fastbootd not working | — | Make sure all 3 vbmeta files were flashed |

**Remove external theme:**
```sh
adb shell 'for f in /data/media/0/TWRP/theme/ui.zip /sdcard/TWRP/theme/ui.zip /TWRP/theme/ui.zip; do [ -f "$f" ] && mv "$f" "$f.bak_no_dpm"; done; killall recovery 2>/dev/null || true'
```
