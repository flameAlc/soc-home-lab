
# Cybersecurity Home Lab Portfolio

Hands-on cybersecurity projects focused on SOC
operations, threat detection, security monitoring,
incident investigation, and vulnerability management.

This repository documents practical security testing
in isolated virtual lab environments, including
configuration, simulations, investigation findings,
and supporting evidence.

## Projects

### Project 1: SOC Monitoring & Threat Detection

**Status:** Completed

**Technologies:** Splunk Enterprise, Sysmon,
Windows 11, Kali Linux, Impacket, Wireshark,
Splunk Universal Forwarder

Built a Windows-based SOC monitoring lab to collect
endpoint security telemetry, simulate suspicious
activity, investigate security events, and validate
SIEM detections.

**Key activities:**

- Configured centralized Windows event monitoring.
- Collected Sysmon, Security, and PowerShell logs.
- Simulated WMI-based remote command execution.
- Investigated encoded PowerShell activity.
- Analyzed failed network authentication attempts.
- Created and validated a scheduled Splunk alert.
- Mapped detection scenarios to MITRE ATT&CK.
- Documented investigation evidence and timelines.

**Validated detection scenarios:**

| Detection | MITRE ATT&CK |
|---|---|
| WMI Remote Execution | T1047 |
| Encoded PowerShell | T1059.001, T1027 |
| Failed Network Logons | T1110.001 |

**[View Project 1 Documentation](threat-detection/README.md)**

**Detection Reports:**

- [Failed Network Logons](detections/failed-network-logons.md)
- [WMI Remote Execution](detections/wmi-remote-execution.md)
- [Encoded PowerShell](detections/encoded-powershell.md)

**[View Incident Timeline](threat-detection/incident-timeline.md)**

**[View Project Evidence](threat-detection/evidence/)**

---

### Project 2: Vulnerability Assessment & Remediation

**Status:** In Progress

**Planned technologies:** Nessus Essentials,
Ubuntu Server, SSH, UFW

This project will focus on identifying,
prioritizing, remediating, and validating
security vulnerabilities in a controlled
Linux lab environment.

**Planned activities:**

- Deploy a vulnerable Ubuntu Server baseline.
- Configure vulnerability scanning.
- Perform initial vulnerability assessment.
- Analyze and prioritize findings.
- Apply security patches and hardening.
- Perform authenticated verification scans.
- Compare before-and-after results.
- Document remediation evidence.

**[View Project 2 Documentation](vuln-assessment/README.md)**

---

## Skills Demonstrated

- Security Information and Event Management (SIEM)
- Windows Security Event Analysis
- Endpoint Threat Detection
- Security Alert Configuration
- Incident Investigation
- Process Tree Analysis
- MITRE ATT&CK Mapping
- Network Traffic Analysis
- Security Documentation

## Disclaimer

All simulations were performed in controlled,
isolated lab environments for educational and
defensive security research purposes.

The projects document simulated activity and
do not represent real-world compromises.
