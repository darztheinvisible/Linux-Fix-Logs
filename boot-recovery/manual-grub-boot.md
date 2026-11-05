# Linux Mint — Manual GRUB Boot Recovery

**Date:** November 5, 2026  
**OS:** Linux Mint 22.3 Cinnamon  
**Kernel:** 7.0.0-42-generic  
**GPU:** AMD Radeon RX 9070  
**Status:** Resolved

---

## 1. Problem

After a problematic AMD graphics firmware update, Linux Mint failed to boot into the graphical desktop.

The system repeatedly displayed AMDGPU errors:

```text
amdgpu [drm] *ERROR* Error queueing DMUB command: status=2

amdgpu [drm] *ERROR* dc_dmub_srv_log_diagnostic_data: DMCUB error - collecting diagnostic data
```

Attempts to access a virtual terminal using `Ctrl + Alt + F3` did not provide a usable terminal.

A recovery method was needed to access Linux Mint without relying on normal AMD graphics initialization.

## 2. Accessing GRUB

After restarting the computer and pressing `Esc` during startup, I reached the GRUB command-line interface:

```text
grub>
```

Unlike the normal GRUB boot menu, this interface allows commands to be entered manually.

### Checking available partitions

I entered:

```text
ls
```

GRUB returned:

```text
(hd0) (hd0,gpt2) (hd0,gpt1)
```

This confirmed GRUB could detect the drive and its GPT partitions.

**Note:** GRUB device identifiers depend on the computer's storage configuration. They should never be assumed to match another system.

## 3. Locating the Boot Files

The next step was identifying which partition contained the Linux installation.

I checked:

```text
ls (hd0,gpt2)/boot/grub/
```

The directory contained GRUB-related files, including `grub.cfg`.

I also inspected:

```text
ls (hd0,gpt2)/boot/
```

This displayed Linux kernel and initial RAM disk files, including:

```text
vmlinuz-7.0.0-42-generic
initrd.img-7.0.0-42-generic
vmlinuz
initrd.img
```

The `vmlinuz` and `initrd.img` paths provided shortcuts to the kernel and initial RAM disk used for the recovery boot.

## 4. Troubleshooting Attempts

Before manually booting the kernel, I attempted to access the standard boot menu.

### Attempt 1 — GRUB normal mode

```text
normal
```

Rather than displaying an interactive boot menu, GRUB proceeded toward the normal boot, which returned to the AMDGPU errors.

### Attempt 2 — Load GRUB configuration

```text
configfile (hd0,gpt2)/boot/grub/grub.cfg
```

This located and loaded the existing GRUB configuration, but the machine again proceeded toward the problematic boot.

Pressing `Esc` during another attempt returned to the GRUB command prompt.

**Lesson:** Loading a GRUB configuration does not necessarily leave the boot menu visible. A hidden menu or automatic timeout can cause Linux to boot immediately.

Because these attempts did not provide a usable recovery boot, I proceeded to load the Linux kernel manually.

## 5. Successful Recovery — Manual Kernel Boot

The following commands were entered individually at the `grub>` prompt.

### Step 1 — Select the Linux partition

```text
set root=(hd0,gpt2)
```

This tells GRUB which partition to use when resolving kernel and boot-file paths.

### Step 2 — Load the Linux kernel

```text
linux /boot/vmlinuz root=/dev/nvme0n1p2 ro nomodeset
```

Explanation:

- `linux` — Loads a Linux kernel.
- `/boot/vmlinuz` — Path to the kernel.
- `root=/dev/nvme0n1p2` — Identifies the Linux root filesystem.
- `ro` — Initially mounts the root filesystem read-only.
- `nomodeset` — Disables normal kernel graphics mode-setting during startup.

The critical parameter was `nomodeset`, which allowed the system to boot without the problematic graphics initialization that occurred during normal startup.

**Important:** The device path `/dev/nvme0n1p2` was specific to this computer and must be verified before use on another system.

### Step 3 — Load the initial RAM disk

```text
initrd /boot/initrd.img
```

The initial RAM disk supplies drivers and startup components needed by the kernel to access the installed operating system.

### Step 4 — Boot Linux

```text
boot
```

After executing the final command, the system successfully reached the Linux Mint login screen.

I entered my normal account password and accessed the Cinnamon desktop.

**Result: Successful recovery boot.**

## 6. Side Effects of Recovery Mode

Although the desktop loaded, the system was not operating under normal graphics conditions.

Observed symptoms included:

- Increased CPU usage compared with normal operation.
- Higher CPU consumption from Cinnamon and Hidamari.
- Graphics acceleration and display behavior potentially limited by `nomodeset`.

The desktop had recovered, but the underlying AMD firmware issue was still present.

Separately, the motherboard's Wi-Fi controller was not detected. This was later restored using a complete motherboard power reset; it should not be assumed that `nomodeset` caused the Wi-Fi issue.

## 7. Permanent Resolution

Once Linux Mint was accessible, I investigated the installed AMD firmware.

The affected version was:

```text
20240318.git3b128b60-0ubuntu3.4
```

The DCN 4.0.1 firmware path incorrectly resolved to a DCN 3.2 firmware file.

I restored the previous AMD firmware version, `0ubuntu3.3`, blocked the affected package version, and rebuilt the initramfs.

After a normal reboot without `nomodeset`, Linux Mint loaded successfully with normal graphics functionality.

See the related guide:

[AMD RX 9070 Firmware Recovery](../graphics/amd-rx9070-firmware-recovery.md)

## 8. Lessons Learned

1. GRUB can be used to recover a Linux system even when the graphical desktop is inaccessible.
2. The `ls` command helps identify available partitions and boot files.
3. Kernel and initramfs files can be loaded manually when the standard boot process cannot be used.
4. `nomodeset` can provide a temporary recovery path for certain graphics initialization failures.
5. Reaching the desktop does not necessarily mean the original problem has been fixed.
6. Temporary recovery methods allow diagnostic commands and repairs to be performed without reinstalling the operating system.

## Important Notes

These commands document a successful recovery on one specific Linux Mint installation.

Partition names, kernel paths, boot configurations, encryption settings, and storage layouts can differ between computers.

Manual boot commands should be adjusted to the actual system. The `nomodeset` parameter is a diagnostic or recovery workaround, not a universal or permanent AMDGPU repair.

---

**Final Outcome:** Linux Mint successfully recovered through GRUB, allowing the underlying AMD firmware regression to be repaired without reinstalling the operating system or losing personal files.
