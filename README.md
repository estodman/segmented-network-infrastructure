# Segmented Network Infrastructure with Security & Monitoring

## Overview
This project documents a production-style network environment that emphasizes network segmentation, secure administrative access, centralized monitoring, and documented operational procedures.

The objective is not to deploy as many tools as possible, but to demonstrate **intentional infrastructure design**, security controls, and the ability to explain *why* architectural decisions were made.

---

## Architecture Goals
- Enforce network segmentation using VLANs and firewall policy
- Minimize management plane exposure
- Centralize monitoring and log analysis
- Isolate storage and application traffic
- Support secure remote administration
- Document operational and recovery procedures

---

## High-Level Architecture
- **OPNsense** provides routing, firewalling, and DHCP
- **Managed switch** enforces VLAN separation
- **Proxmox** hosts application and monitoring VMs
- **TrueNAS** provides isolated storage services
- **Jump host** acts as the single administrative access point
- **Zabbix & Wazuh** provide monitoring and security visibility

Detailed diagrams are available in the [`diagrams/`](./diagrams) directory.

---

## Network Segmentation

| VLAN | Name        | Purpose                                  |
|------|-------------|------------------------------------------|
| 10   | Management  | Admin interfaces and jump host           |
| 20   | Services    | Application services (Nextcloud)         |
| 30   | WLAN        | Trusted user devices                     |
| 40   | Storage     | TrueNAS NFS & SMB traffic                |
| 50   | Monitoring  | Zabbix and Wazuh                         |
| 60   | Guests      | Untrusted devices (internet only)        |

Inter-VLAN routing is handled by OPNsense with a **default-deny** policy and explicit allow rules based on service requirements.

See [`network/firewall-rule-matrix.md`](./network/firewall-rule-matrix.md) for details.

---

## Core Services

| Service            | Function                                 | Location       |
|--------------------|------------------------------------------|----------------|
| OPNsense           | Firewall, routing, DHCP                  | Bare metal     |
| Proxmox            | Virtualization platform                  | Bare metal     |
| Pi-hole + Unbound  | DNS filtering and recursive resolution   | Proxmox VM     |
| Nextcloud          | File collaboration                       | Proxmox VM     |
| TrueNAS            | NFS & SMB storage                        | Bare metal     |
| Zabbix             | Infrastructure monitoring                | Proxmox VM     |
| Wazuh              | Log analysis / SIEM                      | Proxmox VM     |
| Tailscale          | Secure remote administrative access      | Multiple nodes |

---

## Security Design

### Administrative Access
- Direct administrative access from user networks is prohibited
- A hardened **jump host** is the sole entry point for management access
- SSH key-based authentication only
- Jump host access restricted to VPN/Tailscale endpoints

Documentation:
- [`security/jump-host/hardening-checklist.md`](./security/jump-host/hardening-checklist.md)

---

### Firewall Policy
- Default deny between VLANs
- Explicit allow rules for:
  - Application access
  - Monitoring traffic
  - Storage protocols
- Guest VLAN restricted to internet access only

---

## Monitoring & Visibility
- **Zabbix** monitors:
  - Host availability
  - Resource utilization
  - Network and storage devices via SNMP
- **Wazuh** collects and analyzes logs from:
  - Servers
  - Firewall
  - Jump host

Monitoring documentation is available in the [`monitoring/`](./monitoring) directory.

---

## Operational Design

### DHCP & DNS
- DHCP centralized on OPNsense for VLAN awareness
- Clients use Pi-hole as DNS
- Pi-hole forwards to Unbound for recursive resolution

### Break-Glass Access
A documented break-glass procedure exists to restore access during outages or misconfigurations, prioritizing physical and console access over network-based recovery.

See [`security/break-glass-procedure.md`](./security/break-glass-procedure.md).

---

## What This Project Demonstrates
- Practical VLAN-based network design
- Firewall policy implementation and documentation
- Secure administrative access patterns
- Monitoring and logging integration
- Operational thinking (failure scenarios, recovery, auditability)
- Clear technical documentation

