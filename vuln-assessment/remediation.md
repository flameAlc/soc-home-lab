# Vulnerability Remediation and Verification

## 1. Objective

Remediate the security weaknesses identified during the Day 3 assessment of an intentionally vulnerable Ubuntu Server 22.04.1 LTS virtual machine, then repeat vulnerability scanning and manual validation to measure improvements.

The remediation process focused on patch management, SSH authentication, FTP and Samba security, host firewall configuration, and verification of the resulting security posture.

## 2. Assessment Environment

| Component | Details |
|---|---|
| Target | Ubuntu Server 22.04 LTS |
| Target IP | `192.168.56.20` |
| Scanner | Kali Linux — `192.168.56.102` |
| Virtualization | Oracle VirtualBox |
| Vulnerability scanner | Nessus Essentials 10.12.5 |
| Additional tools | Nmap, Lynis, UFW |
| Authentication | SSH public key as `soclab`, with sudo privilege escalation |
| Network | Isolated VirtualBox host-only network |

Temporary NAT connectivity was enabled to install Ubuntu security updates and removed after patching.

## 3. Baseline and Final Results

| Security Metric | Before Remediation | After Remediation |
|---|---|---|
| Nessus grouped finding entries | 52 | 42 |
| Nessus Critical/High/Medium/Low findings | Present | 0 reported |
| Lynis hardening index | 56/100 | 61/100 |
| Running kernel | `5.15.0-43-generic` | `5.15.0-198-generic` |
| Nmap open TCP ports | 4 | 1 |
| SSH root login | Permitted | Disabled |
| SSH password authentication | Enabled | Disabled |
| SSH keyboard-interactive authentication | Enabled | Disabled |
| Anonymous FTP | Enabled | Disabled |
| Samba guest access | Enabled | Disabled |
| SMB signing required | No | Yes |
| UFW firewall | Inactive | Active |
| SSH credentialed Nessus checks | Successful | Successful |

The final Nessus scan reported 42 informational entries. Both baseline and final scans successfully authenticated as `soclab` over SSH.

## 4. Completed Remediation Checklist

- [x] Update vulnerable Ubuntu packages and kernel.
- [x] Disable SSH root login and password authentication.
- [x] Disable SSH keyboard-interactive authentication.
- [x] Disable anonymous FTP access.
- [x] Restrict Samba guest access and require SMB signing.
- [x] Configure and enable UFW while preserving authorized SSH access.
- [x] Verify the new running kernel and security configurations.
- [x] Run an authenticated Nessus rescan.
- [x] Run Nmap service discovery again.
- [x] Run Lynis again and compare hardening scores.
- [x] Record before-and-after results with supporting evidence.
- [x] Remove temporary NAT connectivity to restore lab isolation.

## 5. Remediation Procedures

### 5.1 Ubuntu Package and Kernel Updates

The baseline system was running kernel `5.15.0-43-generic`, and Nessus identified critical kernel-related security findings.

Temporary NAT connectivity was enabled to access Ubuntu package repositories.

The system was updated using APT:

```bash
sudo apt update
sudo apt upgrade
```

The upgrade installed 333 upgraded packages and 17 new packages, including an updated Linux kernel.

The system was rebooted to activate the updated kernel.

**Verification:**

```bash
uname -r
sudo dpkg --audit
```

Confirmed running kernel:

```text
5.15.0-198-generic
```

No incomplete package installations were reported.

The previously detected critical kernel finding, USN-8255-1 (Nessus plugin 314212, CVSS 9.8), was no longer detected in the final authenticated Nessus scan.

**Status:** Completed and verified.

### 5.2 SSH Authentication Hardening

The baseline SSH configuration permitted root login and password-based authentication.

Updated the SSH server configuration and local override file to disable insecure authentication methods while preserving public-key access.

**Effective configuration:**

```text
permitrootlogin no
passwordauthentication no
kbdinteractiveauthentication no
```

**Verification command:**

```bash
sudo sshd -T | grep -E '^(permitrootlogin|passwordauthentication|kbdinteractiveauthentication) '
```

**Validation:**

- Password-only SSH authentication was rejected.
- SSH public-key authentication remained functional.
- The final Nessus scan authenticated successfully as `soclab` via SSH.

**Status:** Completed and verified.

### 5.3 Anonymous FTP Remediation

The baseline FTP configuration permitted anonymous authentication.

Changed the vsftpd configuration to:

```text
anonymous_enable=NO
```

**Verification command:**

```bash
sudo grep -E '^anonymous_enable=' /etc/vsftpd.conf
```

An anonymous FTP access attempt was rejected during validation.

Following UFW activation, the FTP port was no longer accessible from the Kali scanner.

**Status:** Completed and verified.

### 5.4 Samba Guest Access Hardening

The baseline Samba share `soc-lab` permitted guest access.

Updated the share configuration to disable unauthenticated access.

**Validation:**

An unauthenticated Samba connection attempt returned:

```text
NT_STATUS_ACCESS_DENIED
```

This test was performed before firewall activation to distinguish access-control enforcement from network filtering.

Following UFW activation, inbound SMB traffic from Kali was filtered.

**Status:** Completed and verified.

### 5.5 SMB Signing Enforcement

The baseline Samba configuration did not require SMB signing.

Updated Samba to require signing.

**Verification command:**

```bash
sudo testparm -s
```

**Effective setting:**

```text
server signing = required
```

An Nmap SMB2 security-mode test performed before firewall activation confirmed that SMB message signing was enabled and required.

**Status:** Completed and verified.

### 5.6 UFW Firewall Configuration

The baseline system had an inactive UFW firewall.

Enabled UFW with a default-deny incoming policy while preserving authorized SSH access.

**Final firewall configuration:**

```text
Status: active
Default: deny (incoming), allow (outgoing)

22/tcp ALLOW IN 192.168.56.102
22/tcp ALLOW IN 192.168.56.1
```

The permitted addresses correspond to the Kali scanner and Windows host used for lab management.

**Verification command:**

```bash
sudo ufw status verbose
```

SSH public-key access from the authorized Kali system remained functional after firewall activation.

**Status:** Completed and verified.

### 5.7 Restore Lab Network Isolation

Temporary NAT access was used for package installation and updates.

After completing the upgrade:

- Shut down the Ubuntu VM.
- Disabled the temporary NAT adapter in VirtualBox.
- Restarted the Ubuntu VM.
- Verified that the host-only interface remained active.
- Confirmed that no default route remained.

The Ubuntu server retained its host-only IP address:

```text
192.168.56.20/24
```

**Status:** Completed and verified.

## 6. Post-Remediation Verification

### 6.1 Nmap Service Discovery

**Command executed on Kali:**

```bash
nmap -sV 192.168.56.20 -oN ~/nmap-after-final.txt
```

**Baseline open ports:**

- TCP 21 — FTP
- TCP 22 — SSH
- TCP 139 — NetBIOS
- TCP 445 — SMB

**Final scan:**

```text
Not shown: 999 filtered tcp ports

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.17
```

The number of accessible TCP services decreased from four to one, a 75% reduction.

FTP and SMB services were blocked from the Kali scanner by UFW. This does not mean those services were uninstalled.

### 6.2 Lynis Security Audit

**Final audit command:**

```bash
sudo lynis audit system --log-file /home/soclab/lynis-after.log --report-file /home/soclab/lynis-after-report.dat
```

**Results:**

| Metric | Before | After |
|---|---|---|
| Hardening index | 56/100 | 61/100 |
| Improvement | — | +5 points |

The final Lynis audit performed 254 tests.

Lynis confirmed the presence of a firewall but identified the absence of a malware scanner as a remaining hardening opportunity.

**Preserved artifacts:**

- `lynis-baseline.log`
- `lynis-baseline-report.dat`
- `lynis-after.log`
- `lynis-after-report.dat`

### 6.3 Nessus Authenticated Rescan

**Baseline scan:**

`Ubuntu-Final-Baseline-Credentialed`

**Final scan:**

`Ubuntu-After-Remediation-Credentialed`

The final scan used the same target and copied baseline scan configuration, including SSH credentials.

**Authentication verification — Nessus plugin 19506:**

```text
Credentialed checks : yes, as 'soclab' via ssh
```

**Results:**

| Metric | Baseline | Final |
|---|---|---|
| Grouped finding entries | 52 | 42 |
| Critical/High/Medium/Low findings | Present | 0 |
| SSH credentialed checks | Yes | Yes |

The final scan reported 42 informational entries.

A search for the previously detected critical kernel advisory `USN-8255-1` returned zero results.

The reduction in reported findings reflects patching, configuration changes, and reduced network exposure. It should not be interpreted as a direct count of vulnerabilities eliminated.

### 6.4 Manual Configuration Verification

The following checks were performed on Ubuntu:

```bash
sudo sshd -T
sudo grep -E '^anonymous_enable=' /etc/vsftpd.conf
sudo testparm -s
sudo ufw status verbose
uname -r
```

The final configuration confirmed:

- Root SSH login disabled.
- SSH password authentication disabled.
- Keyboard-interactive SSH authentication disabled.
- Anonymous FTP disabled.
- SMB signing required.
- UFW active with default-deny incoming policy.
- Running kernel updated to `5.15.0-198-generic`.

**Manual validation artifacts:**

- `manual-validation-before.txt`
- `manual-validation-after.txt`

Both files were preserved separately.

## 7. Evidence

### Day 3 — Baseline

[View Baseline Assessment Screenshots](evidence/day-3/)

Evidence includes the initial service exposure, vulnerability findings, Lynis hardening index, and vulnerable configuration.

### Day 4 — Remediation

[View Remediation and Verification Screenshots](evidence/day-4/)

Evidence includes:

1. Kernel, SSH, and UFW validation.
2. Final Nmap service discovery.
3. Final Lynis audit.
4. Nessus credentialed authentication verification.
5. Nessus post-remediation host summary.
6. Confirmation that the critical kernel finding was no longer detected.

## 8. Remaining Risks and Limitations

- Lynis reported a final hardening index of 61/100, leaving further hardening opportunities.
- No malware scanner was installed during this exercise.
- FTP and SMB services may remain installed despite being inaccessible from Kali.
- Firewall filtering reduces external reachability and can affect network-based vulnerability checks.
- The final Nessus scan reported no non-informational findings, but this does not guarantee that the host is free of vulnerabilities.
- This was an isolated lab assessment, not a production penetration test.

## 9. Final Status

**Remediation and verification completed.**

The project demonstrated a full vulnerability-management workflow, from identifying critical weaknesses to applying patches and security configuration changes, then validating the results through authenticated vulnerability scanning, service discovery, Lynis auditing, and manual checks.

The most significant verified outcomes were the updated Linux kernel, successful removal of insecure authentication methods, restricted network exposure, improved hardening index, and disappearance of the previously reported critical kernel finding from the final Nessus scan.
