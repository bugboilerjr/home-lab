# wireguard-vpn

## Overview

Dedicated VM for VPN access to the lab. Runs WireGuard to provide secure remote access from anywhere. Also runs an Elastic Agent to ship system and VPN logs to the Elasticsearch VM.

---

## VM Specs

| Setting | Value |
|---|---|
| **VM ID** | 101 |
| **OS** | Ubuntu Server 26.04 LTS |
| **CPU** | 1 core (host type) |
| **RAM** | 512MB (2GB during install, reduced after) |
| **Disk** | 20GB (vm-storage NVMe) |
| **IP** | 192.168.1.52 (static) |
| **Hostname** | wireguard-vpn |

---

## Services Running

| Service | Port | Notes |
|---|---|---|
| WireGuard | 51820 (UDP) | VPN server |
| Elastic Agent | — | Ships logs to Fleet Server |
| SSH | 22 | Remote management |

---

## WireGuard Configuration

Config file: `/etc/wireguard/wg0.conf`

```ini
[Interface]
PrivateKey = <server private key>
Address = 10.0.0.1/24
ListenPort = 51820
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o ens18 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o ens18 -j MASQUERADE

[Peer]
PublicKey = <laptop public key>
AllowedIPs = 10.0.0.2/32
```

---

## IP Forwarding

Required for WireGuard to route traffic between VPN clients and the home network.

File: `/etc/sysctl.d/99-wireguard.conf`
```
net.ipv4.ip_forward=1
```

> Placed in `/etc/sysctl.d/` instead of `/etc/sysctl.conf` to ensure it loads after cloud-init and persists across reboots.

---

## DuckDNS

DuckDNS is used to maintain a persistent hostname pointing to the home public IP.

- **Hostname:** bugbolab.duckdns.org
- **Update script:** ~/duckdns/duck.sh
- **Cron job:** runs every 5 minutes

```bash
*/5 * * * * ~/duckdns/duck.sh >/dev/null 2>&1
```

---

## WireGuard Kernel Debug Logging

Kernel debug logging is enabled to capture detailed WireGuard handshake and tunnel events:

```bash
echo module wireguard +p | sudo tee /sys/kernel/debug/dynamic_debug/control
```

Made persistent via `/etc/rc.local`:
```bash
#!/bin/bash
echo module wireguard +p > /sys/kernel/debug/dynamic_debug/control
exit 0
```

Logs are captured by Elastic Agent and shipped to Elasticsearch where they can be searched in Kibana using:
```
host.name: "wireguard-vpn" and message: "handshake"
```

---

## Elastic Agent

Enrolled in Fleet under the **Default policy**. Ships the following to Elasticsearch:

| Source | What it captures |
|---|---|
| /var/log/auth.log | SSH logins to the VPN VM |
| systemd journal | WireGuard handshake events |
| kernel logs | Detailed WireGuard tunnel events |

---

## Client Configuration (Laptop)

```ini
[Interface]
PrivateKey = <laptop private key>
Address = 10.0.0.2/24
DNS = 8.8.8.8

[Peer]
PublicKey = <server public key>
Endpoint = bugbolab.duckdns.org:51820
AllowedIPs = 192.168.1.0/24
PersistentKeepalive = 25
```

---

## Snapshots

| Name | Description |
|---|---|
| static-ip-configured | Static IP set to 192.168.1.52 |
| duckdns-configured | DuckDNS and cron job configured |
| elastic-agent-enrolled-ip-forward-fixed | Elastic Agent enrolled, IP forwarding persistent |
