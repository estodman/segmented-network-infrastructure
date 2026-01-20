# Break-Glass Procedure – Administrative Access Recovery

## Purpose
This document defines the break-glass procedure used to restore administrative access when standard access paths (VPN, jump host, DNS, or firewall rules) are unavailable or compromised.

Break-glass access is **not** used for convenience. It is invoked only during outages, misconfigurations, or security incidents.

---

## Guiding Principles
- Prefer **physical or console access** over network-based recovery
- Use the **least invasive** recovery method first
- Apply **temporary changes only**
- Ensure **auditability and post-incident review**

---

## Trigger Conditions
- Jump host unavailable or suspected compromise
- VPN or Tailscale access failure
- Firewall misconfiguration blocking management access
- DNS outage affecting administrative access
- Core services unreachable via the network

---

## Break-Glass Access Levels

### Level 1 – Temporary Firewall Bypass
**Use when:** Firewall is functional, but normal admin access paths are unavailable.

Steps:
1. Access OPNsense via local console
2. Temporarily allow:
   - Admin workstation IP → VLAN 10 (SSH/HTTPS only)
3. Restore jump host or VPN access
4. Remove temporary rules immediately

---

### Level 2 – Direct Management VLAN Access
**Use when:** VPN and jump host are unavailable.

Steps:
1. Physically connect an admin workstation to a VLAN 10 switch port
2. Assign a static IP within the Management VLAN
3. Access OPNsense, Proxmox, and TrueNAS
4. Restore failed services
5. Revert switch configuration

---

### Level 3 – Hypervisor Console Recovery
**Use when:** Network access is unavailable or misconfigured.

Steps:
1. Access Proxmox via physical or console access
2. Use VM consoles to:
   - Repair jump host
   - Restore DNS services
   - Correct firewall or routing issues
3. Verify normal access paths

---

### Level 4 – Firewall Configuration Recovery
**Use when:** Firewall configuration prevents all access.

Steps:
1. Access OPNsense console
2. Restore the most recent known-good configuration backup
3. Verify VLANs, DHCP, and firewall rules
4. Reboot and validate connectivity

---

## Credentials & Recovery Artifacts
The following must be available **offline**:
- Encrypted OPNsense configuration backups
- Proxmox administrative credentials
- SSH private keys on encrypted removable media
- Network diagrams and addressing documentation

---

## Post-Incident Actions
1. Remove temporary access paths
2. Rotate exposed credentials
3. Review logs in Wazuh and OPNsense
4. Document root cause and remediation

---

## Summary
Break-glass procedures ensure administrative control can be safely restored during critical failures while maintaining accountability and minimizing exposure.
