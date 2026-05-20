# Proxmox Setup

## Overview

Proxmox VE 9.1.1 is installed on bare metal on the lab machine, replacing the previous Windows gaming OS entirely. It serves as the hypervisor for all VMs in the lab.

---

## Pre-Installation

Before installing Proxmox, AMD-V (SVM Mode) virtualization was enabled in the motherboard BIOS under:

> Advanced Mode → CPU Configuration → SVM Mode → Enabled

---

## Installation

Proxmox VE was flashed to a USB drive using Balena Etcher and installed by booting from the USB.

**Key install settings:**
- Target disk: 500GB WD Black NVMe (nvme1n1)
- Hostname: pve.lab.local
- Management IP: 192.168.1.50/24
- Gateway: 192.168.1.1
- DNS: 8.8.8.8

After install the web UI is accessible at:
```
https://192.168.1.50:8006
```

---

## Storage Configuration

Three storage pools were configured after install:

| Name | Type | Device | Purpose |
|---|---|---|---|
| local | Directory | nvme1n1 | Proxmox system files |
| local-lvm | LVM-Thin | nvme1n1 | VM disks (boot drive) |
| vm-storage | LVM-Thin | nvme0n1 (1TB Samsung) | Primary VM disk storage |
| hdd-storage | Directory | sda (1TB WD HDD) | ISOs, backups, snapshots |

### Steps to add additional drives:
1. Wipe disk via Proxmox UI: **Disks → select disk → Wipe Disk**
2. Initialize with GPT: **Initialize Disk with GPT**
3. For NVMe VM storage: **Disks → LVM-Thin → Create Thinpool**
4. For HDD backup storage: **Disks → Directory → Create Directory**

---

## Post-Installation Notes

- The "No valid subscription" popup appears on every login — this is expected for the free community version and does not limit functionality
- To remove the popup permanently:
```bash
sed -i.bak "s/data.status !== 'Active'/false/g" /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js && systemctl restart pveproxy
```

---

## VM CPU Configuration

All VMs must have their CPU type set to **host** to expose the full Ryzen CPU feature set. This is required for Elasticsearch which needs AVX/AVX2 instructions.

To set: **VM → Hardware → Processors → Type → host**
