# Architecture

The Homelab has two main compute roles: a small always-on core-services machine
and a Proxmox virtualization host. This keeps DNS, ingress, monitoring, password
management, and backups independent from heavier VMs and game workloads.

## Topology

```text
Owner devices
    │
    │  Tailscale private administration
    ▼
┌──────────────────────────┐        ┌──────────────────────────┐
│ Core services            │        │ Virtualization           │
│ ThinkPad T430 / Ubuntu   │        │ Proxmox / Ryzen host     │
│                          │        │                          │
│ Pi-hole                  │        │ VM / LXC workloads       │
│ Traefik                  │        │   ├── gaming VM          │
│ Vaultwarden              │        │   └── experiments        │
│ Homepage                 │        │                          │
│ Prometheus / Grafana     │◄───────┤ node metrics             │
│ Loki / Alloy             │        │                          │
│ Uptime Kuma              │        └──────────────────────────┘
│ Restic                   │
└────────────┬─────────────┘
             │
             ├── local versioned backup
             └── encrypted Backblaze B2 copy

Gaming VM
    └── Crafty Controller
            └── Minecraft workloads
                    └── player ingress via Playit
```

Administration uses Tailscale. Player-facing game traffic uses a separate Playit
path.

## Compute roles

The T430 runs lower-resource services that benefit from staying available:
DNS, ingress, monitoring, Vaultwarden, Homepage, and backup jobs.

The Proxmox host runs VMs/LXCs, gaming infrastructure, and application
experiments. Workloads can be rebuilt or resized there without tying those
changes to the core-services machine.

## Networking and access

Tailscale provides remote administrative access to the lab. SSH, Proxmox,
dashboards, and other management surfaces remain off the public Internet.

Pi-hole handles local DNS and filtering. Traefik routes private web services and
terminates internal HTTPS. Applications keep their own authentication where
required.

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
and Backblaze B2 copies. Restore tests have been performed against both paths.

The gaming VM also has scheduled Proxmox backups. Those archives currently stay
on the virtualization host, so total host loss remains an uncovered failure
case for that backup path.

## Public repository boundary

The public repo includes hardware specs, technologies, service roles,
architecture, failure-domain notes, and selected dated changes. It excludes
private addresses, internal DNS names, credentials, exact management endpoints,
serial numbers, secret locations, sensitive backup identifiers, and executable
recovery procedures.
