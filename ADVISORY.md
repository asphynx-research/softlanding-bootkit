# SOFTLANDING UEFI DXE BOOTKIT — Technical Advisory
## ASPHYNX-2026-0001 | CVSS 9.8 | June 2026

---

## 1. EXECUTIVE SUMMARY

SoftLanding is a UEFI DXE bootkit that persists in SPI flash memory on Gigabyte motherboards. It executes at **Ring -2** (DXE phase) before any operating system loads, rendering it invisible to all OS-level security tools including antivirus, EDR, Secure Boot, and BitLocker.

The implant deploys three-tiered agents across Windows, Linux, and macOS with dual C2 architecture. It cannot be removed by OS reinstallation, disk formatting, or OEM BIOS flashing. Full eradication requires physical SPI flash reprogramming via external programmer.

---

## 2. AFFECTED PRODUCTS

| Field | Detail |
|-------|--------|
| Vendor | Gigabyte Technology Co., Ltd. |
| Component | OverClockSmiHandler (SMI Handler), DXE Phase Firmware Volume |
| CVE Exploited | CVE-2025-7029 — SMM Memory Corruption |
| Confirmed Model | Gigabyte Z590 AORUS MASTER (rev 1.0), Firmware F9/F10 |
| Suspected Models | 240+ Gigabyte motherboards (Z590, Z490, Z690, B560, H570, Q570 series) |
| Reference | Binarly BRLY-2025-009, CERT VU#746790 |

---

## 3. TECHNICAL ARCHITECTURE

### 3.1 Persistence Mechanism

The implant resides in the SPI flash chip's DXE firmware volume. It exploits CVE-2025-7029, a vulnerability in Gigabyte's `OverClockSmiHandler` SMI handler, to gain code execution during the DXE phase:

```
Power On → SEC → PEI → DXE (IMPLANT EXECUTES HERE) → BDS → OS Boot
                        ↑
                  Ring -2 — before any OS code loads
```

### 3.2 Multi-Agent Deployment

```
AGENT-0 (Firmware):        AGENT-1 (Kernel):        AGENT-2 (Userland):
┌──────────────────┐       ┌──────────────────┐     ┌──────────────────┐
│ SPI Flash Resident│       │ Kernel Module    │     │ Userland Daemon  │
│ DXE Driver        │──────▶│ Memory Exfil     │────▶│ Crypto Mining    │
│ Deadman Switch    │       │ Keylogging       │     │ C2 Relay         │
│ UEFI Network Init │       │ Netfilter Hooks  │     │ Token Harvesting │
└──────────────────┘       └──────────────────┘     └──────────────────┘
```

### 3.3 Cross-Platform Support

The DXE driver is OS-agnostic. It deploys platform-specific agents:
- **Windows**: Task Scheduler + COM handler (CLSID: `{F576B2F9-7850-4226-ADB0-E5993FED4F02}`)
- **Linux**: Kernel module + systemd service
- **macOS**: LaunchDaemon + T2 bypass via EFI partition

### 3.4 C2 Architecture

Dual-channel command and control:
- **Primary**: Encrypted TCP tunnels to cloud infrastructure
- **Secondary**: Memory-mapped exfiltration channel (data extracted via firmware-level DMA)
- **Fallback**: P2P mesh network (Session protocol, onion-routed)

---

## 4. DETECTION

### 4.1 Quick Check (Linux / Live USB)

```bash
# Check for bootkit UEFI entry
sudo efibootmgr | grep -i "VenHw"

# Check for EFI artifacts
sudo find /boot/efi -name "mach_kernel"
sudo find /boot/efi -name "SystemVersion.plist"

# Check for C2 traffic
ss -tunap | grep -E "4145|5678|1080"
```

**Any output = investigate immediately.**

### 4.2 Firmware IOCs

| Indicator | Value |
|-----------|-------|
| DXE Driver GUID | `99E275E7-75A0-4B37-A2E6-C5385E6C00CB` |
| UEFI Boot Entry | `VenHw(99E275E7-75A0-4B37-A2E6-C5385E6C00CB)` |
| EFI File | `/boot/efi/mach_kernel` (34 bytes) |
| EFI File | `/boot/efi/System/Library/CoreServices/SystemVersion.plist` |

### 4.3 Host IOCs

| Platform | Indicator |
|----------|-----------|
| Windows | Task: `\SoftLanding\*\SoftLandingCreativeManagementTask` |
| Windows | Registry: `HKLM\...\TaskCache\Tree\SoftLanding` |
| Windows | CLSID: `{F576B2F9-7850-4226-ADB0-E5993FED4F02}` |
| All | Process: `SoftLandingTask.exe` (camouflaged) |

### 4.4 Dropper Hash

```
SHA256: D8D807F731D4ACA5F6DE0F09EFCCFDCFFFF4082187458557F10FB2BEEB35A5C4
Filename: VSCodeUserSetup-x64-1.113.0.exe (139MB)
```

---

## 5. REMEDIATION

### 5.1 Required Actions

| Priority | Action | Effort |
|----------|--------|--------|
| **CRITICAL** | Physical SPI flash reprogramming (CH341A) | Hardware |
| HIGH | Clean OS installation (air-gapped) | 2-4 hours |
| HIGH | Update Gigabyte firmware to patched version | 30 min |
| MEDIUM | Audit all UEFI boot entries | 15 min |
| LOW | Monitor for C2 traffic on blocked ports | Ongoing |

### 5.2 What Does NOT Work

- ❌ OS reinstallation — implant is below the OS
- ❌ Disk formatting — implant is in firmware, not on disk
- ❌ BIOS Q-Flash / OEM update tools — implant blocks firmware writes
- ❌ Antivirus / EDR — implant executes before security software loads

### 5.3 What DOES Work

- ✅ CH341A external programmer + SOIC8 clip (~$8 USD)
- ✅ flashrom v1.3+ with verified clean firmware
- ✅ Physical removal of SPI flash chip (destructive, last resort)

Full step-by-step guide: [REMEDIATION/CH341A_flash_guide.md](REMEDIATION/CH341A_flash_guide.md)

---

## 6. MITRE ATT&CK MAPPING

| Tactic | Technique | ID |
|--------|-----------|-----|
| Persistence | Pre-OS Boot: System Firmware | T1542.001 |
| Defense Evasion | Hijack Execution Flow: DLL Side-Loading | T1574.002 |
| Credential Access | OS Credential Dumping | T1003 |
| Command & Control | Encrypted Channel | T1573 |
| Command & Control | Proxy: Multi-hop Proxy | T1090.003 |
| Exfiltration | Exfiltration Over C2 Channel | T1041 |
| Impact | Resource Hijacking | T1496 |

---

## 7. CAMPAIGN CONTEXT

SoftLanding is **Layer 0** of the TeamPCP campaign — a multi-layer attack stack spanning firmware, VPN exploitation, overlay network hijacking, ML-driven adaptation, and cloud-based C2 infrastructure.

---

## 8. REFERENCES

| Source | Detail |
|--------|--------|
| CVE-2025-7029 | Gigabyte OverClockSmiHandler SMM Corruption |
| Binarly BRLY-2025-009 | Original vulnerability discovery |
| CERT VU#746790 | SMM callout vulnerabilities |
| CVE-2026-45321 | GitHub Actions OIDC Token Hijacking |

---

## 9. DISCLOSURE TIMELINE

| Date | Event |
|------|-------|
| June 2026 | Discovered during threat hunting operations |
| June 28, 2026 | Technical advisory filed (ASPHYNX-2026-0001) |
| June 29, 2026 | Public documentation released |

---

> *"The enemy is not in your OS. It is in your firmware."*

— ASPHYNX RESEARCH, June 2026
