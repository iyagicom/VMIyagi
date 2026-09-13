# VMIyagi

A development VM management tool for verifying the **Iyagi Series `.deb` packages on a clean Ubuntu installation**.

The goal is to eliminate the classic *“It works on my PC”* problem. Install a `.deb` on an Ubuntu system with nothing preinstalled, check whether it runs, and repeatedly return to a clean state.

VMIyagi is **not intended to replace VirtualBox or GNOME Boxes**. It is a thin frontend built on top of QEMU/KVM, containing only the features needed for this repetitive testing workflow.

The interface supports Korean and English. If the system language is Korean, Korean is used; otherwise, English is used.

---

## Requirements

- QEMU/KVM (`qemu-system-x86_64`, `qemu-img`) — a CPU with KVM support
- Qt 6.11 or later (same as the other Iyagi Series applications: `~/Qt/6.11.1/gcc_64`)
- An Ubuntu installation ISO (required once per VM)

```bash
QT_PREFIX=$(ls -d "$HOME/Qt"/*/gcc_64 | sort -V | tail -1)

cmake -S . -B build/linux-release \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_PREFIX_PATH="$QT_PREFIX"

cmake --build build/linux-release -j$(nproc)

./build/linux-release/VMIyagi
```

---

## First Time: Creating a VM

1. **Launch VMIyagi** — If no base disk exists, you will be prompted to select an installation ISO. A 40GB disk is created and booted from the ISO.
2. Install Ubuntu normally. **Use `iyagi` as the account name** (automation connects using this account).
3. After installation, run `apt update && apt full-upgrade` inside the guest.
4. **Prepare the guest** — See the section below.
5. Shut down the guest and **save a snapshot** named `Clean`.

From now on, clicking **Reset** returns the VM to the state saved at step 5.

## Guest Preparation (Once per VM)

The Ubuntu **Desktop** ISO does not include an SSH server or clipboard-sharing tools.

Since automation has no way to enter the guest initially, a one-time manual setup is required.

Click **Prepare Guest**. A preparation script will appear in the shared folder. Open a terminal inside the guest and paste and run the single command shown:

- Installs `openssh-server`, `spice-vdagent`, `xclip`, and `wl-clipboard`
- Registers the VMIyagi public key (`~/.ssh/vmiyagi_ed25519`, automatically generated if missing)
- Configures the shared folder `/mnt/build` to be mounted automatically
- Configures passwordless sudo **only for package-related commands** (`apt`, `dpkg`, and `systemctl`)

If clipboard sharing is not working yet and you cannot paste the command, use **Type Command in Guest**.

This types the command directly through the QEMU monitor, so no clipboard is required.

After preparation is complete, use **Test Connection** to verify it. Then shut down the guest and use **Save Snapshot** to update `Clean`.

If you do not save the snapshot, the preparation state will also be lost when you reset the VM.

---

## Normal Usage

| Button | Description |
|--------|-------------|
| **Run / Stop** | Start or stop the VM (Stop sends an ACPI shutdown request) |
| **Reset** | Restore the VM to the `Clean` snapshot |
| **Save Snapshot** | Save the current state as a snapshot. Using the same name **updates it by merging the changes** |
| **Clone** | Create an independent VM that shares no files with the original |
| **Snapshot Manager** | List and delete snapshots. Deletion is blocked if another snapshot depends on it |
| **Prepare Guest** | See above, plus connection testing and clipboard diagnostics |
| **Type Command in Guest** | Type commands into the guest without using the clipboard |
| **Run Test** | Select the latest Iyagi Series `.deb`, install and run it, then save the logs |

### Run Test

VMIyagi scans `<project>/build/**.deb` and displays the **latest `.deb` for each project**.

After selecting a package:

1. Copy it into the build folder (it immediately appears under `/mnt/build` in the guest)
2. Install it using `apt-get install` to verify that dependencies are actually resolved and installed
3. Locate the executable installed by the package and launch it on the guest desktop
4. Check whether it is still running after 8 seconds
5. Save the complete output to `logs/<vm>_<app>_<timestamp>.log`

**For a genuinely clean test, click Reset before running the test.** Otherwise, you may be testing on a VM where dependencies from previous tests are already installed.

### Clipboard / Display

- Host–guest copy and paste is automatically connected **when the VM is launched**.
  When the log shows `Clipboard connected`, it is ready.
- A VM shown as **“Running (launched outside the app)”** was started by a previous application instance. Clipboard and logging are disconnected, so stop and restart it.
- If the guest display flickers or looks corrupted, try changing the **Display Mode** from `virtio (GL off)` to `Safe`. The change takes effect on the next launch.

---

## Important Notes

- **VM disks are not backed up.** Snapshots are linked through differences (diff layers). Deleting files directly inside `vms/` from a file manager can corrupt other snapshots. Always delete snapshots through **Snapshot Manager**.
- If the guest account is not named `iyagi`, SSH key authentication will be rejected.
- Networking is fixed to QEMU user-mode NAT. Bridged networking and port forwarding are intentionally not exposed — they are outside the scope of this tool.

Implementation details and pitfalls are documented in [DEVNOTES.md](DEVNOTES.md).
