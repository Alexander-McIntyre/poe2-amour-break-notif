# PoE2 Armour Break Notifier

A lightweight screen-monitoring tool for **Path of Exile 2** that detects when an enemy's armour is broken and plays an audio cue so you can act on it immediately.

> **Current scope:** Boss enemies only.

---

## How it works

The tool does **not** read game memory or modify any game files — it works entirely by capturing a small region of your screen and running template matching against a reference image of the armour break icon.

```
loop at ~10 fps:
    capture small screen region around enemy status icons
    run OpenCV template match against armour break reference image
    if confidence > threshold and not in cooldown:
        play audio cue
        set cooldown timer
```

This approach is ToS-safe: it is equivalent to a person watching their screen.

---

## Requirements

### System
- Debian (Hyprland / Wayland)
- [`grim`](https://sr.ht/~emersion/grim/) — Wayland screen capture (`wlr-screencopy` protocol, supported natively by Hyprland)
- [`slurp`](https://github.com/emersion/slurp) — region coordinate picker (used during setup only)
- Python 3.10+

```bash
sudo apt install grim slurp python3-venv python3-pip
```

### Python dependencies
```bash
pip install opencv-python Pillow pygame
```

---

## Setup

```bash
git clone <repo-url>
cd poe2-amour-break-notif

python3 -m venv .venv
source .venv/bin/activate
pip install opencv-python Pillow pygame
```

### 1. Capture a reference image

Take a screenshot of the armour break icon as it appears on your screen at your native resolution. Save it to:

```
assets/armour_break_icon.png
```

The reference image should be cropped tightly around the icon with minimal surrounding pixels.

### 2. Find your screen region

Run `slurp` to click-drag a bounding box around the area of the screen where the icon appears (typically near the top of the screen above the boss health bar):

```bash
slurp
```

Note the output (e.g. `1234,56 200x80`) — you'll put this in the config.

### 3. Configure

Copy the example config and fill in your values:

```bash
cp config.example.toml config.toml
```

---

## Usage

```bash
source .venv/bin/activate
python main.py
```

---

## Platform support

| Platform | Status |
|---|---|
| Debian / Hyprland (Wayland) | Primary target |
| Windows | Planned — will use `mss` for screen capture instead of `grim` |

---

## Limitations

- Resolution-dependent: the reference icon image must be captured at the same resolution and UI scale you play at.
- Boss-only for now: the icon location for regular enemies may differ and is not yet handled.
- A cooldown period (default ~3 seconds) prevents audio spam if the icon persists on screen.
