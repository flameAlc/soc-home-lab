# Cybersecurity Home Lab Portfolio

Hands-on cybersecurity projects focused on SOC operations, threat detection, security monitoring, incident investigation, vulnerability assessment, and remediation.

This repository documents practical security testing in isolated virtual lab environments, including configuration, attack simulations, investigation findings, security hardening, validation, and supporting evidence.

## Projects

### Project 1: SOC Monitoring & Threat Detection

**Status:** Completed

**Technologies:** Splunk Enterprise, Sysmon, Windows 11, Kali Linux, Impacket, Wireshark, Splunk Universal Forwarder

Built a Windows-based SOC monitoring lab to collect endpoint security telemetry, simulate suspicious activity, investigate security events, and validate SIEM detections.

**Key activities:**

- Configured centralized Windows event monitoring using Splunk Enterprise and Universal Forwarder.
- Collected and analyzed Sysmon, Windows Security, and PowerShell logs.
- Simulated WMI-based remote command execution using Kali Linux and Impacket.
- Investigated encoded PowerShell activity and suspicious process execution.
- Analyzed failed network authentication attempts.
- Created and validated a scheduled Splunk alert for repeated failed logons.
- Mapped detection scenarios to MITRE ATT&CK techniques.
- Documented investigation evidence, detection logic, and incident timelines.

**Validated detection scenarios:**

| Detection | MITRE ATT&CK |
|---|---|
| WMI Remote Execution | T1047 |
| Encoded PowerShell | T1059.001, T1027 |
| Failed Network Logons | T1110.001 |

**Project documentation:**

- [View Project 1 Documentation](threat-detection/README.md)
- [Failed Network Logons Detection](detections/failed-network-logons.md)
- [WMI Remote Execution Detection](detections/wmi-remote-execution.md)
- [Encoded PowerShell Detection](detections/encoded-powershell.md)
- [Incident Timeline](threat-detection/incident-timeline.md)
- [Project Evidence](threat-detection/evidence/)

---

### Project 2: Vulnerability Assessment & Remediation

**Status:** Completed

**Technologies:** Nessus Essentials, Nmap, Lynis, Ubuntu Server 22.04 LTS, Kali Linux, SSH, UFW, Samba, vsftpd, VirtualBox

Conducted an end-to-end vulnerability assessment and remediation exercise on an intentionally vulnerable Ubuntu Server virtual machine.

Identified and prioritized security weaknesses, applied system patches and configuration hardening, and performed authenticated rescanning to validate remediation outcomes.

**Key activities:**

- Established an intentionally vulnerable Ubuntu Server baseline in an isolated VirtualBox environment.
- Performed network service discovery using Nmap.
- Conducted authenticated vulnerability assessments using Nessus Essentials.
- Audited Linux security configurations using Lynis.
- Identified critical kernel vulnerabilities, insecure SSH settings, anonymous FTP access, guest-accessible Samba shares, optional SMB signing, and inactive firewall protection.
- Applied Ubuntu security updates and kernel patches.
- Hardened SSH authentication, disabled anonymous FTP and Samba guest access, and enforced SMB signing.
- Configured UFW with a default-deny incoming policy and restricted SSH access.
- Repeated Nessus, Nmap, Lynis, and manual validation checks.
- Preserved before-and-after evidence and documented remediation outcomes.

**Verified results:**

| Security Metric | Before | After |
|---|---|---|
| Nessus grouped finding entries | 52 | 42 |
| Nessus Critical/High/Medium/Low findings | Present | None reported |
| Lynis hardening index | 56/100 | 61/100 |
| Open TCP ports detected by Nmap | 4 | 1 |
| Running Linux kernel | 5.15.0-43-generic | 5.15.0-198-generic |
| SSH root login | Enabled | Disabled |
| SSH password authentication | Enabled | Disabled |
| Anonymous FTP | Enabled | Disabled |
| Samba guest access | Enabled | Disabled |
| SMB signing | Not required | Required |
| UFW firewall | Inactive | Active |

The previously detected critical kernel vulnerability **USN-8255-1 (Nessus plugin 314212, CVSS 9.8)** was no longer reported in the final authenticated scan.

Nessus successfully authenticated as `soclab` over SSH in both the baseline and post-remediation scans.

The reduction in findings reflects patching, security hardening, and restricted network exposure. It does not establish that the system is free of vulnerabilities.

**Project documentation:**

- [View Project 2 Documentation](vuln-assessment/README.md)
- [Vulnerability Findings](vuln-assessment/findings.md)
- [Remediation and Verification Report](vuln-assessment/remediation.md)
- [Day 3 — Baseline Evidence](vuln-assessment/evidence/day-3/)
- [Day 4 — Remediation Evidence](vuln-assessment/evidence/day-4/)

---

## Skills Demonstrated

**SOC Operations & Threat Detection**
- Security Information and Event Management (SIEM)
- Windows Security Event Analysis
- Endpoint Threat Detection
- Security Alert Configuration
- Incident Investigation
- Process Tree Analysis
- MITRE ATT&CK Mapping

**Vulnerability Management**
- Authenticated Vulnerability Scanning
- Vulnerability Assessment and Prioritization
- Linux Security Hardening
- Patch Management
- SSH Security Configuration
- Firewall Configuration
- Remediation Verification
- Before-and-After Security Assessment

**Security Tools & Documentation**
- Splunk Enterprise and Sysmon
- Nessus Essentials, Nmap, and Lynis
- Wireshark and Impacket
- Technical Security Documentation
- Evidence Collection and Analysis

## Disclaimer

All simulations, vulnerability assessments, and remediation activities were performed in controlled, isolated virtual lab environments for educational and defensive security research purposes.

The projects document simulated activity and do not represent real-world compromises or production security assessments.
