# Gaming Infrastructure

Gaming is a primary workload on the Proxmox host. Minecraft servers run inside a
dedicated Debian VM so game-server changes and Java memory use stay isolated
from core network and owner services.

The workload details and measurements below reflect the
[September 23, 2026 baseline](history/2026-09-23-lab-baseline.yml).

## Architecture

```text
Proxmox host
    │
    └── Debian gaming VM
            │
            ├── Docker
            │    └── Crafty Controller
            │           ├── vanilla Minecraft work
            │           └── StoneBlock 4
            │
            ├── Tailscale
            │    └── private administration
            │
            └── Playit
                 └── player-facing game ingress
```

Crafty and VM administration use Tailscale. Playit handles the game traffic that
needs to be reachable by players.

## Crafty Controller

Crafty handles Minecraft server creation, configuration, console access,
start/stop/restart, files, versions, and server status.

## StoneBlock 4

One documented workload is FTB StoneBlock 4:

- Minecraft 1.21.1
- NeoForge 21.1.248
- Java 21
- 2 GiB initial / 8 GiB maximum Java heap

During the September 2026 baseline, the server had remained active for roughly a
week and the VM showed high memory use. Process-level inspection traced the
usage to the Java server. VM memory returned to a low idle state after the server
was stopped.

Modded servers were moved to on-demand operation. Crafty can remain available
while the Java process stays off when nobody is playing.

## World data and backups

The September baseline verified scheduled Proxmox backups and a produced gaming
VM archive. These backups remained on the virtualization host, so host loss
remains a gap for that recovery path.

World-level backup history and restore points independent of the VM are useful
next steps, alongside an off-host copy of the VM archives.
