# Aiden's Homelab

My personal homelab for learning infrastructure by actually running it. I built
most of it from repurposed hardware and use it for private networking,
self-hosted services, monitoring, backups, game servers, and experiments.

I'm a CS student, so learning is a big part of the point, but I try to make the
lab useful first. Most of the things here exist because I wanted a service, hit
a limitation, broke something, or found a systems problem worth understanding.

> **Lab snapshot — September 2026.** Hardware and service details below describe
> a dated snapshot of the lab, not a promise that every component is always
> online or unchanged.

## Lab at a glance

| System | Hardware / platform | What it does |
| --- | --- | --- |
| **Core services** | Lenovo ThinkPad T430 · Ubuntu Server · ~8 GiB RAM | DNS, private ingress, monitoring, Vaultwarden, start page, backups |
| **Virtualization host** | Ryzen 5 2600 · 16 GiB DDR4 · Proxmox VE · SATA SSD + NVMe | VMs/LXCs, gaming infrastructure, application experiments |
| **Gaming VM** | Debian 12 · Docker · 4 vCPU · 10 GiB RAM | Crafty Controller, Minecraft workloads, game ingress |
| **Admin / development** | ASUS ROG Zephyrus G14 (2024) · Windows 11 + WSL | SSH administration, development, maintenance, local experiments |

More detail: [hardware and roles](docs/hardware.md).

## Architecture

```text
                         AIDEN'S HOMELAB

                    Tailscale private access
                             │
                ┌────────────┴─────────────┐
                │                          │
          ThinkPad T430               Proxmox host
          Ubuntu Server               Ryzen 5 2600
                │                          │
          Docker services                VMs / LXCs
                │                          │
     ┌──────────┼──────────┐          Gaming VM
     │          │          │               │
  Pi-hole    Traefik   Observability     Crafty
                         │                 │
                 Prometheus / Grafana   Minecraft
                    Loki / Kuma            │
                                          Playit

                Backup / recovery
                     │
               Restic + B2
```

The lab deliberately separates the lower-change services I want available most
of the time from heavier or more experimental workloads that benefit from VM/LXC
isolation.

[Architecture details →](docs/architecture.md)

## What runs here

**Networking & access** — Tailscale for private administration, Pi-hole for
local DNS/filtering, and Traefik for private HTTPS ingress.

**Observability** — Prometheus and node exporter for metrics, Grafana for
visualization, Loki + Alloy for logs, and Uptime Kuma for simple availability
checks.

**Core services** — Vaultwarden, Homepage, and the supporting Docker services
that make the lab useful day to day.

**Backup & recovery** — Restic for local and encrypted Backblaze B2 backups,
plus scheduled Proxmox VM backups. I treat restore testing as a separate problem
from simply seeing a green backup job.

**Gaming** — Crafty Controller inside a dedicated gaming VM, Minecraft servers,
and Playit for explicit player ingress while administration stays private.

[Full service map →](docs/services.md)

## A few engineering stories

### Private infrastructure without public admin surfaces

Remote administration lives behind Tailscale rather than exposed management
ports. Internal names are resolved through Pi-hole, while Traefik gives private
services a consistent HTTPS entry point.

### Backup means restore

The core-services environment uses versioned Restic backups locally and off-site
in B2. I have restored the Vaultwarden database from both paths and checked the
restored SQLite database rather than treating successful backup jobs as proof of
recoverability.

### Debugging SSH configuration, not just editing it

While hardening the core-services host, `PasswordAuthentication no` was present
in a new drop-in but password authentication was still effective. The cause was
an earlier cloud-init fragment and OpenSSH's first-obtained-value behavior. I
fixed the ordering, validated the daemon configuration, then tested both a
successful key-only login and an expected-failure password login.

### Letting the workload explain the resource problem

A modded StoneBlock server made the gaming VM look memory-constrained. Instead
of assuming a leak or immediately adding hardware, I traced the usage to the
active Java process. Stopping the on-demand server returned the VM to a low idle
memory footprint, which reinforced the decision to keep expensive game workloads
on-demand.

### Building around old hardware

The lab started with machines I already had. That constraint has been useful: it
forced me to think about workload placement, memory pressure, storage roles,
power usage, backup failure domains, and when an upgrade is actually justified.

[How I operate the lab →](docs/operations.md)

## Gaming

Minecraft has been one of the most useful "real" workloads in the lab. It has
pushed me into VM isolation, game-specific ingress, backup/history thinking,
version management, memory debugging, and deciding what should actually run
24/7.

[Gaming infrastructure and roadmap →](docs/gaming.md)

## What I'm exploring next

- a better personalized **Homelab Home** instead of a generic start page;
- **Ansible** for repeatable host configuration and validation;
- local AI/model serving on hardware I already own before buying dedicated AI
  equipment;
- a future gaming/GPU machine that can also provide Sunshine/Moonlight streaming
  and local inference;
- personal media and storage only when a real collection justifies the capacity;
- a disposable Kubernetes/GitOps lab for learning without moving critical
  services into Kubernetes just for the sake of it; and
- weird/fun projects such as the **Aiden TV** CRT channel network.

The goal is not to collect containers. It's to keep turning the lab into a more
useful personal computing environment while learning from the systems problems
that naturally come with it.

## Documentation

- [Hardware & roles](docs/hardware.md)
- [Architecture](docs/architecture.md)
- [Services](docs/services.md)
- [Operations & reliability](docs/operations.md)
- [Gaming infrastructure](docs/gaming.md)
- [Selected changes & snapshots](docs/history/)

This public repository intentionally leaves out credentials, private addresses,
internal DNS names, exact management endpoints, and sensitive recovery detail.
Architecture pages describe the design; dated snapshots preserve selected
history. The live systems remain the source of truth for what is running now.
