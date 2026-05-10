# Holiton CS2

> Read-only kernel-level external for Counter-Strike 2 — packaged for one-click launch.

This repository contains a pre-built distribution of [Holiton](https://github.com/hitolox/holiton-cs2). Just download, unzip, run `holiton-loader.exe`. No build tools, no source code, no setup.

---

## Download

[**Download the latest release →**](https://github.com/hitolox/holiton-loader-cs2/releases/latest)

Or clone this repository:

```bash
git clone https://github.com/hitolox/holiton-loader-cs2.git
```

## Quick start

1. **Extract** all files into the **same folder** — they must stay together.
2. **Launch Counter-Strike 2** and wait until you reach the main menu.
3. **Run `holiton-loader.exe`** (double-click).
4. On the first run, the loader will ask permission to map the kernel driver. Click **Yes** and accept the **UAC prompt** — administrator rights are required to load the driver.
5. Once the driver is loaded, the controller starts automatically.
6. **In-game**, press `PAUSE` to open the Holiton overlay menu.

After the first launch, two folders appear next to the loader (`cached_schema/` and a generated `config.yaml`) — that is normal.

## What's inside

| File | Purpose |
|------|---------|
| `holiton-loader.exe` | Launcher — checks the environment and starts the controller |
| `controller.exe` | The actual overlay / ESP / triggerbot |
| `driver_interface_kernel.dll` | Userspace bridge to the kernel driver |
| `driver_standalone.sys` | Read-only kernel driver |
| `kdmapper.exe` | Maps the kernel driver into Windows kernel space |

**Do not delete or rename any of these files.** The loader expects all five to be in the same directory.

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
| Windows 10 / 11 (x64) | Windows 7/8 not supported |
| Vulkan-capable GPU | Comes with any modern NVIDIA / AMD / Intel driver |
| Counter-Strike 2 | Vanilla / Steam build |
| Administrator rights | Only required to load the driver (UAC prompt on first run) |

The loader does **not** require Visual C++ Redistributable — the C runtime is statically linked.

## Important — before you launch

The kernel driver cannot be mapped if any of these block it:

- **Hypervisor-Enforced Code Integrity (HVCI / "Memory Integrity")** must be **OFF**
  Settings → Privacy & security → Windows Security → Device security → Core isolation → **Memory integrity** → Off, then reboot.
- **Microsoft Vulnerable Driver Blocklist** must be **OFF**
  Settings → Privacy & security → Windows Security → Device security → Core isolation → **Microsoft Vulnerable Driver Blocklist** → Off, then reboot.
- **Antivirus / Defender** may quarantine `kdmapper.exe` or `driver_standalone.sys`. Add the Holiton folder to your AV exclusions.

If any of these are enabled, `kdmapper.exe` will exit with a non-zero code and the loader will display the error.

## Troubleshooting

| Symptom | Likely cause |
|---------|--------------|
| `kdmapper.exe exited with code 1` | HVCI or driver blocklist is enabled, or AV blocked the mapper |
| `Required files are missing` | You moved files apart — keep all five in one folder |
| Loader closes silently after CS2 prompt | You clicked Cancel — relaunch and click OK |
| Overlay does not appear in CS2 | Press `PAUSE` to open the menu; verify CS2 is in fullscreen-windowed or windowed mode |
| Controller starts but reports driver error | The driver was unloaded (e.g. after reboot). Run the loader again — it will re-map. |

## After a Windows reboot

The kernel driver is unloaded on every restart. Just run `holiton-loader.exe` again — it will detect the missing driver and re-map it automatically.

## Source code

This repository hosts the **built artifacts only**. The full source code is at:

**[github.com/hitolox/holiton-cs2](https://github.com/hitolox/holiton-cs2)**

The loader source lives in [`loader/`](https://github.com/hitolox/holiton-cs2/tree/main/loader); the controller in [`controller/`](https://github.com/hitolox/holiton-cs2/tree/main/controller).

## Disclaimer

Holiton is an **educational project** that explores the Windows kernel and Vulkan overlay rendering with Rust. It is read-only — it never writes to the CS2 process — but Valve's Anti-Cheat policies still apply. Use at your own risk; the authors take no responsibility for VAC bans, account suspensions, or system instability.

## License

See [LICENSE](https://github.com/hitolox/holiton-cs2/blob/main/LICENSE) in the source repository.
