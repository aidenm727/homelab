# Gaming Infrastructure

Gaming is a primary workload on the Proxmox host. Minecraft servers run inside a
dedicated Debian VM so game-server changes and Java memory use stay isolated
from core network and owner services.

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

A future Gaming Hub could add a higher-level view of worlds, players, backup
history, versions, and status while leaving server-management mechanics in
Crafty.

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

Modded servers now run on demand. Crafty can remain available while the Java
process stays off when nobody is playing.

## World data and backups

The gaming VM has scheduled Proxmox backups. Longer term, world data should also
have explicit world-level history and restore points independent of a specific
VM installation.

Possible additions include:

- world-level backup history;
- restore points;
- Minecraft and modpack version history;
- long-lived personal worlds; and
- a Game World Vault view for worlds, milestones, screenshots, and history.

## Future work

- durable Vanilla Forever world;
- friends' SMP infrastructure;
- better world/backup/history views;
- simple server status and start/stop controls;
- Sunshine/Moonlight support on a future gaming PC;
- wake/sleep state in the Homelab Home; and
- shared use of a future GPU node for gaming and local AI.
