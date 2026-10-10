
# SOC Lab Incident Timeline

## Overview

This report documents simulated remote execution,
host discovery, encoded PowerShell activity, and
failed network authentication in an isolated SOC lab.

All timestamps are from Splunk's displayed event times.

## Environment

- Windows endpoint: DESKTOP-UEUG3UJ
- Windows IP: 192.168.56.10
- Kali Linux IP: 192.168.56.102
- SIEM: Splunk Enterprise
- Telemetry: Sysmon and Windows Event Logs
- Simulation tool: Impacket WMIExec

## Timeline — October 11, 2026

| Time | Event | Evidence |
|---|---|---|
| 02:22:21.929 | WMI remote command execution | Sysmon Event ID 1 |
| 02:23:36.215 | whoami execution | Sysmon Event ID 1 |
| 02:38:02.049 | hostname execution | Sysmon Event ID 1 |
| 02:38:56.350 | PowerShell command encoding | PowerShell Event ID 4104 |
| 02:39:49.517 | Encoded PowerShell launched through WMI | Sysmon Event ID 1 |
| 02:39:54.351 | Get-Process script block recorded | PowerShell Event ID 4104 |
| 02:51:57.555 | net user execution | Sysmon Event ID 1 |
| 02:55:04.500 | systeminfo execution | Sysmon Event ID 1 |
| 03:25:33.027 | Failed network logon | Security Event ID 4625 |
| 03:26:52.230 | Second failed network logon | Security Event ID 4625 |
| 03:30:01 | Scheduled failed-logon alert triggered | Splunk Alert History |

## Detection Findings

### WMI Remote Execution
Observed WmiPrvSE.exe spawning cmd.exe.

MITRE ATT&CK: T1047

### Encoded PowerShell
Observed an encoded PowerShell command executed
through WMI, followed by Get-Process script-block
logging.

MITRE ATT&CK: T1059.001, T1027

### Failed Network Authentication
Two failed network logons targeted the soclab account
from 192.168.56.102.

- Logon Type: 3
- Status: 0xc000006d
- Substatus: 0xc000006a

MITRE ATT&CK: T1110.001 (simulated password guessing)

## Analysis

The lab successfully generated and collected
telemetry for remote command execution,
host discovery, PowerShell activity, and
failed authentication.

The WMI execution activity preceded the failed
network logons. These were separate controlled
tests and do not establish a real-world intrusion
or successful compromise.

## Limitations

- All activity was generated in an isolated lab.
- Administrative WMI activity can be legitimate.
- Encoded PowerShell is not inherently malicious.
- Additional telemetry is required to establish
  malicious intent in a production environment.

## Conclusion

The SOC lab demonstrated successful collection,
investigation, and correlation of endpoint
security events using Splunk Enterprise.
