# Jump Host Security Hardening Checklist

## Purpose
This document defines the security hardening controls applied to the jump host.
The jump host serves as the **sole administrative entry point** to management
interfaces and must be treated as a high-risk, high-value asset.

Hardening focuses on minimizing attack surface, enforcing strong authentication,
and ensuring auditability.

---

## Host Role & Scope
- Administrative access point for:
  - OPNsense
  - Proxmox
  - TrueNAS
  - Monitoring platforms
- Located in **Management VLAN (VLAN 10)**
- No user-facing services
- No application workloads

---

## Secure Access Model

### Remote Access
- Remote administrative access is provided **exclusively via Tailscale**
- Jump host is **not exposed** to the public internet
- No inbound SSH allowed from local user VLANs
- Tailscale enforces:
  - Device-based identity
  - Encrypted point-to-point connectivity
  - Explicit approval of admin devices

### SSH Access
- SSH allowed **only over the Tailscale interface**
- Password authentication disabled
- SSH key-based authentication only
- Root login disabled
- Separate non-root administrative user
- SSH keys protected with passphrases

---

## Authorization Controls
- Least-privilege sudo access
- No shared accounts
- Administrative access limited to approved users and devices
- Tailscale ACLs restrict which nodes may access the jump host

---

## Network Restrictions
- Inbound access limited to:
  - SSH over Tailscale only
- Outbound access restricted to:
  - Management interfaces
  - Monitoring systems
  - Required update repositories
- No direct internet-facing services
- Firewall rules explicitly defined and logged

---

## System Hardening

### Operating System
- Minimal base OS installation
- Unnecessary services removed or disabled
- Automatic security updates enabled
- System time synchronized via NTP

### SSH Configuration
- Protocol 2 only
- Strong ciphers and MACs enforced
- Connection rate limiting enabled
- Idle session timeouts configured

---

## Monitoring & Logging
- Zabbix agent installed for:
  - Availability
  - Resource utilization
- Wazuh agent installed for:
  - Authentication events
  - Privilege escalation attempts
  - Configuration changes
- SSH and Tailscale access events are logged and reviewed

---

## File & Key Management
- SSH private keys stored securely
- No private keys stored in plaintext
- Regular review of authorized keys
- Permissions enforced on SSH directories and files

---

## Backup & Recovery
- Jump host configuration included in backup strategy
- SSH keys backed up securely and offline
- Recovery procedures documented separately

---

## Validation & Maintenance
- Hardening controls reviewed periodically
- Configuration validated after updates
- Unauthorized changes investigated immediately

---

## Security Rationale
The jump host is intentionally:
- Isolated behind an identity-aware access layer (Tailscale)
- Heavily monitored
- Functionally limited

This design reduces exposure of the management plane while maintaining secure,
auditable administrative access.

---

## Summary
Strong jump host hardening, combined with identity-based remote access, ensures
administrative control is both secure and observable without exposing management
services to untrusted networks.
