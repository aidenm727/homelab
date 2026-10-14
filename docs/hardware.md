# Hardware & Roles

Most of the lab uses repurposed consumer hardware. The specifications below are
a September 2026 snapshot. Sahale's T430 workload was verified separately on
[October 14](history/2026-10-14-sahale-observations.yml).

## Core services: ThinkPad T430

- **Platform:** Lenovo ThinkPad T430
- **OS:** Ubuntu Server 24.04 LTS
- **Memory:** ~8 GiB
- **Storage:** SSD-backed system storage
- **Role:** always-on core-services machine

The T430 runs local DNS, private ingress, monitoring, Vaultwarden, Homepage, and
backup jobs. The September Docker stack had headroom in CPU, memory, and
storage. The October observations add [Sahale](https://github.com/aidenm727/sahale),
a Node.js/SQLite workload managed by systemd. Its public showcase documents
private access through Tailscale Serve; live ingress configuration was not
verified in the October checks.

## Virtualization host

- **Motherboard:** Gigabyte B450M DS3H
- **CPU:** AMD Ryzen 5 2600, 6 cores / 12 threads
- **Memory:** 16 GiB DDR4
- **Hypervisor:** Proxmox VE
- **System storage:** 500 GB SATA SSD
- **VM/LXC storage:** 1 TB NVMe SSD
- **Additional storage:** 2 TB SATA SSD used for archive/preservation data
- **Role:** VMs/LXCs, gaming, application experiments

Memory was the limiting resource for additional concurrent workloads in the
September baseline. A RAM upgrade can be sized when measured workload demand
requires it.

## Gaming VM

- **Guest OS:** Debian 12
- **vCPU:** 4
- **Memory:** 10 GiB
- **Boot disk:** 64 GiB
- **Runtime:** Docker
- **Primary application:** Crafty Controller

The VM keeps game-server configuration, Java memory use, and player-facing
ingress isolated from the T430 services.

## Admin / development machine

- **Platform:** ASUS ROG Zephyrus G14 (2024)
- **CPU:** AMD Ryzen 9 8945HS
- **OS:** Windows 11 with WSL
- **Role:** development, SSH administration, maintenance, testing, local
  experiments

The G14 serves as the operator/development machine in the September snapshot.
