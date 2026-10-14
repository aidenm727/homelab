# Architecture

The Homelab has two main compute roles: a small always-on core-services machine
and a Proxmox virtualization host. This keeps DNS, ingress, monitoring, password
management, and backups independent from heavier VMs and game workloads.

The infrastructure layout reflects the [September 23 baseline](history/2026-09-23-lab-baseline.yml),
with Sahale added from the [October 14 observations](history/2026-10-14-sahale-observations.yml).
The Sahale access path below reflects its public showcase design. Live ingress
configuration and the deployed application revision were not verified.

## Topology

```text
Owner devices
    │
    │  Tailscale private administration
    ▼
ThinkPad T430 / Ubuntu Server
    ├── Docker services
    │     ├── Pi-hole: local DNS / filtering
    │     ├── Traefik -> Vaultwarden / Homepage
    │     ├── Prometheus -> Grafana
    │     ├── Alloy -> Loki
    │     └── Uptime Kuma: reachability
    ├── systemd: Sahale (Node.js / SQLite)
    │     └── scheduled SQLite snapshot job
    └── Restic
          ├── local versioned backup
          └── encrypted Backblaze B2 copy

Proxmox / Ryzen host (also administered over Tailscale)
    ├── node exporter -> T430 Prometheus
    ├── VM / LXC workloads and experiments
    └── Debian gaming VM
          ├── Docker / Crafty Controller -> Minecraft
          ├── Playit -> player-facing Minecraft ingress
          └── scheduled Proxmox backup -> host-local archive
```

Private web access on the T430, including Sahale's documented Serve path:

```text
Owner devices over Tailscale
    ├── Traefik -> private web services (Vaultwarden / Homepage)
    └── Tailscale Serve -> Sahale's loopback Node.js listener
```

Administration uses Tailscale. Player-facing game traffic uses a separate Playit
path.

## Compute roles

The T430 runs lower-resource services that benefit from staying available:
DNS, ingress, monitoring, Vaultwarden, Homepage, and backup jobs.

[Sahale](https://github.com/aidenm727/sahale) also runs on the T430 as a systemd
workload using Node.js and SQLite. It has its own scheduled SQLite snapshot job.

The Proxmox host runs VMs/LXCs, gaming infrastructure, and application
experiments. Workloads can be rebuilt or resized there without tying those
changes to the core-services machine.

## Networking and access

Tailscale provides remote administrative access to the lab. SSH, Proxmox,
dashboards, and other management surfaces remain off the public Internet.

Pi-hole handles local DNS and filtering. Traefik routes private web services and
terminates internal HTTPS. Applications keep their own authentication where
required.

Sahale's documented private HTTPS path uses Tailscale Serve, which forwards to
its loopback listener. Its access controls are described in the
[public application showcase](https://github.com/aidenm727/sahale).

## Virtualization and storage

Proxmox provides VM/LXC isolation. The host uses separate system and workload
storage roles, including an NVMe pool for VM/LXC workloads and additional
archive/preservation storage.

Failure domains are tracked explicitly:

- VM storage provides capacity for guests.
- Hypervisor snapshots provide rollback.
- VM backups stored on the Proxmox host cover guest-level recovery but remain in
  the same physical-host failure domain.
- Preservation data is copied and checked before source storage is repurposed.

## Observability

The monitoring stack is split by function:

```text
Uptime Kuma  -> service reachability
Prometheus   -> metrics collection
Grafana      -> metrics visualization
Loki / Alloy -> centralized logs
```

These signals are used together during troubleshooting. Backup recovery is
checked through restore testing.

## Backup and recovery

The core-services host uses Restic for versioned encrypted backups with local
and Backblaze B2 copies. Vaultwarden restore tests have been performed against
both paths, followed by SQLite integrity checks.

The September baseline also verified scheduled Proxmox backups for the gaming
VM. Those archives stayed on the virtualization host, so total host loss remains
an uncovered failure case for that backup path.

October 14 observations confirmed successful Sahale SQLite snapshot, local
Restic, and off-site Restic jobs. Snapshot inclusion in Restic and Sahale restore
success remain unverified; see [operations](operations.md#october-14-sahale-observations).

## Public repository boundary

The public repo includes hardware specs, technologies, service roles,
architecture, failure-domain notes, and selected dated changes. It excludes
private addresses, internal DNS names, credentials, exact management endpoints,
serial numbers, secret locations, sensitive backup identifiers, and executable
recovery procedures.
