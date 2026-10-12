# Ubuntu Server Vulnerability Assessment

## 1. Assessment Overview

This assessment evaluates an intentionally vulnerable Ubuntu Server 22.04.1 LTS virtual machine in an isolated VirtualBox cybersecurity lab.

The assessment used Nessus Essentials, Nmap, Lynis, and manual configuration checks to identify security weaknesses and establish a baseline before remediation.

**Target:** 192.168.56.20  
**Operating system:** Ubuntu Server 22.04.1 LTS  
**Nessus scan:** Ubuntu-Final-Baseline-Credentialed  
**Authentication:** Successful SSH credentialed checks  
**Nessus results:** 52 grouped vulnerability entries  
**Lynis hardening index:** 56/100

The identified weaknesses include an outdated Linux kernel, permissive SSH authentication, anonymous FTP access, unauthenticated Samba share access, optional SMB signing, and an inactive host firewall.

The next phase will prioritize remediation, verify the applied security controls, and compare the results against this baseline.
