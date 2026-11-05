# 🐧 Linux-Fix-Logs

### Real Problems. Real Troubleshooting. Documented Solutions.

A growing collection of Linux troubleshooting experiences, system recovery procedures, diagnostic commands, and verified fixes.

This repository documents real technical issues encountered while working with Linux systems, including the investigation process, unsuccessful attempts, successful solutions, and lessons learned.

**The goal is simple:** Learn from every problem, document the solution, and make that knowledge useful to others.

---

## 📖 Troubleshooting Index

| Category | Issue | Status |
|---|---|---|
| Graphics | [AMD RX 9070 Firmware Recovery](graphics/amd-rx9070-firmware-recovery.md) | ✅ Resolved |
| Boot Recovery | [Manual Linux Boot Through GRUB](boot-recovery/manual-grub-boot.md) | ✅ Resolved |
| Networking | [Missing Wi-Fi Controller Recovery](networking/missing-wifi-controller.md) | ✅ Resolved |

*Additional troubleshooting guides will be added as new issues are investigated and resolved.*

## 🔧 Documented Recoveries

### 01 — AMD RX 9070 Firmware Boot Failure

**System:** Linux Mint 22.3 Cinnamon  
**Date:** November 5, 2026

A problematic AMD firmware update prevented Linux Mint from booting normally, repeatedly generating DMUB/DMCUB errors.

**Solution:** Identified the affected firmware package, restored the previous working version, blocked the problematic update, and rebuilt the system's boot images.

[Read the recovery guide](graphics/amd-rx9070-firmware-recovery.md)

### 02 — Manual Linux Recovery Through GRUB

**System:** Linux Mint 22.3 Cinnamon  
**Date:** November 5, 2026

The graphical desktop was inaccessible, and the GRUB configuration automatically returned to the failing boot process.

**Solution:** Located the Linux partition, manually loaded the kernel and initial RAM disk, and temporarily bypassed normal graphics initialization using `nomodeset`.

[Read the recovery guide](boot-recovery/manual-grub-boot.md)

### 03 — Missing Motherboard Wi-Fi Controller

**System:** MSI B850 Gaming Plus WiFi / Linux Mint  
**Date:** November 5, 2026

The integrated wireless controller disappeared from Linux's network-device listings, leaving the computer without its normal Wi-Fi connection.

**Solution:** Used Android USB tethering for temporary internet access, investigated hardware detection, and restored wireless functionality through a complete motherboard power reset.

[Read the recovery guide](networking/missing-wifi-controller.md)

---

## 🧠 Troubleshooting Methodology

Each case study follows a consistent documentation structure.

1. **Problem Identification** — Describe the symptoms and error messages.
2. **System Information** — Record relevant hardware, software, and versions.
3. **Investigation** — Document diagnostic commands and observations.
4. **Failed Attempts** — Record what did not resolve the problem.
5. **Solution** — Explain the successful recovery procedure.
6. **Verification** — Confirm the issue was resolved.
7. **Lessons Learned** — Explain the technical knowledge gained.

Failed attempts are intentionally documented. Understanding why a particular approach did not work is often just as important as understanding the final solution.

## 💻 Environment

The initial troubleshooting cases were performed on the following system:

| Component | Configuration |
|---|---|
| Operating System | Linux Mint 22.3 Cinnamon |
| CPU | AMD Ryzen 7 7700X |
| GPU | AMD Radeon RX 9070 |
| Motherboard | MSI B850 Gaming Plus WiFi |
| Memory | 32 GB RAM |
| Kernel at time of recovery | 7.0.0-42-generic |

Future entries may include additional Linux distributions, virtual machines, servers, and networking environments.

## 🛡️ Security and Safety

This repository is intended for educational purposes and technical reference.

- Commands and configurations may need to be adapted to individual systems.
- Always verify device paths, package versions, and firmware compatibility.
- Back up important data before making potentially destructive system changes.
- Review downloaded packages and verify their integrity.
- Avoid running unfamiliar commands without understanding their effects.
- Never publish passwords, authentication tokens, private keys, or sensitive system information.

**Important:** A solution that worked on one system is not guaranteed to work on another. Read the complete case study and verify that the symptoms and environment match before attempting a recovery.

## 🎯 Project Goals

- Build a searchable library of Linux troubleshooting knowledge.
- Develop practical Linux administration and diagnostic skills.
- Document real-world technical problem-solving experience.
- Share useful recovery procedures with the Linux community.
- Maintain a technical portfolio demonstrating hands-on troubleshooting.
- Continue improving documentation as new tools and techniques are learned.

## 📚 Learning Through Experience

Linux-Fix-Logs is an ongoing learning project.

Rather than presenting every troubleshooting session as a flawless process, this repository preserves the actual investigation: what was observed, what was tested, what failed, and what ultimately worked.

Every resolved issue is an opportunity to better understand Linux, computer hardware, networking, and system administration.

---

**Built through experience. Improved through troubleshooting. Shared for others to learn.**
