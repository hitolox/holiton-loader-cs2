# Holiton CS2

> Read-only kernel-level external for Counter-Strike 2 — packaged for one-click launch.

This repository contains a pre-built distribution of [Holiton](https://github.com/hitolox/holiton-cs2). Just download, unzip, run `holiton-loader.exe`. No build tools, no source code, no setup.

The loader downloads the antivirus-sensitive kernel components (`kdmapper.exe` + `driver_standalone.sys`) from the latest GitHub release on first run, so they never sit in this repo where Microsoft might flag and remove the whole project.

---

## ⚠️ READ THIS FIRST — Windows Defender will delete `kdmapper.exe`

This is **not optional**, **not a bug**, and **not something you can ignore**.

The kernel driver mapper (`kdmapper.exe`) is detected as `HackTool:Win32/Kdmapper` by Microsoft Defender and **every other antivirus**. This is by design — kdmapper exploits a vulnerable Intel-signed driver to map unsigned code into the kernel. There is no version that does not get flagged.

**Symptoms if you skip this section:**
- The loader shows: *"The following required files are missing: kdmapper.exe"* — even though you just unzipped the file.
- The file simply disappears from Explorer minutes after extraction.
- A Defender notification pops up about a quarantined "threat".

### Step 1 — Disable Defender real-time protection (or turn off your AV entirely)

1. **Win + I** → Privacy & security → Windows Security → Virus & threat protection
2. Click **Manage settings** under "Virus & threat protection settings"
3. Turn **Real-time protection** OFF
4. Turn **Tamper Protection** OFF
5. Turn **Cloud-delivered protection** OFF

If you use a third-party AV (Kaspersky, ESET, Avast, etc.), disable its real-time scanning the same way.

### Step 2 — Add a permanent exclusion

So Defender does not delete the files when re-enabled later. Open **PowerShell as Administrator** and run:

```powershell
Add-MpPreference -ExclusionPath "C:\Path\To\Your\Holiton\Folder"
```

Replace `C:\Path\To\Your\Holiton\Folder` with the **actual folder** where you extracted the files. Example:

```powershell
Add-MpPreference -ExclusionPath "C:\Users\YOU\Desktop\Holiton"
```

### Step 3 — Let the loader re-download if needed

If `kdmapper.exe` or `driver_standalone.sys` get quarantined later, just run `holiton-loader.exe` again — it will detect they're gone and offer to re-download them automatically. (This only works if Defender is now properly excluded; otherwise the file gets deleted again the moment it lands on disk.)

---

## Download

[**Download the latest release →**](https://github.com/hitolox/holiton-loader-cs2/releases/latest)

Or clone this repository:

```bash
git clone https://github.com/hitolox/holiton-loader-cs2.git
```

> ⚠️ Even cloning the repo through `git` will trigger Defender to delete `kdmapper.exe` immediately if real-time scanning is on. **Disable Defender before cloning.**

## Quick start

1. **Disable Defender** and add an exclusion (see warning above) — **mandatory**.
2. **Extract** the three shipped files into the **excluded folder**. They must stay together.
3. **Launch Counter-Strike 2** and wait until you reach the main menu.
4. **Run `holiton-loader.exe`** (double-click).
5. On the **first** run, the loader asks permission to download `kdmapper.exe` and `driver_standalone.sys` (~370 KB) from the GitHub release. Click **Yes**.
6. The loader then asks permission to map the kernel driver. Click **Yes** and accept the **UAC prompt** — administrator rights are required.
7. Once the driver is loaded, the controller starts automatically.
8. **In-game**, press `PAUSE` to open the Holiton overlay menu.

After the first launch, two folders appear next to the loader (`cached_schema/` and a generated `config.yaml`) — that is normal.

## What's inside

Shipped in this repo:

| File | Purpose |
|------|---------|
| `holiton-loader.exe` | Launcher — checks the environment, downloads kernel components on first run, starts the controller |
| `controller.exe` | The actual overlay / ESP / triggerbot |
| `driver_interface_kernel.dll` | Userspace bridge to the kernel driver |

Downloaded automatically on first run (from [the latest release](https://github.com/hitolox/holiton-loader-cs2/releases/latest)):

| File | Purpose |
|------|---------|
| `kdmapper.exe` | Maps the kernel driver into Windows kernel space |
| `driver_standalone.sys` | Read-only kernel driver |

**Do not delete or rename any of these files** once they are present. If `kdmapper.exe` or `driver_standalone.sys` go missing (e.g. antivirus quarantines them), the loader will offer to re-download on next launch.

## Features

- **Player ESP** — boxes (2D / 3D), skeletons, health bars, weapon, distance
- **Bomb info** — defuse timer, plant site, time-to-detonation
- **Spectator list** — see who is observing you
- **Trigger bot** — fires the moment an enemy enters your crosshair
- **Sniper crosshair**, **bomb label**, **anti-aim punish**, **grenade helper**
- **Stream-proof** by default — the overlay never appears in screen shares or recordings

## System requirements

| Requirement | Notes |
|-------------|-------|
| Windows 10 / 11 (x64) | Windows 7 / 8 not supported |
| Vulkan-capable GPU | Comes with any modern NVIDIA / AMD / Intel driver |
| Counter-Strike 2 | Vanilla / Steam build |
| Administrator rights | Only required to load the driver (UAC prompt on first run) |

The loader does **not** require Visual C++ Redistributable — the C runtime is statically linked.

## Other prerequisites — Windows kernel protections

Beyond Defender, the kernel driver also cannot be mapped if any of these are enabled:

- **Hypervisor-Enforced Code Integrity (HVCI / "Memory Integrity")** must be **OFF**
  Settings → Privacy & security → Windows Security → Device security → Core isolation → **Memory integrity** → Off, then reboot.
- **Microsoft Vulnerable Driver Blocklist** must be **OFF**
  Settings → Privacy & security → Windows Security → Device security → Core isolation → **Microsoft Vulnerable Driver Blocklist** → Off, then reboot.

If either is enabled, `kdmapper.exe` will exit with a non-zero code and the loader will surface the error.

## Troubleshooting

| Symptom | Likely cause |
|---------|--------------|
| `Could not download the missing components` | Internet is down, or Defender deleted the file mid-download. Disable Defender real-time scanning and add an exclusion before relaunching. |
| `kdmapper.exe exited with code 1` | HVCI or driver blocklist is enabled, or AV blocked the mapper at runtime |
| `Required files are missing: controller.exe / driver_interface_kernel.dll` | You moved files apart — keep all three shipped files in one folder |
| Loader closes silently after CS2 prompt | You clicked Cancel — relaunch and click OK |
| Overlay does not appear in CS2 | Press `PAUSE` to open the menu; works best in fullscreen / borderless |
| Controller starts but reports driver error | The driver was unloaded (e.g. after reboot). Run the loader again — it will re-map. |

## After a Windows reboot

The kernel driver is unloaded on every restart. Just run `holiton-loader.exe` again — it will detect the missing driver and re-map it automatically. (Defender exclusions persist across reboots; you only set them up once.)

## Source code

This repository hosts the **built artifacts only**. The full source code is at:

**[github.com/hitolox/holiton-cs2](https://github.com/hitolox/holiton-cs2)**

The loader source lives in [`loader/`](https://github.com/hitolox/holiton-cs2/tree/main/loader); the controller in [`controller/`](https://github.com/hitolox/holiton-cs2/tree/main/controller).

## Disclaimer

Holiton is an **educational project** that explores the Windows kernel and Vulkan overlay rendering with Rust. It is read-only — it never writes to the CS2 process — but Valve's Anti-Cheat policies still apply. Use at your own risk; the authors take no responsibility for VAC bans, account suspensions, or system instability.

## License

See [LICENSE](https://github.com/hitolox/holiton-cs2/blob/main/LICENSE) in the source repository.
