# fix-audio-alc245 — Minisforum X1 AI Pro

Fixes silent/broken audio on the **Minisforum X1 AI Pro** (and likely other mini PCs using the **Realtek ALC245** codec) on Linux.

The codec ships with incorrect default coefficients that prevent the internal speakers and headphone jack from initialising properly. This fix writes the correct values via `hda-verb` at boot and after every suspend resume.

## Symptoms

- No sound at all after boot
- Audio appears in PipeWire / PulseAudio but produces no output
- Speakers or headphone jack silent even with volume at 100 %
- Sound returns temporarily after a cold reboot but disappears after suspend

## How it works

The script uses `hda-verb` to program the ALC245 codec registers directly:

1. Writes the correct processing coefficients (verbs 0x06 – 0x67)
2. Resets GPIO state to a known-clean baseline
3. Waits 1 second for the codec to settle

Two systemd services run the script automatically:

| Service | Trigger |
|---|---|
| `fix-audio-alc245.service` | Boot |
| `fix-audio-alc245-resume.service` | Resume from suspend / hibernate |

## Requirements

- `hda-verb` (part of **alsa-tools**)

| Distro | Install command |
|---|---|
| Arch / Manjaro | `sudo pacman -S alsa-tools` |
| Debian / Ubuntu | `sudo apt install alsa-tools` |
| Fedora | `sudo dnf install alsa-tools` |

## Install

```bash
git clone https://github.com/YOUR_USERNAME/fix-audio-alc245.git
cd fix-audio-alc245
sudo bash install.sh
```

## Uninstall

```bash
sudo bash uninstall.sh
```

## Manual test (without installing)

```bash
sudo bash fix-audio-alc245.sh
```

## Check service status

```bash
systemctl status fix-audio-alc245.service
journalctl -u fix-audio-alc245.service
```

## Tested on

| Hardware | Kernel | Distro |
|---|---|---|
| Minisforum X1 AI Pro | 6.x | Arch Linux |
| Minisforum X1 AI Pro | 7.x | Ubuntu 26.04 LTS |

If this works on other hardware or distros, feel free to open a PR to expand the table.

## License

MIT
