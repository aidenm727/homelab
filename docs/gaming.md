# Gaming Infrastructure

Gaming is one of the more fun parts of the Homelab, but it has also generated
some of the best real infrastructure problems in the project.

The basic goal is simple: keep personal/friends' worlds durable and easy to run
without turning the core-services machine into a game server.

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
                 └── explicit player-facing game ingress
```

Administrative access and player access are separate paths. Crafty and the VM
remain private; Playit exists specifically for the game traffic that needs to be
reachable by players.

## Crafty Controller

Crafty provides the Minecraft-specific control plane: server creation,
configuration, console access, start/stop/restart, files, versions, and the basic
operational view of each world.

The point is not to rebuild Crafty. Longer term, a personal Gaming Hub can sit
above it and focus on the experience I actually care about—worlds, players,
backups/history, versions, and quick status—while Crafty continues to own the
server-management mechanics.

## StoneBlock 4

One documented workload is FTB StoneBlock 4:

- Minecraft 1.21.1
- NeoForge 21.1.248
- Java 21
- 2 GiB initial / 8 GiB maximum Java heap

During the September 2026 baseline, the server had accidentally remained active
for roughly a week and the gaming VM appeared memory constrained. Process-level
inspection showed the Java server itself accounting for the large footprint.
After the server was stopped, the VM returned to a low idle memory state.

That changed the operating model: modded servers are **on-demand workloads**.
Crafty can stay available without paying the memory cost of a large Java process
when nobody is playing.

## Worlds are data, not disposable compute

The VM and server processes can be rebuilt. Personal worlds are different.

The current design includes scheduled VM backup, but the longer-term goal is to
make game-world continuity more explicit:

- world-level backup/history;
- easy restore points;
- clear Minecraft/modpack version history;
- durable personal worlds that outlive a particular VM or server install; and
- eventually a **Game World Vault** view of worlds, milestones, screenshots, and
  history.

## Where this could go

The broader Gaming Hub direction includes:

- a durable Vanilla Forever world;
- friends' SMP infrastructure;
- better world/backups/history UX;
- simple server status and start/stop controls;
- a future gaming PC that can run Sunshine for Moonlight streaming;
- wake/sleep state surfaced through the Homelab Home; and
- local-AI/GPU workloads sharing the future high-performance node when sensible.

Persistent Minecraft belongs on the virtualization host. A future high-end PC
should be allowed to sleep when nobody needs interactive/GPU work.
