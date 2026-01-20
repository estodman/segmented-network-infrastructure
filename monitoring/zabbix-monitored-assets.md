# Zabbix Monitored Assets

## Purpose
This document describes the assets monitored by Zabbix, the monitoring methods used, and the rationale behind monitoring scope. The goal is to provide **operational visibility** without unnecessary noise.

---

## Monitoring Objectives
- Detect outages quickly
- Monitor resource utilization trends
- Support troubleshooting and incident response
- Provide capacity planning data

---

## Monitoring Architecture
- Zabbix Server resides in VLAN 50 (Monitoring)
- Agents are deployed on supported hosts
- SNMP is used for network and storage devices
- Monitoring traffic is explicitly permitted by firewall rules

---

## Monitored Assets

### Infrastructure

| Asset           | VLAN | Method       | Metrics                     |
|-----------------|------|--------------|-----------------------------|
| OPNsense        | 10   | Agent + ICMP | CPU, memory, interfaces     |
| Managed Switch  | 10   | SNMP         | Port status, errors         |
| Proxmox Host    | 10   | Agent        | CPU, memory, disk           |
| Jump Host       | 10   | Agent        | Availability, system health |

---

### Services

| Asset             | VLAN | Method   | Metrics          |
|-------------------|------|----------|------------------|
| Pi-hole / Unbound | 10   | Agent    | DNS availability |
| Nextcloud         | 20   | Agent    | Service uptime   |
| Zabbix Server     | 50   | Internal | Self-monitoring  |
| Wazuh Server      | 50   | Agent    | Resource usage   |

---

### Storage

| Asset   | VLAN | Method | Metrics                  |
|---------|------|--------|--------------------------|
| TrueNAS | 40   | SNMP   | Pool health, disk status |

---

## Alerting Strategy
- Severity-based alerting (Warning / High / Critical)
- Threshold tuning to reduce false positives
- Alerts mapped to documented response actions

---

## Security Considerations
- Zabbix has **read-only visibility**
- No shared administrative credentials
- Monitoring VLAN cannot initiate management access

---

## Summary
Zabbix provides centralized visibility into availability and performance while maintaining strict separation from administrative control planes.
