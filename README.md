# VMIyagi

**"Works on my PC" isn't enough. Test your app on a clean Linux in seconds — install it, run it, check it, reset, repeat.**

[English](README.md) · [한국어](README_ko.md)

## Why VMIyagi?

- **Back to a clean system in one click.** Snapshots store only the differences, so they're fast and take almost no space. Reset and the VM is exactly as you saved it — no reinstalling the OS between tests.
- **Test a package with one button.** Pick a `.deb`, `.rpm`, `.pkg.tar.zst`, AppImage or zip. **Run Test** installs it in the guest, launches it, checks it's still alive and collects the logs.
- **Copy, paste and share folders without setup.** Run one command in the guest and clipboard, auto-resize and shared folders just work.
- **Ubuntu, Debian, Fedora, Arch — and Windows 11.** Windows 11 installs with UEFI + TPM 2.0, with clipboard and shared folders too.
- **Start from a ready-made image.** Import the qcow2 images distributions publish and boot without installing.
- **Light, not a do-everything VM suite.** A small window on top of QEMU/KVM with just what this test loop needs.

## Features

| | |
|---|---|
| **Snapshot / reset** | Difference-only snapshots, boot any snapshot separately |
| **Run Test** | Install → run → alive check → logs, from a package you pick |
| **Guest tools CD** | Clipboard, auto screen size and shared folders with one command |
| **Shared folders** | Host folder appears at `/mnt/<name>` (a drive letter on Windows guests) |
| **Data CD** | Burn a folder to ISO and attach it — handy for large installers |
| **Clone** | A fully independent copy of a VM |
| **Import disk image** | Start from a distribution's qcow2 image |
| **Windows 11 guests** | UEFI + TPM 2.0 |
| **Korean/Hanja keys** | Sends keys QEMU's window doesn't pass through |

## Download

**[⬇ Latest release](https://github.com/iyagicom/VMIyagi/releases/latest)**

| Your system | File to pick |
|---|---|
| Ubuntu 24.04 · Debian | `.deb` marked **ubuntu24.04** |
| Ubuntu 26.04 | `.deb` marked **ubuntu26.04** |
| Fedora · openSUSE | `.rpm` |
| Arch · Manjaro | `.pkg.tar.zst` |
| Any other Linux | `.AppImage` or `.zip` |

**You need** an x86-64 PC with virtualization (KVM) enabled, plus `qemu-system-x86` and `qemu-utils`. The `.deb` pulls these in for you, along with the optional helpers (`virtiofsd` for shared folders, `xorriso` for CDs, `ovmf` + `swtpm` for Windows 11).

```bash
sudo apt install ./vmiyagi_*_amd64.deb       # Ubuntu / Debian
```
