# Vulnerability Assessment Findings

## 1. Assessment Overview

This document records the vulnerabilities identified on an intentionally vulnerable Ubuntu Server 22.04.1 LTS virtual machine and the results of subsequent remediation and verification.

The assessment was conducted in an isolated VirtualBox lab using Nessus Essentials, Nmap, Lynis, and manual configuration checks.

### Assessment Details

| Metric | Baseline | After Remediation |
|---|---|---|
| Target | Ubuntu Server 22.04.1 LTS | Ubuntu Server 22.04 LTS, updated packages |
| IP address | 192.168.56.20 | 192.168.56.20 |
| Scanner | Nessus Essentials 10.12.5 | Nessus Essentials 10.12.5 |
| Scan | Ubuntu-Final-Baseline-Credentialed | Ubuntu-After-Remediation-Credentialed |
| Credentialed checks | Successful (`soclab` via SSH) | Successful (`soclab` via SSH) |
| Grouped finding entries | 52 | 42 |
| Lynis hardening index | 56/100 | 61/100 |
| Running kernel | 5.15.0-43-generic | 5.15.0-198-generic |
| Open TCP ports (Nmap) | 4 | 1 |

The post-remediation Nessus scan reported 42 informational entries and no Critical, High, Medium, or Low findings.

## 2. Prioritized Baseline Findings

| Priority | Vulnerability | Baseline Severity | Post-Remediation Status |
|---|---|---|---|
| P1 | Outdated Ubuntu kernel (USN-8255-1) | Critical — CVSS 9.8 | No longer detected in authenticated Nessus scan |
| P1 | Root SSH login permitted | High (assessed) | Disabled; manually verified |
| P1 | Anonymous FTP access enabled | High (assessed) | Disabled; manually verified |
| P2 | SMB shares accessible without authentication | High — CVSS 7.5 | Guest access disabled; access denial verified before firewall activation |
| P2 | SMB signing not required | Medium — CVSS 5.3 | SMB signing required; configuration verified |
| P2 | Host firewall disabled | Medium (assessed) | UFW enabled with default-deny incoming policy |
| P2 | SSH Terrapin prefix truncation weakness | Medium — CVSS 5.9 | No longer reported in final scan; OpenSSH packages updated |

*Assessed severities are lab risk assessments, not scanner-assigned CVSS scores.*

The absence of a finding in the final scan does not independently establish that every underlying vulnerability has been eliminated. Some network checks were affected by the newly enabled firewall.

## 3. Detailed Technical Findings

### Finding 1 — Outdated Ubuntu Kernel

**Priority:** P1  
**Baseline severity:** Critical — CVSS 9.8  
**Advisory:** USN-8255-1  
**Nessus plugin:** 314212

**Baseline observations:**
- Running kernel: `5.15.0-43-generic`
- Nessus identified critical vulnerabilities associated with the outdated kernel.

**Security risk:**

Unpatched kernel vulnerabilities can expose a system to exploitation, depending on the affected component and attack prerequisites.

**Remediation:**
- Updated Ubuntu packages using APT.
- Installed the updated kernel package.
- Rebooted into the patched kernel.

**Verification:**
- Running kernel confirmed as `5.15.0-198-generic`.
- Final Nessus scan successfully authenticated over SSH.
- Searching the final scan for `USN-8255-1` returned zero results.

**Status:** No longer detected after remediation.

### Finding 2 — Permissive SSH Authentication

**Priority:** P1  
**Baseline severity:** High (assessed)

**Baseline configuration:**

```text
permitrootlogin yes
passwordauthentication yes
```

**Security risk:**

Permitting direct root login and password-based SSH authentication increases exposure to credential attacks and unauthorized administrative access.

**Remediation:**
- Disabled root SSH login.
- Disabled SSH password authentication.
- Disabled keyboard-interactive authentication.
- Preserved SSH public-key authentication.

**Verification:**

```text
permitrootlogin no
passwordauthentication no
kbdinteractiveauthentication no
```

SSH public-key authentication continued to function, including for the final credentialed Nessus scan.

**Status:** Remediated and manually verified.

### Finding 3 — Anonymous FTP Access

**Priority:** P1  
**Baseline severity:** High (assessed)

**Baseline configuration:**

```text
anonymous_enable=YES
anon_root=/srv/ftp
```

**Security risk:**

Anonymous FTP access can expose files to unauthenticated users.

**Remediation:**

Changed the FTP configuration to:

```text
anonymous_enable=NO
```

**Verification:**
- Confirmed the updated configuration.
- Anonymous FTP authentication was denied during validation.
- UFW subsequently restricted access to the FTP port.

**Status:** Remediated and manually verified.

### Finding 4 — Unauthenticated Samba Share Access

**Priority:** P2  
**Baseline severity:** High — CVSS 7.5

**Baseline observations:**
- Share: `soc-lab`
- Guest access enabled.

**Security risk:**

Guest-accessible SMB shares may expose files to unauthorized users.

**Remediation:**
- Disabled guest access to the Samba share.
- Restricted network exposure through UFW.

**Verification:**
- An unauthenticated SMB access attempt returned `NT_STATUS_ACCESS_DENIED` before firewall activation.
- Following firewall activation, SMB connections from Kali were filtered.

**Status:** Guest access disabled and validated; SMB additionally protected by the firewall.

### Finding 5 — SMB Signing Not Required

**Priority:** P2  
**Baseline severity:** Medium — CVSS 5.3

**Baseline observation:**

SMB signing was not required.

**Security risk:**

Optional SMB signing can increase exposure to certain relay or man-in-the-middle attacks under suitable conditions.

**Remediation:**

Configured Samba to require SMB signing.

**Verification:**

```text
server signing = required
```

An Nmap SMB2 security-mode check before firewall activation confirmed that message signing was enabled and required.

**Status:** Remediated and verified.

### Finding 6 — Inactive Host Firewall

**Priority:** P2  
**Baseline severity:** Medium (assessed)

**Baseline configuration:**

```text
Status: inactive
```

**Security risk:**

Without host-level inbound traffic filtering, unnecessary services may be reachable from connected networks.

**Remediation:**
- Enabled UFW.
- Set the default incoming policy to deny.
- Preserved required SSH access from the designated Kali and Windows management hosts.

**Verification:**

```text
Status: active
Default: deny (incoming), allow (outgoing)
22/tcp ALLOW IN 192.168.56.102
22/tcp ALLOW IN 192.168.56.1
```

Final Nmap results showed only SSH port 22 open, with the remaining 999 scanned TCP ports filtered.

**Status:** Remediated and verified.

### Finding 7 — SSH Terrapin Weakness

**Priority:** P2  
**Baseline severity:** Medium — CVSS 5.9  
**CVE:** CVE-2023-48795

**Baseline observation:**

Nessus identified an SSH Terrapin-related weakness.

**Security risk:**

Certain SSH protocol implementations and algorithm combinations may be vulnerable to prefix truncation attacks.

**Remediation:**
- Installed available Ubuntu security updates, including OpenSSH package updates.

**Verification:**
- The final authenticated Nessus scan reported no Medium findings.
- No separate post-update SSH algorithm negotiation test was recorded.

**Status:** No longer reported by Nessus; independent protocol-level validation was not performed.

## 4. Before-and-After Results

| Metric | Before | After | Change |
|---|---|---|---|
| Nessus grouped entries | 52 | 42 | 10 fewer entries |
| Critical/High/Medium/Low Nessus findings | Present | None reported | Improved scan results |
| Lynis hardening index | 56/100 | 61/100 | +5 points |
| Open TCP ports | 4 | 1 | 75% reduction |
| Critical kernel advisory USN-8255-1 | Detected | Not detected | Improved |
| Credentialed scan authentication | Successful | Successful | Maintained |

The reduction in Nessus entries is a comparison of grouped findings, not a count of individual CVEs fixed.

Both scans authenticated successfully, but the results also reflect changed network reachability after UFW activation.

## 5. Evidence and Artifacts

### Baseline — Day 3

- [Baseline Assessment Screenshots](evidence/day-3/)
- Nmap output: `nmap-before-final.txt`
- Lynis audit log: `lynis-baseline.log`
- Lynis report: `lynis-baseline-report.dat`
- Manual validation: `manual-validation-before.txt`

### Post-Remediation — Day 4

- [Remediation and Verification Screenshots](evidence/day-4/)
- Nmap output: `nmap-after-final.txt`
- Lynis audit log: `lynis-after.log`
- Lynis report: `lynis-after-report.dat`
- Manual validation: `manual-validation-after.txt`

The command-output artifacts listed above were generated within the lab. Their filenames are documented for reproducibility; they are not necessarily all uploaded to this repository.

The original and final Nessus scans remain available in the scanner. Scan results were documented through screenshots because report export was unavailable in this installation.

## 6. Remaining Risks and Limitations

- The Lynis hardening index remains 61/100, indicating additional hardening opportunities.
- Lynis reported that no malware scanner was installed.
- Some services remain installed even though inbound access is blocked by UFW.
- The final Nessus scan reported no non-informational findings, but this does not guarantee the absence of vulnerabilities.
- Firewall restrictions can affect network-based vulnerability detection.
- The assessment was performed in an isolated lab and does not represent a production security assessment.

## 7. Conclusion

The project demonstrated a complete vulnerability-management workflow: identifying and prioritizing weaknesses, applying security patches and configuration changes, and validating the resulting security posture through authenticated scanning and independent manual checks.

The strongest verified outcomes were the updated running kernel, disabled insecure authentication methods, restricted network exposure, improved Lynis hardening index, and disappearance of the previously identified critical kernel finding from the final authenticated Nessus scan.
