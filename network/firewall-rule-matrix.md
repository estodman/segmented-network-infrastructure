# Inter-VLAN Firewall Policy

## Purpose
This document defines the inter-VLAN firewall policy enforced by OPNsense.  
The objective is to enforce **least-privilege network access**, reduce lateral
movement risk, and clearly separate trust zones.

All inter-VLAN traffic is **blocked by default** unless explicitly permitted.

---

## Network Segments

| VLAN | Name        | Purpose                                  |
|------|-------------|------------------------------------------|
| 10   | Management  | Administrative access and control plane  |
| 20   | Services    | Application and infrastructure services  |
| 30   | LAN         | Trusted user devices                     |
| 40   | Storage     | Storage and data services                |
| 50   | Monitoring  | Observability and security monitoring    |
| 60   | Guests      | Untrusted devices                        |

---

## Policy Model
- Firewall rules are applied **on the source VLAN**
- Inter-VLAN traffic is **explicit-allow only**
- Management access is restricted to the **jump host**
- Monitoring systems have **visibility without control**

---

## VLAN Policies

### VLAN 10 – Management
**Trust Level:** Highest

**Allowed:**
- SSH / HTTPS → OPNsense, Proxmox, TrueNAS
- SSH → Jump host
- HTTPS → Monitoring dashboards (Zabbix, Wazuh)

**Denied:**
- Direct access from non-management VLANs
- User or guest-initiated connections

**Rationale:**  
Isolates the control plane and prevents administrative access from user or service networks.

---

### VLAN 20 – Services
**Trust Level:** Medium

**Allowed:**
- Application traffic to Storage VLAN (NFS/SMB as required)
- Agent-based monitoring → VLAN 50
- DNS → Pi-hole

**Denied:**
- Management plane access
- Direct access to user or guest networks

**Rationale:**  
Limits service blast radius while allowing only required dependencies.

---

### VLAN 30 – LAN
**Trust Level:** Medium

**Allowed:**
- HTTPS → Application services
- DNS → Pi-hole
- Internet access

**Denied:**
- Management VLAN access
- Storage VLAN access
- Monitoring VLAN access

**Rationale:**  
Prevents user devices from accessing sensitive infrastructure components.

---

### VLAN 40 – Storage
**Trust Level:** High (restricted)

**Allowed:**
- NFS / SMB from approved services and hypervisors
- SNMP → Monitoring VLAN

**Denied:**
- Direct user access
- Internet access
- Management access from non-admin sources

**Rationale:**  
Protects data by restricting access to known consumers only.

---

### VLAN 50 – Monitoring
**Trust Level:** Low impact, high visibility

**Allowed:**
- Polling agents and SNMP targets
- DNS and OS updates

**Denied:**
- Administrative access
- Service initiation outside monitoring scope

**Rationale:**  
Ensures observability without introducing a lateral movement path.

---

### VLAN 60 – Guests
**Trust Level:** Untrusted

**Allowed:**
- DNS
- HTTP/HTTPS to the internet

**Denied:**
- Any RFC1918 traffic
- Any internal VLAN access

**Rationale:**  
Fully isolates guest devices from internal resources.

---

## Operational Notes
- Temporary firewall rules are documented and removed immediately
- All firewall changes are logged
- Emergency access follows the break-glass procedure

See:
- [`security/jump-host/break-glass-procedure.md`](../security/jump-host/break-glass-procedure.md)
