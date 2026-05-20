# Home Lab

A personal home lab built on a repurposed gaming PC running Proxmox VE. The goal of this lab is to build hands-on experience with security monitoring, log analysis, virtualization, and eventually penetration testing in an isolated environment.

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

> Static IPs are configured directly on each VM via Netplan. DHCP range on the router starts at 192.168.1.101.

---

## Lab Stack

| Service | Version | Host |
|---|---|---|
| Proxmox VE | 9.1.1 | Bare metal |
| Ubuntu Server | 26.04 LTS | Both VMs |
| Elasticsearch | 9.4.1 | elasticsearch-vm |
| Kibana | 9.4.1 | elasticsearch-vm |
| Fleet Server | 9.4.1 | elasticsearch-vm |
| Elastic Agent | 9.4.1 | Both VMs |
| WireGuard | 1.0.0 | wireguard-vpn |

---

## Remote Access

Remote access to the lab is provided via WireGuard VPN. A DuckDNS hostname (`bugbolab.duckdns.org`) is used to handle dynamic IP changes on the home internet connection. The DuckDNS record is updated every 5 minutes via a cron job on the WireGuard VM.

---

## Current Monitoring

- SSH login attempts (successful and failed)
- WireGuard VPN handshake events
- System logs from both VMs via Elastic Agent and Fleet

---

## Repository Structure

```
home-lab/
├── README.md
├── infrastructure/
│   ├── proxmox-setup.md
│   └── network-config.md
├── machines/
│   ├── elasticsearch-vm.md
│   └── wireguard-vpn.md
└── write-ups/
    └── README.md
```

---

## Write-Ups

See the [write-ups](./write-ups/README.md) directory for future penetration testing and security research documentation.
