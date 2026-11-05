
# AMD RX 9070 — DMUB Firmware Boot Failure

**Date:** November 5, 2026  
**OS:** Linux Mint 22.3 Cinnamon  
**Kernel:** 7.0.0-42-generic  
**GPU:** AMD Radeon RX 9070  
**Status:** Resolved

## 1. Problem

After starting my PC, Linux Mint failed to load the desktop and repeatedly displayed AMDGPU errors.

### Error messages

```text
amdgpu [drm] *ERROR* Error queueing DMUB command: status=2

amdgpu [drm] *ERROR* dc_dmub_srv_log_diagnostic_data: DMCUB error - collecting diagnostic data
```

The system could not boot normally into the graphical desktop.

## 2. Diagnosis

I suspected an AMD graphics driver or firmware problem.

After recovering access to Linux Mint using a temporary GRUB boot parameter, I checked the installed firmware:

```bash
apt policy linux-firmware-amd-graphics linux-firmware
```

The installed AMD graphics firmware version was:

```text
20240318.git3b128b60-0ubuntu3.4
```

I also checked the firmware file:

```bash
readlink -f /lib/firmware/amdgpu/dcn_4_0_1_dmcub.bin.zst
```

It unexpectedly resolved to:

```text
/usr/lib/firmware/amdgpu/dcn_3_2_0_dmcub.bin.zst
```

This was consistent with a reported AMD firmware packaging regression.

## 3. Solution

The solution was to downgrade the affected AMD firmware package to version `0ubuntu3.3`.

### Download the previous firmware

```bash
cd /tmp

wget https://launchpadlibrarian.net/877016066/linux-firmware-amd-graphics_20240318.git3b128b60-0ubuntu3.3_all.deb
```

### Verify the downloaded package

```bash
echo "7c6a6083e963fd9394d1f6471b6a94e787a76c6f98b262c2d9262b9a57308b53  linux-firmware-amd-graphics_20240318.git3b128b60-0ubuntu3.3_all.deb" | sha256sum -c
```

The checksum verification returned `OK`.

### Install the working firmware

```bash
sudo apt install --allow-downgrades /tmp/linux-firmware-amd-graphics_20240318.git3b128b60-0ubuntu3.3_all.deb
```

### Block the affected firmware version

```bash
printf 'Package: linux-firmware-amd-graphics\nPin: version 20240318.git3b128b60-0ubuntu3.4\nPin-Priority: -1\n' | sudo tee /etc/apt/preferences.d/amdgpu-firmware.pref
```

This prevents the known-bad version from being selected again.

### Rebuild boot images

```bash
sudo update-initramfs -u -k all
```

## 4. Verification

I checked the installed version:

```bash
apt policy linux-firmware-amd-graphics
```

Results:

```text
Installed: 20240318.git3b128b60-0ubuntu3.3
Candidate: 20240318.git3b128b60-0ubuntu3.3
```

I also verified that the DCN 4.0.1 firmware file was restored as a regular file rather than the incorrect symlink.

After rebooting normally, the graphical desktop loaded successfully.

## 5. What Didn't Work

- Attempting to boot normally repeatedly produced the same AMDGPU errors.
- A temporary `nomodeset` boot allowed desktop access but did not repair the firmware.
- The previous firmware package returned HTTP 404 from the attempted Ubuntu archive URL.
- Downloading the package from Launchpad worked.

## 6. Lessons Learned

- A graphics boot failure does not automatically indicate damaged hardware.
- Firmware updates can cause regressions.
- GRUB recovery options are valuable when the graphical desktop cannot load.
- Package checksums should be verified before installing downloaded files.
- Rebuilding initramfs can be necessary after changing firmware.
- Always verify a repair with a normal reboot.

## Important Notes

This documents a specific recovery performed on November 5, 2026. It is not a universal fix for every AMDGPU error.

Confirm the affected package version and firmware mismatch before applying the downgrade. Future corrected firmware releases may make this workaround unnecessary.
