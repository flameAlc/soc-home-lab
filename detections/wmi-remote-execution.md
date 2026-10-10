
# WMI Remote Execution Detection

## Objective
Identify remote command execution through Windows
Management Instrumentation (WMI).

## Environment
- Windows 11 Enterprise Evaluation
- Kali Linux
- Sysmon Event ID 1
- Splunk Enterprise
- Impacket WMIExec

## Detection Logic
Identify suspicious process creation where:
- Parent process: WmiPrvSE.exe
- Child process: cmd.exe
- Commands executed through remote WMI

## MITRE ATT&CK
T1047 - Windows Management Instrumentation

## Test Procedure
1. Connected from Kali using Impacket WMIExec.
2. Executed hostname, whoami, net user, and systeminfo.
3. Executed a Base64-encoded PowerShell command.
4. Collected Sysmon process creation events.
5. Verified WmiPrvSE.exe spawned cmd.exe.
6. Verified cmd.exe spawned powershell.exe.
7. Confirmed PowerShell Event ID 4104 logging.

## Results
- Target host: DESKTOP-UEUG3UJ
- Target IP: 192.168.56.10
- Attacker IP: 192.168.56.102
- WMI process execution: Confirmed
- Process ancestry: Confirmed
- Encoded PowerShell execution: Confirmed

## Limitations
- WMI execution can be legitimate administrative activity.
- Process ancestry alone does not establish malicious intent.
- Additional context is needed to reduce false positives.
