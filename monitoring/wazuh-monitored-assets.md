# Wazuh Monitored Assets

## Purpose
This document describes the assets monitored by Wazuh and the types of security events collected and analyzed.  
Wazuh is used for **security visibility and detection**, not system performance monitoring.

---

## Security Monitoring Objectives
- Detect unauthorized access attempts
- Identify configuration drift or policy violations
- Surface suspicious system behavior
- Support incident investigation and post-event analysis

---

## Monitoring Architecture
- Wazuh Manager runs in the Monitoring VLAN (VLAN 50)
- Agents are installed on security-relevant hosts only
- Log collection is **one-way** (agent → manager)
- Monitoring traffic is explicitly allowed by firewall policy

---

## Monitored Assets

| Asset                 | VLAN | Log Types Collected                                |
|-----------------------|------|----------------------------------------------------|
| Jump Host             | 10   | Authentication logs, sudo usage, SSH events        |
| OPNsense              | 10   | Firewall events, blocked traffic, admin actions    |
| Proxmox Host          | 10   | System logs, authentication events                 |
| Application Servers   | 20   | System logs, service events                        |
| Monitoring Servers    | 50   | System and service logs                            |

---

## Key Detection Areas

### Authentication & Access
- Failed and successful SSH login attempts
- Privilege escalation (sudo usage)
- Access attempts outside expected timeframes

---

### System Integrity
- Configuration file changes
- Service restarts
- Package installation or removal

---

### Network & Firewall Events
- Repeated blocked connection attempts
- Policy violations or unexpected traffic patterns

---

## Noise Reduction & Tuning
- Only security-relevant log sources are enabled
- Alert thresholds tuned to reduce false positives
- Informational events retained for investigation, not alerting

---

## Security Considerations
- Wazuh does not have administrative access to monitored systems
- Agents run with least-privilege permissions
- Logs are retained based on operational and storage constraints

---

## Operational Notes
- Detection rules are reviewed periodically
- New log sources are added only when they provide clear security value
- Alerts are correlated with Zabbix data during incident response

---

## Summary
Wazuh provides centralized security event visibility across critical infrastructure components, enabling early detection and investigation of security-relevant activity without expanding the attack surface.
