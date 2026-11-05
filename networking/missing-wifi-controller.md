# Linux Mint — Missing Wi-Fi Controller Recovery

**Date:** November 5, 2026  
**OS:** Linux Mint 22.3 Cinnamon  
**Motherboard:** MSI B850 Gaming Plus WiFi  
**Network Hardware:** Integrated Wi-Fi 7 module  
**Status:** Resolved

---

## 1. Problem

After recovering from an AMD graphics firmware boot failure, Linux Mint successfully loaded the Cinnamon desktop.

However, the computer had no Wi-Fi connection.

The network menu displayed a wired connection with the message:

```text
Wired
Cable unplugged
```

This initially appeared to be a network connection issue, but further investigation revealed that Linux was not detecting the motherboard's Wi-Fi controller.

The computer normally connects to the internet using the MSI wireless antenna attached to the motherboard.

## 2. Initial Diagnosis

The first step was to determine which network devices Linux could detect.

### Checking NetworkManager

```bash
nmcli device status
```

The output showed the loopback interface and the Ethernet interface:

```text
lo
enp13s0
```

However, no Wi-Fi interface was listed.

The Ethernet interface being disconnected was expected because the computer normally uses Wi-Fi rather than an Ethernet cable.

### Checking PCI Devices

I checked for a network controller:

```bash
lspci | grep -i network
```

The command produced no output.

This indicated that no PCI device description matched the word `network`, but additional investigation was necessary.

I then ran:

```bash
lspci
```

The full PCI device listing included:

- AMD Radeon RX 9070 graphics hardware.
- Realtek RTL8126 Ethernet controller.
- NVMe storage controller.
- Other motherboard components.

However, no identifiable wireless network controller appeared in the PCI listing.

### Checking USB Devices

Next, I checked the USB device list:

```bash
lsusb
```

The output included connected peripherals such as the keyboard, mouse, webcam, and ReSpeaker microphone array.

No clearly identifiable wireless adapter appeared in the output.

**Diagnostic conclusion:** The Wi-Fi controller was not visible through the normal Linux network-device listing or identifiable in the hardware listings.

This suggested a hardware detection or initialization problem rather than simply an incorrect Wi-Fi password or disconnected network.

## 3. Temporary Workaround — Android USB Tethering

Because the motherboard's wireless connection was unavailable, I needed another way to connect the computer to the internet.

I used an Android phone and a USB-A-to-USB-C data cable.

### Steps

1. Connected the Android phone directly to the PC.
2. Confirmed the USB cable supported data transfer.
3. Opened Android's hotspot and tethering settings.
4. Enabled USB tethering.
5. Waited for Linux Mint to recognize the new network connection.

During setup, Linux Mint displayed an MTP-related error:

```text
Unable to open MTP device
```

This was associated with accessing the phone's files and did not prevent USB tethering from working.

After enabling USB tethering, the computer successfully connected to the internet through the phone.

**Result: Temporary internet access restored.**

This allowed additional troubleshooting and access to online resources while the motherboard's Wi-Fi was unavailable.

## 4. Checking After the AMD Firmware Repair

The AMD graphics firmware problem was subsequently repaired, and Linux Mint successfully rebooted with normal graphics functionality.

However, the motherboard's Wi-Fi controller was still missing.

This was important evidence: restoring the AMD graphics firmware did not automatically restore the wireless adapter.

It also showed that the wireless problem required separate troubleshooting.

## 5. Successful Solution — Complete Motherboard Power Reset

Rather than immediately reinstalling network drivers or modifying BIOS settings, I performed a full power reset.

### Procedure

1. Shut down Linux Mint normally.
2. Switched the power supply to the `O` (OFF) position.
3. Unplugged the computer's power cable.
4. Held the computer's power button for approximately 20–30 seconds.
5. Reconnected the power cable.
6. Switched the power supply to `I` (ON).
7. Powered on the computer normally.

After booting back into Linux Mint, the Wi-Fi connection was restored.

**Result: Successful Wi-Fi recovery.**

No network driver reinstall, BIOS reset, or operating system reinstall was required.

## 6. Why the Power Reset May Have Worked

Modern desktop computers can receive standby power even while shut down.

When a power supply remains connected and switched on, its standby power rail may continue powering selected motherboard circuits.

This enables features such as wake-on-LAN and certain power-management functions.

A controller can occasionally remain in an unresponsive or improperly initialized state across ordinary restarts.

Completely disconnecting power may allow the affected circuitry to reset.

### Capacitors vs. CMOS Battery

The motherboard and power supply contain capacitors that can temporarily retain electrical charge after external power is removed.

Holding the computer's power button while the PSU is disconnected can help drain some residual low-voltage charge.

The CMOS battery primarily supports the real-time clock and certain motherboard settings-related functions. It does not normally power the motherboard's Wi-Fi controller.

### Technical Interpretation

The successful power reset suggests the wireless controller may have been stuck in a hardware or power-management state.

However, the exact underlying failure was not independently confirmed.

The important observation was that the controller became available again after complete power removal.

## 7. What Didn't Resolve the Problem

Several earlier actions did not restore Wi-Fi:

- Booting Linux Mint using the `nomodeset` graphics workaround.
- Restoring the previous AMD graphics firmware.
- Rebooting normally after the AMD firmware repair.

These results helped distinguish the Wi-Fi issue from the graphics firmware regression.

USB tethering provided temporary connectivity but did not repair the motherboard's wireless controller.

## 8. Verification

After the full power reset, Wi-Fi connectivity returned and the computer was usable without USB tethering.

A useful command for checking network devices is:

```bash
nmcli device status
```

A functioning wireless interface should normally appear with the type `wifi`.

For additional diagnostic information:

```bash
lspci -nnk
```

This can help identify detected PCI devices and their associated kernel drivers.

## 9. Lessons Learned

1. A missing Wi-Fi interface is not always caused by a driver problem.
2. Hardware enumeration tools such as `lspci` and `lsusb` help narrow down detection issues.
3. An Ethernet connection labeled "cable unplugged" does not necessarily indicate a Wi-Fi failure.
4. Android USB tethering can provide temporary connectivity when the computer's wireless adapter is unavailable.
5. A complete motherboard power reset can sometimes restore an unresponsive hardware controller.
6. A normal reboot does not necessarily reset every hardware or power-management state.
7. Hardware detection problems should be investigated before reinstalling drivers or changing operating system settings.

## 10. Final Outcome

The motherboard's wireless functionality was successfully restored through a full power reset.

No operating system reinstall, driver reinstall, or BIOS reset was needed.

This case demonstrates why Linux troubleshooting should include both software diagnostics and hardware-level recovery techniques.

---

**Final Status:** Resolved — Wi-Fi restored after completely removing power from the motherboard.

**Important:** This is a documented recovery of one specific system. A power reset is not guaranteed to repair every missing wireless adapter. Persistent failures may require additional driver, firmware, BIOS, or hardware investigation.
