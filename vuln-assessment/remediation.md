# Vulnerability Remediation and Verification

## Objective

Remediate the security weaknesses identified during the Day 3 Ubuntu Server assessment, then repeat the security scans to measure improvements.

## Baseline

| Metric | Before remediation | After remediation |
|---|---|---|
| Nessus grouped vulnerability entries | 52 | Pending |
| Lynis hardening index | 56/100 | Pending |
| Running kernel | 5.15.0-43-generic | Pending |
| SSH root login | Permitted | Pending |
| SSH password authentication | Enabled | Pending |
| Anonymous FTP | Enabled | Pending |
| Samba guest access | Enabled | Pending |
| SMB signing required | No | Pending |
| UFW firewall | Inactive | Pending |

## Planned Remediation

- [ ] Update vulnerable Ubuntu packages and kernel.
- [ ] Disable SSH root login and password authentication.
- [ ] Disable anonymous FTP access.
- [ ] Restrict Samba guest access and require SMB signing.
- [ ] Configure and enable UFW while preserving authorized SSH access.
- [ ] Verify the new running kernel and security configurations.
- [ ] Run authenticated Nessus rescan.
- [ ] Run Nmap service discovery again.
- [ ] Run Lynis again and compare hardening scores.
- [ ] Record before-and-after results with supporting evidence.

## Verification Requirements

The post-remediation Nessus scan must successfully authenticate as `soclab` via SSH. A reduction in vulnerability findings is not sufficient evidence of remediation if credentialed checks fail.

The SSH key used for Nessus scanning must remain functional, and UFW rules must permit authorized scanner access.

## Status

**Remediation not started.** Day 3 baseline evidence has been collected and documented.
