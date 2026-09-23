# Architecture

The Homelab is intentionally split into a small always-on core and a more
flexible virtualization environment. The point is not maximum complexity; it is
to keep important lightweight services stable while still having room to break,
rebuild, and experiment elsewhere.

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
                    └── explicit player ingress via Playit
```

No public management path is required by this design. Player-facing game ingress
is deliberately separate from administrative access.

## Why two compute roles?

The core-services machine runs things I want to be predictable and inexpensive
to leave on: DNS, ingress, monitoring, password management, and backups.

The Proxmox machine is the opposite side of the lab. It is where workloads can
have stronger isolation, use more resources, or be rebuilt without coupling
those changes to the core network/services machine.

This separation is simple, but it has paid off repeatedly—especially for gaming
and application experiments.

## Private networking and access

Tailscale is the administrative network. Remote management does not depend on
opening SSH, Proxmox, dashboards, or other admin surfaces directly to the public
Internet.

Pi-hole provides local DNS/filtering. Traefik is the ingress boundary for
private web services and internal HTTPS. DNS, ingress, and application
authentication are intentionally separate controls.

## Virtualization and storage

Proxmox provides the isolation boundary for VM/LXC workloads. The virtualization
host separates system storage from a dedicated NVMe workload pool, with
additional archive/preservation storage kept as another role.

The important distinction is failure domain:

- VM storage is capacity, not backup.
- A hypervisor snapshot is useful rollback, not independent protection.
- A VM backup stored on the same physical host helps with guest failure but does
  not protect against complete host loss.
- Data worth preserving should have an independent copy before storage is
  repurposed.

## Observability

Monitoring is deliberately layered rather than collapsed into one dashboard:

```text
Uptime Kuma  -> Is the expected surface responding?
Prometheus   -> What is the host/service measuring?
Grafana      -> What do those measurements look like over time?
Loki / Alloy -> What happened around an event?
```

These tools are useful observations, not the source of truth for system state or
backup recoverability.

## Backup and recovery

The core-services backup path uses Restic for versioned encrypted backups with
both local and Backblaze B2 copies. Restore testing is treated separately from
job success.

The gaming VM also has a scheduled Proxmox backup, but that copy is currently in
the same physical-host failure domain. That is a known limitation rather than a
reason to pretend the backup is more resilient than it is.

## Public/private boundary

The public repo can safely show hardware classes/specs, technologies, service
roles, architecture, failure-domain reasoning, and selected dated engineering
stories.

It intentionally does not publish private addresses, internal DNS names,
credentials, exact management endpoints, serial numbers, secret locations,
sensitive backup identifiers, or executable recovery procedures.
