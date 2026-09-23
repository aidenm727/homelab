# Hardware & Roles

The lab is built mostly from repurposed consumer hardware rather than dedicated
enterprise equipment. That is part of the project: resource constraints are
real, and architecture decisions have to account for them.

The specifications below are a **September 2026 snapshot**.

## Core services — ThinkPad T430

- **Platform:** Lenovo ThinkPad T430
- **OS:** Ubuntu Server 24.04 LTS
- **Memory:** ~8 GiB
- **Storage:** SSD-backed system storage
- **Role:** always-on core-services machine

This machine handles the services that benefit from being boring and available:
local DNS, private ingress, monitoring, Vaultwarden, the start page, and backup
coordination.

An older laptop works surprisingly well here. It is low-power relative to the
virtualization machine, has enough memory for the current lightweight Docker
stack, and keeps core services independent from experiments and gaming
workloads.

## Virtualization host

- **Motherboard:** Gigabyte B450M DS3H
- **CPU:** AMD Ryzen 5 2600 — 6 cores / 12 threads
- **Memory:** 16 GiB DDR4
- **Hypervisor:** Proxmox VE
- **System storage:** 500 GB SATA SSD
- **VM/LXC storage:** 1 TB NVMe SSD
- **Additional storage:** 2 TB SATA SSD used for archive/preservation data
- **Role:** isolated workloads, gaming, application experiments, future lab VMs

The main constraint here is memory rather than CPU or disk capacity. That is why
heavier game workloads are run on demand and why a future move to 32 GiB makes
sense only when additional workloads actually need it.

## Gaming VM

- **Guest OS:** Debian 12
- **vCPU:** 4
- **Memory:** 10 GiB
- **Boot disk:** 64 GiB
- **Runtime:** Docker
- **Primary application:** Crafty Controller

Keeping gaming inside its own VM gives game-server changes, Java memory use, and
player-facing ingress a failure/resource boundary separate from core networking
and owner services.

## Admin / development machine

- **Platform:** ASUS ROG Zephyrus G14 (2024)
- **CPU:** AMD Ryzen 9 8945HS
- **OS:** Windows 11 with WSL
- **Role:** development, SSH administration, maintenance, testing, and local
  experiments

The G14 is not an always-on server. It is the main operator/development machine
and is also the obvious first place to experiment with local AI before buying a
dedicated GPU node.

## Future hardware

I try not to buy hardware just because it would look good in a homelab diagram.
Two upgrades have plausible future roles if real workloads justify them:

- **More Proxmox memory** for additional concurrent VMs/labs.
- **A gaming/GPU desktop** that can serve as a normal PC, Sunshine streaming
  host, local-AI endpoint, and occasional GPU/transcoding worker while sleeping
  when it is not needed.

A dedicated NAS is intentionally not assumed. Preservation, photos, game worlds,
media, and backup growth should establish the storage requirements first.
