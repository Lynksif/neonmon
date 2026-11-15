# neonmon
A dashboard-style system monitor for **Linux, Windows and macOS** (built on Pop!_OS), with a dark neon-green UI. It shows what `btop` shows, laid out as cards with ring gauges, donut and line charts.

# NeonMon
# Design by LynkSif
A dashboard-style system monitor for **Linux, Windows and macOS** (built on Pop!_OS), with a dark neon-green UI.
It shows what `btop` shows, laid out as cards with ring gauges, donut and line charts.

![NeonMon screenshot]

## Features

- **CPU**: total load, per-thread bars, frequency, load average, package temperature
- **Memory**: used / cache / free donut, swap usage
- **Network & disk I/O**: live download/upload and read/write charts (last 60 seconds)
- **Load history**: CPU, RAM and GPU over the last 60 seconds
- **Disks**: usage for every mounted partition
- **Sensors & GPU**: all temperature sensors; NVIDIA (via `nvidia-smi`) and AMD GPU load, VRAM, temperature and power
- **Processes**: top processes sorted by CPU or RAM, with a name filter
- Updates every second, runs fully offline, listens on `127.0.0.1` only

## Install

Download the zip from the [Releases](../../releases) page and unzip it.

### Windows 10 / 11

1. Double-click **`install-windows.bat`**.
2. If Windows SmartScreen warns you, click *More info* then *Run anyway*.
3. Open **NeonMon** from the Start Menu or the Desktop shortcut.

The installer needs Python 3.8+. If it is missing, it installs Python with `winget`
(or asks you to get it from [python.org](https://www.python.org/downloads/)).
Everything goes into `%LOCALAPPDATA%\NeonMon`, no admin rights needed.
The window uses Edge WebView2 through `pywebview`; if that is not available, NeonMon opens in an Edge app window.

### macOS 10.13+

Open **Terminal** in the unzipped folder and run:

```bash
bash install-macos.command
```

Then open **NeonMon** from Launchpad or Spotlight (Cmd+Space, type NeonMon).
The installer needs Python 3.8+ (`xcode-select --install` provides it). It creates
`~/Applications/NeonMon.app` and keeps its files in `~/Library/Application Support/NeonMon`.

### Linux (Pop!_OS / Ubuntu / Debian)

```bash
unzip neonmon-1.0.0.zip
cd neonmon
chmod +x install.sh uninstall.sh
./install.sh
```

The script asks for your sudo password to install `python3-psutil`, `python3-gi`,
WebKitGTK (for the native window) and `lm-sensors` (for temperatures). It then copies the app to
`~/.local/share/neonmon`, adds a `neonmon` command in `~/.local/bin` and a launcher in your app menu.

Recommended once, to detect more temperature sensors:

```bash
sudo sensors-detect   # press Enter to accept the defaults
```

## Run

- Press **Super**, type **NeonMon**, press Enter, or
- run `neonmon` in a terminal (log out and back in if the command is not found yet).

Windows and macOS: use the Start Menu / Launchpad entry created by the installer.

Run without installing (any OS):

```bash
pip install psutil pywebview   # Linux: sudo apt install python3-psutil
python3 neonmon.py              # native window (GTK WebKit), falls back to Chrome --app or your browser
python3 neonmon.py --browser    # open in a browser
python3 neonmon.py --no-window  # server only, then open http://127.0.0.1:8765
```

Options: `--port 8765`, `--interval 1` (seconds), `--size 1480x940`.

## Platform notes

| | Linux | Windows | macOS |
|---|---|---|---|
| CPU, RAM, disks, network, processes | yes | yes | yes |
| Temperature sensors | yes (`lm-sensors`) | no (not exposed to apps) | no (needs root) |
| NVIDIA GPU | yes (`nvidia-smi`) | yes (`nvidia-smi`) | n/a |
| AMD GPU | yes | no | no |
| Load average | yes | emulated by psutil | yes |

## GPU support

- **NVIDIA**: needs the NVIDIA driver (`nvidia-smi`). Shows load, VRAM, temperature and power.
- **AMD**: read from `/sys/class/drm` (load, VRAM, temperature).
- **Intel iGPU**: name only, as the kernel exposes no simple load counter.
- With no readable GPU, the inner ring of the Activity card shows swap instead.

## Uninstall

- **Linux**: `./uninstall.sh`
- **Windows**: double-click `uninstall-windows.bat`
- **macOS**: `bash uninstall-macos.command`

This removes the app and its shortcuts. Python and packages installed with apt are kept.

## Project layout

- `server.py`: collects stats with psutil and serves JSON at `/api/stats`
- `neonmon.py`: starts the server and opens the window
- `web/`: the dashboard (plain HTML/CSS/JS, no internet needed)
- `install.sh`, `uninstall.sh`: Linux install and removal
- `install-windows.bat` / `.ps1`, `uninstall-windows.bat` / `.ps1`: Windows
- `install-macos.command`, `uninstall-macos.command`: macOS
- `icon.svg`, `icon.png`, `icon.ico`, `icon.icns`: app icons

## Requirements

Python 3.8+ and `psutil`. For the native window: PyGObject + WebKit2GTK on Linux, `pywebview` on Windows and macOS.
Without them NeonMon falls back to a Chrome/Edge app window or your default browser.
