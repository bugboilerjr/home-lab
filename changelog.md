# Changelog

All significant changes to the home lab are documented here.

---

## v1.3 — 2026-05-22

### Added
- Windows Server 2022 VM (dc01) deployed at 192.168.1.54
- Active Directory Domain Services installed and configured
- bugbolab.local domain created
- BugboLab OU structure: IT, HR, Management, Computers, Service Accounts
- Users created: jsmith, sconnor, mjohnson, lbrown, tcarroll
- Security groups: IT-Admins, IT-Support, HR-Staff, Management-Staff
- IT-Admins group added to Domain Admins
- Password Policy GPO enforced (12 char min, 90 day max, complexity required)
- Account Lockout Policy GPO enforced (5 attempts, 30 min lockout)
- Elastic Agent enrolled on dc01 under Windows Policy
- Windows integration added — collecting Security, PowerShell, System logs
- AD Security Dashboard built in Kibana (logon events, failed vs successful, top usernames)
- Fleet agent policies reorganized: Fleet Server Policy, Linux Policy, Windows Policy
- dc01.md added to repository documentation

### Changed
- README updated with dc01, AD structure, Fleet policies, and new dashboards
- Fleet Default Policy split into Linux Policy and Windows Policy for proper OS separation

### Fixed
- dc01 timezone corrected to Central Time (UTC-6) via w32tm sync
- Windows time server changed from Local CMOS Clock to time.windows.com

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
- WireGuard VPN tested and confirmed working from external network

### Changed
- Fleet Server reconfigured after IP change
- Kibana and Elasticsearch configs updated to reflect new static IP
- SSL verification mode set to none in Fleet output

### Fixed
- Fleet Server showing unhealthy after static IP change
- IP forwarding resetting to 0 after reboot

---

## v1.0 — 2026-05-17

### Added
- Proxmox VE 9.1.1 installed on bare metal
- AMD-V (SVM Mode) enabled in BIOS
- Three storage pools configured (local-lvm, vm-storage, hdd-storage)
- Ubuntu Server 26.04 LTS VM (elasticsearch-vm)
- Elasticsearch 9.4.1 with security enabled
- Kibana deployed and connected via enrollment token
- Fleet Server installed and configured
- Elastic Agent enrolled on elasticsearch-vm
- WireGuard VPN VM (wireguard-vpn)
- WireGuard 1.0.0 installed and configured
- Laptop enrolled as WireGuard peer
- SSH monitoring dashboard built in Kibana
- WireGuard handshake monitoring dashboard built in Kibana

---

## Planned

- [ ] Complete AD Security Dashboard (account lockouts panel, fix pie chart labels)
- [ ] Install Sysmon on dc01
- [ ] Join a Windows workstation to bugbolab.local domain
- [ ] Set up Kibana alerting rules
- [ ] Add pfSense VM for network segmentation
- [ ] Document first penetration testing exercise
- [ ] Set up second WireGuard tunnel for home network access
