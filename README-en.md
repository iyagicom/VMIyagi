# VMIyagi

**A tool for checking whether your programs actually run on other people's PCs, using clean virtual machines.**

VMIyagi was created to eliminate the classic *"It works on my PC"* problem. Install a package in a Linux VM with nothing preinstalled, run it, and return to the saved clean state with one click. Repeat as needed.

A lightweight management interface built on top of QEMU/KVM. It is not a full-featured VM manager like VirtualBox — it includes only the features needed for this repetitive testing workflow.

The interface supports Korean and English. Korean is used when the system language is Korean; otherwise, English is displayed.

---

## Who Is It For?

- Anyone who wants to verify installation and execution across multiple distributions before **releasing** `deb` / `rpm` / `pkg.tar.zst` / `zip` / `AppImage` packages
- Anyone who does not want to reinstall an OS for every test — click **Reset** to return to the saved state
- Anyone who wants clipboard sharing and shared folders between host and guest to work without complicated setup

## Key Features

| Feature | Description |
|---|---|
| **Snapshots / Reset** | Stores only differences, making snapshots fast and space-efficient. Each snapshot can be booted independently |
| **Run Test** | Select a package from a folder, install and run it in the guest, check whether it stays alive, and save logs |
| **Guest Tools CD** | Run one command in the guest to configure clipboard sharing, automatic display resizing, and shared folders. Works without network access |
| **Shared Folders** | Host folders appear under `/mnt/<name>` in the guest (drive letters for Windows guests) |
| **Data CD** | Burns a folder into an ISO and attaches it to the guest as a CD. Useful for transferring large installation files |
| **Clone** | Creates an independent VM that shares no files with the original |
| **Import Disk Image** | Start immediately from distribution-provided images such as qcow2, without installation |
| **Windows 11 Guest** | Install using UEFI + TPM 2.0. Clipboard and shared folders are supported |
| **Korean/English · Hanja Keys** | Sends keys that are not forwarded through the QEMU window by simulating them inside the guest |

Supported Linux guests: Ubuntu / Debian-based, Fedora-based, and Arch-based distributions.

---

## Requirements

- An x86-64 PC with KVM support (hardware virtualization enabled in BIOS)
- `qemu-system-x86`, `qemu-utils` — **required**
- Optional packages depending on the features you use:

| Package | Required For |
|---|---|
| `virtiofsd` | Shared folders (without it, only one shared folder works via SMB) |
| `xorriso` | Guest Tools CD and Data CD |
| `ovmf`, `swtpm`, `swtpm-tools` | Windows 11 guests (UEFI + TPM) |
| `openssh-client` | Run Test |

On Ubuntu:

```bash
sudo apt install qemu-system-x86 qemu-utils virtiofsd xorriso ovmf swtpm swtpm-tools
```

When installed from a `.deb`, required packages are installed automatically, and the remaining packages are included as recommended dependencies.

## Installation

Download the appropriate package for your distribution. Qt is bundled, so no separate Qt installation is required.

| Distribution | Package |
|---|---|
| Ubuntu 24.04 | `vmiyagi_<version>~ubuntu24.04_amd64.deb` |
| Ubuntu 26.04 | `vmiyagi_<version>~ubuntu26.04_amd64.deb` |
| Fedora | `vmiyagi-<version>-1.x86_64.rpm` |
| Arch | `vmiyagi-<version>-1-x86_64.pkg.tar.zst` |
| Other distributions | `vmiyagi-v<version>-x86_64.AppImage` or `…-linux-x64.zip` |

---

## Getting Started

1. **Create a VM** — Choose a name, firmware (BIOS for Linux, UEFI + TPM for Windows 11), and disk (empty disk or imported image).
2. **Run** — If using an empty disk, select an installation ISO when prompted. Install the OS normally. The default account name is `iyagi`; if you use a different name, update it in **Settings**.
3. **Prepare Guest** — Run the one-line command shown in the guest terminal to install the guest tools. If clipboard sharing is not working yet, **Type in Guest** will enter the command for you.
4. Shut down the guest and **Save Snapshot** — use the name `Clean`.

Now use **Run Test** to test packages, and click **Reset** afterward to return to the state saved in step 4.

VMs are stored in `~/VMIyagi`. To move them, use **VM Folder → Move** in the application.

**Do not directly delete or rename snapshot files in a file manager.** Doing so may corrupt other snapshots.

## Troubleshooting

- **Clipboard does not work** — Make sure Guest Tools are installed, then try **Prepare Guest → Clipboard Diagnostics**. Enable **Diagnostic Logging** to see the cause in the logs.
- **Display flickers or does not appear** — Change **Display Mode** from `virtio (GL off)` → `Safe (VGA)`. The change takes effect on the next launch.
- **"Running (launched outside the app)"** — The VM was already running before the application was started. Clipboard will reconnect, but if anything behaves incorrectly, shut down and restart the VM.
