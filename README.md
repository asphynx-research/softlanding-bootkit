# SOFTLANDING UEFI BOOTKIT — ASPHYNX-2026-0001

[![Classification](https://img.shields.io/badge/Classification-PUBLIC-brightgreen)](.)
[![TLP](https://img.shields.io/badge/TLP-WHITE-brightgreen)](.)
[![Affected](https://img.shields.io/badge/Affected-240%2B%20Gigabyte%20Models-red)](.)
[![CVEs](https://img.shields.io/badge/CVEs-Mapped-9cf)](.)

> **The first public documentation of the SoftLanding UEFI DXE bootkit** — a multi-agent, cross-platform firmware implant that persists in SPI flash and survives all software-based removal attempts.
>
> **Part of the TeamPCP campaign:** An 8-layer APT attack stack using bootkits, VPN exploitation, overlay network hijacking, ML-driven adaptation, and cloud-based C2 infrastructure.

---

## ⚡ QUICK CHECK — ARE YOU INFECTED?

```bash
# Linux (or Live USB for Win/Mac)
sudo efibootmgr | grep -i "VenHw"
sudo find /boot/efi -name "mach_kernel"
sudo find /boot/efi -name "SystemVersion.plist"

# Check for C2 traffic
ss -tunap | grep -E "4145|5678|1080"
```

**ANY output = investigate immediately.** Reinstalling your OS will not help.  
**The implant survives disk formatting, OS reinstallation, and BIOS updates.**

---

## 🔴 WHAT THIS IS

This is a **UEFI DXE firmware bootkit** installed in the **SPI flash chip** on your motherboard — a physical hardware component. It executes at **Ring -2** BEFORE any operating system loads, making it invisible to:

- All antivirus and EDR software
- OS-level security tools (firewalls, ACLs, iptables)
- Secure Boot and BitLocker
- Disk formatting and OS reinstallation

**It survives:** ✅ Disk formatting | ✅ OS reinstall | ✅ BIOS Q-Flash | ✅ AV scans | ✅ Apple diagnostics

**It does NOT survive:** ❌ Physical SPI flash reprogramming (CH341A)

---

## 🗺️ THE FULL ATTACK — TEAMPCP CAMPAIGN

SoftLanding is **Layer 0** (firmware) of an 8-layer attack stack:

```
LAYER 0:  UEFI BOOTKIT (SoftLanding)       ← THIS REPO
LAYER 1:  SUPPLY CHAIN (npm/PyPI poisoning)
LAYER 2:  VPN PRE-BOOT (Gateway exploitation)
LAYER 3:  WIREGUARD (Encryption bypass)
LAYER 4:  TAILSCALE (Overlay network hijacking)
LAYER 5:  C2 CLOUD INFRA (Distributed C2)
LAYER 6:  ML ADAPTIVE CLUSTER (Polymorphic behavior)
LAYER 7:  HARDWARE RCE (Embedded device exploitation)
```

**19 CVEs mapped | Multiple vulnerabilities under coordinated disclosure**

---

## 📦 REPOSITORY CONTENTS

```
softlanding-bootkit/
├── README.md                         ← YOU ARE HERE
├── ADVISORY.md                       ← Full technical advisory (ASPHYNX-2026-0001)
├── IOC_LIST.md                       ← Indicators of compromise
├── DETECTION/
│   ├── yara_rules.yar                ← Firmware/disk/memory rules (5 rules)
│   ├── snort_suricata.rules          ← Network IDS/IPS rules (12 rules)
│   └── sigma_rules.yml               ← SIEM detection rules
├── BLOCKLISTS/
│   ├── ip_blocklist.txt              ← Firewall blocklist (100+ IPs)
│   └── domain_blocklist.txt          ← DNS/Pi-hole blocklist
├── REMEDIATION/
│   └── CH341A_flash_guide.md         ← Step-by-step SPI flash recovery
└── LICENSE
```

---

## 🔑 KEY FINDINGS

| Field | Detail |
|-------|--------|
| **First seen** | March 2026 |
| **Infection vector** | SEO-poisoned installers + social engineering |
| **Firmware exploit** | CVE-2025-7029 — Gigabyte OverClockSmiHandler SMM corruption |
| **DXE driver GUID** | `99E275E7-75A0-4B37-A2E6-C5385E6C00CB` |
| **Affected hardware** | 240+ Gigabyte motherboard models (Binarly BRLY-2025-009) |
| **Confirmed model** | Z590 AORUS MASTER (rev 1.0), firmware F9/F10 |
| **Persistence level** | Ring -2 (SPI flash, DXE phase) |
| **Cross-platform** | Windows, Linux, macOS (DXE driver is OS-agnostic) |
| **Multi-agent** | Firmware implant → kernel agent → userland agent |
| **Dual C2** | Memory exfiltration channel + operational control |
| **AI/ML evasion** | GPU-accelerated code obfuscation |
| **Campaign** | TeamPCP supply chain operation |

---

## 🔬 VULNERABILITIES UNDER COORDINATED DISCLOSURE

Additional findings discovered during this research are under responsible disclosure:

| # | Description | Product | Severity |
|---|-------------|---------|----------|
| 1 | ACL Hijacking with ML-driven persistence | Tailscale | Critical |
| 2 | Permanent decryption stall | WireGuard | High |
| 3 | DNS peer injection + credential exfiltration | Tailscale | High |

*Full advisories will be published after coordinated disclosure window.*

---

## 🤝 I WANT TO HELP

**If you found this useful:**
- ⭐ Star this repo
- 🔄 Share it with your network
- ✉️ Contact: asphynx-research@proton.me

**If you found this bootkit on your system:**
1. **DO NOT reinstall your OS** — it won't help
2. Read `REMEDIATION/CH341A_flash_guide.md`
3. Send your firmware dump to help research
4. Report to abuse.ch / AlienVault OTX with tag `ASPHYNX-2026-0001`

---

## 📚 CREDITS & REFERENCES

- **Discovery & Analysis:** ASPHYNX RESEARCH (June 2026)
- **CVE exploited:** CVE-2025-7029 (Binarly Research, BRLY-2025-009)
- **Campaign context:** TeamPCP supply chain operation (Microsoft, Wiz, Zscaler, Datadog)
- **VPN research:** CVE-2026-0257 (Palo Alto), CVE-2026-50751 (Check Point)
- **WireGuard research:** CVE-2026-52945, CVE-2026-31579, CVE-2026-27899
- **Tailscale research:** TS-2026-001/002/003, CVE-2026-41393, CVE-2026-32045

---

> *"Trust nothing. Verify everything. The enemy is not in your OS. It is in your firmware."*
> — ASPHYNX RESEARCH, June 2026
