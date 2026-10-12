# Ubuntu Server Vulnerability Assessment and Remediation

## 1. Project Overview

Conducted an end-to-end vulnerability assessment and remediation exercise on an intentionally vulnerable Ubuntu Server 22.04.1 LTS virtual machine in an isolated VirtualBox cybersecurity lab.

The project involved identifying vulnerabilities using Nmap, Nessus Essentials, Lynis, and manual configuration checks; applying security hardening and system updates; and performing post-remediation scans to validate the changes.

**Target:** `192.168.56.20`  
**Operating System:** Ubuntu Server 22.04 LTS  
**Assessment Tools:** Nessus Essentials, Nmap, Lynis  
**Environment:** VirtualBox, Kali Linux, Ubuntu Server  
**Assessment Type:** Vulnerability Assessment, Remediation, and Validation

## 2. Initial Vulnerability Assessment

The baseline assessment identified several security weaknesses:

- Outdated Linux kernel with reported critical vulnerabilities.
- Root SSH login and password authentication enabled.
- Anonymous FTP access enabled.
- Unauthenticated Samba share access.
- SMB signing not required.
- Inactive UFW host firewall.

The final baseline Nessus scan, `Ubuntu-Final-Baseline-Credentialed`, successfully authenticated over SSH and reported 52 grouped finding entries.

The initial Lynis hardening index was 56/100.

## 3. Remediation Activities

Implemented the following security improvements:

- Updated Ubuntu packages and installed a patched Linux kernel.
- Disabled root SSH login, password authentication, and keyboard-interactive authentication.
- Preserved SSH public-key authentication for authorized access.
- Disabled anonymous FTP access.
- Disabled Samba guest access and required SMB signing.
- Enabled UFW with a default-deny incoming policy.
- Restricted SSH access to designated lab management hosts.
- Removed temporary internet connectivity after system updates to restore lab isolation.

## 4. Post-Remediation Validation

Repeated the security assessment using Nmap, Lynis, Nessus Essentials, and manual configuration checks.

The final Nessus scan, `Ubuntu-After-Remediation-Credentialed`, confirmed successful SSH authentication as `soclab`.

### Before and After Comparison

| Security Metric | Before Remediation | After Remediation |
|---|---|---|
| Nessus grouped finding entries | 52 | 42 |
| Nessus Critical/High/Medium/Low findings | Vulnerabilities reported | 0 reported |
| Lynis hardening index | 56/100 | 61/100 |
| Open TCP ports detected by Nmap | 4 | 1 |
| Running Linux kernel | 5.15.0-43-generic | 5.15.0-198-generic |
| Root SSH login | Enabled | Disabled |
| SSH password authentication | Enabled | Disabled |
| Anonymous FTP | Enabled | Disabled |
| Samba guest access | Enabled | Disabled |
| SMB signing | Not required | Required |
| UFW firewall | Inactive | Active |

The previously identified critical kernel finding, **USN-8255-1 (Nessus plugin 314212, CVSS 9.8)**, was no longer detected in the final authenticated scan.

The reduction in reported findings reflects the combined effects of patching, configuration hardening, and reduced network exposure. It does not establish that the system is free of all vulnerabilities.

## 5. Evidence

Screenshots are organized by assessment phase:

- [Day 3 — Baseline Assessment](evidence/day-3/)
- [Day 4 — Remediation and Verification](evidence/day-4/)

Evidence includes baseline vulnerability findings, authenticated scan results, Lynis assessments, Nmap scans, remediation verification, and post-remediation vulnerability results.

## 6. Project Documentation

- [Detailed Findings](findings.md)
- [Remediation and Validation](remediation.md)

## 7. Key Skills Demonstrated

- Vulnerability assessment and prioritization
- Authenticated vulnerability scanning
- Linux security hardening
- SSH and firewall configuration
- Network service exposure analysis
- Patch management
- Security control validation
- Before-and-after vulnerability reporting

## 8. Limitations

This assessment was conducted in an isolated virtual lab rather than a production environment. Nessus results depend on scan configuration, available plugins, authentication, and network reachability. The final scan's absence of non-informational findings should not be interpreted as proof that no vulnerabilities remain.
