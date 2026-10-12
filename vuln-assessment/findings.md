# Vulnerability Assessment Findings

## Assessment Baseline

| Metric | Result |
|---|---|
| Target | Ubuntu Server 22.04.1 LTS |
| IP address | 192.168.56.20 |
| Scanner | Nessus Essentials 10.12.5 |
| Scan | Ubuntu-Final-Baseline-Credentialed |
| Credentialed checks | Successful (soclab via SSH) |
| Grouped vulnerability entries | 52 |
| Lynis hardening index | 56/100 |
| Running kernel | 5.15.0-43-generic |

The assessment was conducted in an isolated VirtualBox lab. Findings were identified using Nessus, Nmap, Lynis, and manual validation.

## Prioritized Findings

| Priority | Vulnerability | Severity | Evidence |
|---|---|---|---|
| P1 | Outdated Ubuntu kernel (USN-8255-1) | Critical — CVSS 9.8 | Nessus credentialed scan |
| P1 | Root SSH login permitted | High (assessed) | SSH configuration validation |
| P1 | Anonymous FTP access enabled | High (assessed) | FTP configuration and access testing |
| P2 | SMB shares accessible without authentication | High — CVSS 7.5 | Nessus and Samba access testing |
| P2 | SMB signing not required | Medium — CVSS 5.3 | Nessus credentialed scan |
| P2 | Host firewall disabled | Medium (assessed) | UFW configuration validation |
| P2 | SSH Terrapin prefix truncation weakness | Medium — CVSS 5.9 | Nessus scan |

*Assessed severities are lab risk assessments, not scanner-assigned CVSS scores.*

## Key Technical Findings

### 1. Outdated Ubuntu Kernel

- **Advisory:** USN-8255-1
- **Nessus Plugin:** 314212
- **CVSS:** 9.8
- **Running kernel:** 5.15.0-43-generic
- **Risk:** The outdated kernel is affected by security vulnerabilities addressed in later Ubuntu security updates.
- **Recommended remediation:** Install applicable Ubuntu kernel security updates and reboot into the patched kernel.

### 2. Permissive SSH Configuration

- `PermitRootLogin yes`
- `PasswordAuthentication yes`
- **Risk:** Direct root authentication and password-based access increase exposure to credential attacks.
- **Recommended remediation:** Disable root SSH login and use SSH key authentication for authorized users.

### 3. Anonymous FTP Access

- `anonymous_enable=YES`
- `anon_root=/srv/ftp`
- **Risk:** Unauthenticated users can access files exposed by the FTP service.
- **Recommended remediation:** Disable anonymous FTP access.

### 4. Unauthenticated Samba Share Access

- Share: `soc-lab`
- `guest ok = Yes`
- **Risk:** The share permits guest access, potentially exposing files to unauthorized users.
- **Recommended remediation:** Disable guest access and restrict share permissions.

### 5. SMB Signing Not Required

- **CVSS:** 5.3
- **Risk:** Optional SMB signing can permit certain relay or man-in-the-middle attacks under suitable conditions.
- **Recommended remediation:** Require SMB signing where supported.

### 6. Inactive Host Firewall

- `ufw status: inactive`
- **Risk:** The host lacks UFW-enforced inbound traffic restrictions.
- **Recommended remediation:** Configure an allowlist for required management traffic and enable UFW.

### 7. SSH Terrapin Weakness

- **CVE:** CVE-2023-48795
- **CVSS:** 5.9
- **Risk:** Certain SSH protocol configurations may be vulnerable to prefix truncation attacks.
- **Recommended remediation:** Apply relevant OpenSSH security updates and verify supported algorithms.

## Baseline Evidence

The following artifacts were preserved:

- Nessus baseline and vulnerability screenshots
- Nmap service discovery: `nmap-before-final.txt`
- Lynis audit log: `lynis-baseline.log`
- Lynis report: `lynis-baseline-report.dat`
- Manual configuration validation: `manual-validation-before.txt`

The Nessus baseline remains available in the scanner. Nessus Essentials does not provide scan export functionality in this installation.

## Remediation Verification Plan

Following remediation, the assessment will be repeated using authenticated Nessus scanning, Nmap service discovery, Lynis auditing, and manual configuration checks.

Before-and-after results will be compared to confirm that security controls were applied and that the scanner retained authenticated visibility.
