# Backup Strategy

## Purpose
This document defines the backup strategy for the infrastructure environment. The objective is to ensure configurations and critical services can be restored following failure or security incidents.

---

## Backup Principles
- Prioritize configuration and state
- Maintain offline and off-host copies
- Ensure backups are verifiable
- Protect backups from unauthorized access

---

## Backup Scope

### Infrastructure
- OPNsense configuration
- Proxmox host configuration
- Jump host system configuration

---

### Services
- Pi-hole and Unbound configuration
- Zabbix configuration and templates
- Wazuh configuration
- Nextcloud configuration (excluding user data where appropriate)

---

### Storage
- TrueNAS configuration
- ZFS snapshots for application data

---

## Backup Methods
- Scheduled configuration exports
- Proxmox snapshot-based VM backups
- ZFS snapshots managed by TrueNAS

---

## Storage Locations

| Location                    | Purpose                   |
|-----------------------------|---------------------------|
| TrueNAS                     | Primary backup repository |
| Offline media               | Disaster recovery         |
| Encrypted removable storage | Credential backups        |

---

## Backup Schedule

| Item            | Frequency           |
|-----------------|---------------------|
| Firewall config | Weekly + pre-change |
| VM snapshots    | Daily               |
| Service configs | Weekly              |
| Offline copy    | Monthly             |

---

## Validation
- Periodic restore testing
- Backup integrity checks after major changes

---

## Summary
This backup strategy prioritizes recoverability of core infrastructure while minimizing operational complexity. Backups are treated as a critical availability and security control.
