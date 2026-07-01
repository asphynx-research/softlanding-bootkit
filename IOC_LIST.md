# Indicator of Compromise Master List
## TeamPCP Campaign — TLP:WHITE — Public Release

Collected: June 2026 | Author: ASPHYNX RESEARCH

---

## FILE HASHES

### Droppers
```
SHA256: D8D807F731D4ACA5F6DE0F09EFCCFDCFFFF4082187458557F10FB2BEEB35A5C4
  Filename: VSCodeUserSetup-x64-1.113.0.exe (139MB)
  Description: Trojanized VSCode installer with embedded bootkit payload
```

### Camouflage Files
```
SHA256: c807715cefbbc9573f7382dbc3ad253c3afe46eadd59e58b69b113f07e0c3bf5
  Note: Legitimate Microsoft file. Malware hides alongside it.
```

---

## UEFI FIRMWARE IOCs

| Type | Value |
|------|-------|
| DXE Driver GUID | `99E275E7-75A0-4B37-A2E6-C5385E6C00CB` |
| COM Handler GUID (Win) | `{F576B2F9-7850-4226-ADB0-E5993FED4F02}` |
| UEFI Boot Entry | `VenHw(99E275E7-75A0-4B37-A2E6-C5385E6C00CB)` |
| EFI File | `/boot/efi/mach_kernel` (34 bytes) |
| EFI File | `/boot/efi/System/Library/CoreServices/SystemVersion.plist` |

---

## WINDOWS IOCs

### Task Scheduler
```
\SoftLanding\S-1-5-21-*\SoftLandingCreativeManagementTask
```

### Registry
```
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tree\SoftLanding
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\AppModel\StateRepository\Cache\*\*SoftLanding*
HKCU\Software\Microsoft\Windows NT\CurrentVersion\HostActivityManager\CommitHistory\MicrosoftWindows.Client.CBS_cw5n1h2txyewy!SoftLanding
HKCU\Software\Classes\Local Settings\Software\Microsoft\Windows\CurrentVersion\AppModel\SystemAppData\MicrosoftWindows.Client.CBS_cw5n1h2txyewy\HAM\AUI\SoftLanding
```

### Process
```
SoftLandingTask.exe  (camouflaged as legitimate ContentDeliveryManager)
```

---

## C2 INFRASTRUCTURE

### Primary C2 (83.142.209.0/24)
```
83.142.209.194      # Primary C2
83.142.209.11       # Attack wave
83.142.209.203      # Attack wave
83.142.209.120      # Brute force host
83.142.209.0/24     # BLOCK ENTIRE SUBNET
```

### Additional IPs
```
185.95.159.32       # C2 domain host
85.209.11.79        # APT affiliate infrastructure
```

### Domains
```
t.m-kosche[.]com
git-tanstack[.]com
filev2.getsession[.]org
ghostynetworks.com
merezha.net
```

### Proxy Relay Layer (90+ nodes — partial list)
```
190.153.121.2:4145     177.101.135.89:5678     77.75.230.115:4249
103.113.71.230:1080    68.71.254.6:4145        162.214.102.121:52730
130.193.123.34:5678    142.54.232.6:4145        202.43.182.3:4153
202.145.11.220:5678    200.91.251.180:3629      154.12.178.107:29985
103.111.160.41:5678    91.201.240.84:5678       193.158.12.138:4153
98.178.72.21:10919     194.8.232.46:4153        46.182.6.69:38780
46.98.184.203:5678     103.82.11.209:4153       24.249.199.4:4145
159.223.166.21:5078    91.213.119.246:46024     72.217.216.239:4145
174.77.111.196:4145    95.143.12.201:60505      125.227.169.85:38157
185.109.184.150:49319  95.178.108.189:5678      193.105.62.11:58973
103.164.190.221:5430   138.0.60.19:1080         51.158.108.165:16379
45.184.183.39:4145     128.199.196.31:56619     138.197.92.110:9095
51.158.111.76:16379    184.168.121.153:27659    167.86.102.169:18176
23.105.170.33:45951    162.255.110.52:5678      183.81.157.65:5678
172.96.117.205:38001   143.137.148.112:5678     129.153.42.81:3128
212.115.232.79:10800   202.86.138.18:8080       35.185.196.38:3128
193.122.98.1:3128      197.232.47.122:5678      132.148.128.8:22115
129.80.134.71:3128     51.89.173.40:17982       182.53.216.73:4145
51.15.242.202:80       51.254.69.243:80         217.76.154.132:80
185.51.10.19:80        45.92.108.112:80         212.32.226.234:80
51.75.122.80:80        202.86.138.18:8080       51.145.176.250:8080
128.199.104.93:8000    129.126.99.254:4153      14.103.26.198:8000
178.176.193.56:1080    51.77.64.139:8081        170.247.43.142:32812
208.87.130.154:80      34.154.161.152:80        82.223.102.92:80
88.202.230.103:8896    162.159.26.231:80        173.255.232.166:80
51.15.242.202:8888     112.30.155.83:12792      8.222.152.158:55555
45.95.203.149:4444     207.180.198.165:20242    220.248.70.237:9002
67.43.236.18:16417     191.252.222.91:80        82.223.102.92:9443
45.90.218.210:4444     45.95.203.115:4444       86.100.63.127:4145
66.45.246.194:8888     92.205.61.38:12817       45.90.219.39:4444
72.10.164.178:13967    116.63.129.202:6000      45.144.65.14:4444
178.128.113.118:23128
```

---

## HOSTING INFRASTRUCTURE — ASNs TO BLOCK

```
AS205759    # Threat actor shell company (Kentucky, USA)
AS202412    # Threat actor shell company (Seychelles)
```

---

## AFFECTED HARDWARE

### Confirmed
- Gigabyte Z590 AORUS MASTER (rev 1.0) — Firmware F9, F10

### Potentially Affected (240+ models via Binarly BRLY-2025-009)
- Gigabyte Z590 Series: AORUS MASTER, ULTRA, ELITE, PRO, VISION G, VISION D, UD, GAMING X, AORUS XTREME
- Gigabyte Z490 Series (potentially)
- Gigabyte Z690 Series (potentially)
- Gigabyte B560, H570, Q570 Series (potentially)
- Any Gigabyte board with OverClockSmiHandler

**Reference:** https://www.binarly.io/advisories/brly-2025-009

---

## AFFECTED OPERATING SYSTEMS

- Windows 10 / Windows 11
- Linux (any distribution)
- macOS (any version)
- **ANY OS** — DXE driver executes before OS loads

---

## GITHUB DEAD-DROP PATTERNS

Repository naming patterns used by attackers:
- Repos named: "Shai-Hulud"
- Descriptions: "Sha1-Hulud: The Second Coming"
- Deadman switch token: "IfYouRevokeThisTokenItWillWipeTheComputerOfTheOwner"

---

## VULNERABILITIES EXPLOITED IN CAMPAIGN

| CVE | Severity | Component |
|-----|----------|-----------|
| CVE-2025-7029 | 8.2 (HIGH) | Gigabyte OverClockSmiHandler SMM |
| CVE-2026-45321 | 9.6 (CRITICAL) | GitHub Actions OIDC Hijacking |
| CVE-2025-31133 | — | runC Container Escape |

---

## CAMPAIGN SCOPE

- npm packages compromised: 796+
- GitHub repos affected: 5,561 (Megalodon)
- npm weekly downloads affected: 20,000,000+
- Microsoft-identified malicious domains: 150+
- Affected devices: 320,777+ (credential spraying target)

---

**Last Updated:** 29 June 2026 — ASPHYNX RESEARCH

> *Share IOCs freely. Block everything. Trust nothing.*
