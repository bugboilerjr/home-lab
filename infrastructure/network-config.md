# Network Configuration

## Overview

All lab machines are connected to a home GFiber network (192.168.1.0/24). Static IPs are configured directly on each VM via Netplan to avoid DHCP reassignment. The DHCP range on the router starts at 192.168.1.101, so all static IPs are assigned below 192.168.1.100.

---

## IP Layout

| Device | IP | Role |
|---|---|---|
| GFiber Router | 192.168.1.1 | Gateway / DNS |
| Proxmox Host | 192.168.1.50 | Hypervisor web UI |
| elasticsearch-vm | 192.168.1.51 | Elasticsearch, Kibana, Fleet |
| wireguard-vpn | 192.168.1.52 | VPN gateway |

---

## Setting Static IPs with Netplan

Ubuntu Server 26.04 uses Netplan for network configuration. The config file is located at:

```
/etc/netplan/00-installer-config.yaml
```

### Example static IP configuration:

```yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens18:
      dhcp4: false
      dhcp6: false
      addresses:
        - 192.168.1.51/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
      match:
        macaddress: bc:24:11:ba:1f:d2
      set-name: ens18
  version: 2
```

Apply changes with:
```bash
sudo netplan apply
```

> Note: SSH session will drop when the IP changes. Reconnect using the new IP.

---

## Remote Access — WireGuard VPN

Remote access to the lab is provided via WireGuard VPN running on the wireguard-vpn VM.

| Setting | Value |
|---|---|
| VPN Subnet | 10.0.0.0/24 |
| Server VPN IP | 10.0.0.1 |
| Laptop VPN IP | 10.0.0.2 |
| Listen Port | 51820 (UDP) |
| External Hostname | bugbolab.duckdns.org |

### Port Forwarding
Port 51820 UDP is forwarded on the GFiber router to 192.168.1.52 (wireguard-vpn).

### DuckDNS
A DuckDNS cron job runs every 5 minutes on the wireguard-vpn VM to keep the hostname pointed at the current home public IP:

```bash
*/5 * * * * ~/duckdns/duck.sh >/dev/null 2>&1
```

---

## VPN Split Tunneling

The WireGuard client is configured with split tunneling — only lab network traffic (192.168.1.0/24) routes through the VPN. Regular internet traffic uses the normal connection.

```ini
AllowedIPs = 192.168.1.0/24
```

> When connected to the home network directly, the VPN should not be active as it will cause routing conflicts.
