# Hardware & Roles

Most of the lab uses repurposed consumer hardware. The specifications below are
a September 2026 snapshot.

## Core services: ThinkPad T430

- **Platform:** Lenovo ThinkPad T430
- **OS:** Ubuntu Server 24.04 LTS
- **Memory:** ~8 GiB
- **Storage:** SSD-backed system storage
- **Role:** always-on core-services machine

The T430 runs local DNS, private ingress, monitoring, Vaultwarden, Homepage, and
backup jobs. Its current Docker stack fits comfortably within the available CPU,
memory, and storage.

## Virtualization host

- **Motherboard:** Gigabyte B450M DS3H
- **CPU:** AMD Ryzen 5 2600, 6 cores / 12 threads
- **Memory:** 16 GiB DDR4
- **Hypervisor:** Proxmox VE
- **System storage:** 500 GB SATA SSD
- **VM/LXC storage:** 1 TB NVMe SSD
- **Additional storage:** 2 TB SATA SSD used for archive/preservation data
- **Role:** VMs/LXCs, gaming, application experiments, future lab VMs

Memory is the current limiting resource for additional concurrent workloads. A
32 GiB upgrade is a reasonable future option if those workloads appear.

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

The G14 is the main operator/development machine. It is also the first candidate
for local model-serving experiments before adding dedicated AI hardware.

## Possible hardware additions

- **More Proxmox memory** for additional concurrent VMs and lab workloads.
- **Gaming/GPU desktop** for normal desktop use, Sunshine streaming, local model
  serving, and occasional GPU/transcoding work.

A dedicated NAS remains optional. Storage hardware can be sized once photo,
media, preservation, game-world, or backup growth creates a clear requirement.
