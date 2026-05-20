# Changelog

All significant changes to the home lab are documented here.

---

## v1.2 — 2026-05-19

### Added
- Network diagram created in draw.io
- GitHub repository initialized with full lab documentation
- changelog.md added to track lab versions

### Changed
- README updated with full hardware specs, network layout, and stack versions

---

## v1.1 — 2026-05-18

### Added
- Static IPs configured on all VMs via Netplan
  - elasticsearch-vm: 192.168.1.51
  - wireguard-vpn: 192.168.1.52
- Elastic Agent enrolled on wireguard-vpn VM
- WireGuard kernel debug logging enabled for detailed VPN event capture
- DuckDNS configured with hostname bugbolab.duckdns.org
- Cron job set up to update DuckDNS every 5 minutes
- IP forwarding made persistent via /etc/sysctl.d/99-wireguard.conf
- Port forwarding rule added on GFiber router for port 51820 UDP
- WireGuard VPN tested and confirmed working from external network (phone hotspot)

### Changed
- Fleet Server reconfigured after IP change from 192.168.1.108 to 192.168.1.51
- Kibana and Elasticsearch configs updated to reflect new static IP
- SSL verification mode set to none in Fleet output to resolve certificate mismatch after IP change
- WireGuard VM disk expanded from 8GB to 20GB to accommodate Elastic Agent installation

### Fixed
- Fleet Server showing unhealthy after static IP change — resolved by updating Fleet output SSL settings in kibana.yml
- IP forwarding resetting to 0 after reboot — resolved by moving config to /etc/sysctl.d/

---

## v1.0 — 2026-05-17

### Added
- Proxmox VE 9.1.1 installed on bare metal (repurposed gaming PC)
- AMD-V (SVM Mode) enabled in BIOS for virtualization support
- Three storage pools configured:
  - local-lvm: 500GB WD Black NVMe (VM boot disks)
  - vm-storage: 1TB Samsung 970 EVO Plus NVMe (primary VM storage)
  - hdd-storage: 1TB WD HDD (ISOs, backups, snapshots)
- Ubuntu Server 26.04 LTS VM created (elasticsearch-vm)
- Elasticsearch 9.4.1 deployed with security enabled
- JVM heap size set to 4GB
- vm.max_map_count set to 262144
- Kibana deployed and connected to Elasticsearch via enrollment token
- Fleet Server installed and configured
- Elastic Agent enrolled on elasticsearch-vm
- WireGuard VPN VM created (wireguard-vpn)
- Ubuntu Server 26.04 LTS installed on wireguard-vpn
- WireGuard 1.0.0 installed and configured
- Laptop enrolled as WireGuard peer
- SSH monitoring dashboard built in Kibana
- WireGuard handshake monitoring dashboard built in Kibana
- CPU type set to host on all VMs to expose full Ryzen feature set

### Infrastructure
- Hypervisor: Proxmox VE 9.1.1
- Host machine: AMD Ryzen 5 3600XT, 32GB DDR4, RTX 3060 Ti
- Network: GFiber router, 192.168.1.0/24

---

## Planned

- [ ] Add pfSense VM for network segmentation
- [ ] Add Windows 11 VM for workstation monitoring
- [ ] Configure Kibana alerting rules
- [ ] Set up VLAN segmentation
- [ ] Document first penetration testing exercise
