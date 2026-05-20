# elasticsearch-vm

## Overview

The primary lab server. Runs the full Elastic Stack including Elasticsearch, Kibana, and Fleet Server. All log data from the lab flows into this machine.

---

## VM Specs

| Setting | Value |
|---|---|
| **VM ID** | 100 |
| **OS** | Ubuntu Server 26.04 LTS |
| **CPU** | 4 cores (host type) |
| **RAM** | 8GB |
| **Disk** | 100GB (vm-storage NVMe) |
| **IP** | 192.168.1.51 (static) |
| **Hostname** | elasticsearch-vm |

---

## Services Running

| Service | Port | Notes |
|---|---|---|
| Elasticsearch | 9200 (HTTPS) | Security enabled by default |
| Kibana | 5601 (HTTP) | Web UI |
| Fleet Server | 8220 (HTTPS) | Manages Elastic Agents |
| Elastic Agent | — | Ships logs to Elasticsearch |
| SSH | 22 | Remote management |

---

## Elasticsearch Configuration

Config file: `/etc/elasticsearch/elasticsearch.yml`

Key settings:
```yaml
network.host: 192.168.1.51
http.port: 9200
```

### JVM Heap Size

File: `/etc/elasticsearch/jvm.options.d/heap.options`
```
-Xms4g
-Xmx4g
```
> Set to 4GB (half of available RAM). Both values must match to prevent heap resizing.

### System Settings

File: `/etc/sysctl.conf`
```
vm.max_map_count=262144
```
> Required by Elasticsearch for memory mapping. Applied with `sudo sysctl -p`.

---

## Kibana Configuration

Config file: `/etc/kibana/kibana.yml`

Key settings:
```yaml
server.host: 192.168.1.51
elasticsearch.hosts: ["https://192.168.1.51:9200"]
elasticsearch.ssl.verificationMode: none
```

Fleet output is managed via:
```yaml
xpack.fleet.outputs: [{id: fleet-default-output, name: default, is_default: true, is_default_monitoring: true, type: elasticsearch, hosts: ["https://192.168.1.51:9200"], ssl: {verification_mode: "none"}}]
```

---

## Fleet Server

Fleet Server manages Elastic Agents across the lab. It runs as part of the Elastic Agent installation on this VM.

- **Fleet Server URL:** https://192.168.1.51:8220
- **Enrolled agents:** elasticsearch-vm, wireguard-vpn
- **Agent policy:** Fleet Server Policy (fleet-server-policy)

---

## Monitoring

The following log sources are collected via Elastic Agent:

| Source | Dataset | What it captures |
|---|---|---|
| /var/log/auth.log | system.auth | SSH logins, sudo, failed auth |
| systemd journal | system.syslog | General system events |
| System metrics | system.cpu/memory | Resource usage |

---

## Dashboards

| Dashboard | Description |
|---|---|
| SSH Login Attempts | Visualizes successful and failed SSH logins over time, broken down by event type |
| WireGuard Monitoring | VPN handshake events from the wireguard-vpn agent |

---

## Snapshots

| Name | Description |
|---|---|
| clean-ubuntu-install | Fresh Ubuntu Server before Elasticsearch install |
| elasticsearch-working | Elasticsearch 9.4.1 running and accessible |
| fleet-server-healthy-static-ip | Fleet Server healthy after static IP change |
| full-stack-working | All services running, both agents enrolled |
