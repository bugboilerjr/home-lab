# Home Lab

A personal home lab built on a repurposed gaming PC running Proxmox VE. The goal of this lab is to build hands-on experience with security monitoring, log analysis, virtualization, Active Directory, and penetration testing in an isolated environment.

![Lab Version](https://img.shields.io/badge/lab%20version-v1.3-blue)
![Proxmox](https://img.shields.io/badge/Proxmox-9.1.1-orange)
![Elasticsearch](https://img.shields.io/badge/Elasticsearch-9.4.1-005571)
![Windows Server](https://img.shields.io/badge/Windows%20Server-2022-0078D4)

---

## Hardware

| Component | Spec |
|---|---|
| **CPU** | AMD Ryzen 5 3600XT (6 cores / 12 threads) |
| **RAM** | 32GB DDR4 |
| **GPU** | NVIDIA RTX 3060 Ti |
| **Storage** | 500GB WD Black NVMe (Proxmox OS), 1TB Samsung 970 EVO Plus NVMe (VM storage), 1TB WD HDD (backups/ISOs) |

---

## Network Layout

| Device | IP | Role |
|---|---|---|
| GFiber Router | 192.168.1.1 | Gateway |
| Proxmox Host | 192.168.1.50 | Hypervisor |
| elasticsearch-vm | 192.168.1.51 | Elasticsearch, Kibana, Fleet Server |
| wireguard-vpn | 192.168.1.52 | WireGuard VPN, Elastic Agent |
| dc01 | 192.168.1.54 | Domain Controller, Windows Server 2022 |

> Static IPs are configured directly on each VM. Linux VMs use Netplan. Windows Server uses static IPv4 configuration. DHCP range on the router starts at 192.168.1.101.

---

## Lab Stack

| Service | Version | Host |
|---|---|---|
| Proxmox VE | 9.1.1 | Bare metal |
| Ubuntu Server | 26.04 LTS | elasticsearch-vm, wireguard-vpn |
| Windows Server | 2022 Standard Evaluation | dc01 |
| Elasticsearch | 9.4.1 | elasticsearch-vm |
| Kibana | 9.4.1 | elasticsearch-vm |
| Fleet Server | 9.4.1 | elasticsearch-vm |
| Elastic Agent | 9.4.1 | All VMs |
| WireGuard | 1.0.0 | wireguard-vpn |
| Active Directory | Windows Server 2022 | dc01 |

---

## Remote Access

Remote access to the lab is provided via WireGuard VPN. A DuckDNS hostname (`bugbolab.duckdns.org`) handles dynamic IP changes on the home internet connection. The DuckDNS record is updated every 5 minutes via a cron job on the WireGuard VM.

---

## Fleet Agent Policies

| Agent | Policy | Role |
|---|---|---|
| elasticsearch-vm | Fleet Server Policy | Hosts Fleet Server |
| wireguard-vpn | Linux Policy | Linux log collection |
| dc01 | Windows Policy | Windows Security log collection |

---

## Current Monitoring

- SSH login attempts (successful and failed) — elasticsearch-vm and wireguard-vpn
- WireGuard VPN handshake events — wireguard-vpn
- Windows Security Event logs (4624, 4625, 4740) — dc01
- Windows PowerShell logs — dc01
- System metrics from all VMs

---

## Active Directory

- **Domain:** bugbolab.local
- **Domain Controller:** dc01 (192.168.1.54)
- **Structure:** BugboLab OU → IT, HR, Management, Computers, Service Accounts
- **Password Policy:** 12 char minimum, complexity required, 90 day max age, 5 attempt lockout
- **Groups:** IT-Admins (Domain Admins), IT-Support, HR-Staff, Management-Staff

---

## Dashboards

| Dashboard | Data Source | Description |
|---|---|---|
| SSH Login Attempts | elasticsearch-vm, wireguard-vpn | SSH authentication events over time |
| WireGuard Monitoring | wireguard-vpn | VPN handshake events |
| AD Security Dashboard | dc01 | Windows logon events, failed logons, top usernames |

---

## Repository Structure

```
home-lab/
├── README.md
├── changelog.md
├── infrastructure/
│   ├── proxmox-setup.md
│   └── network-config.md
├── machines/
│   ├── elasticsearch-vm.md
│   ├── wireguard-vpn.md
│   └── dc01.md
└── write-ups/
    └── README.md
```

---

## Write-Ups

See the [write-ups](./write-ups/README.md) directory for future penetration testing and security research documentation.
