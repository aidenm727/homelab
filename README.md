# Homelab

A public engineering record for a small, independently operated homelab focused
on practical infrastructure: virtualization, Linux systems, private networking,
containerized services, observability, backup/recovery, gaming infrastructure,
and bounded experimentation.

The project is intentionally more than a service list. It documents the
architecture, trust boundaries, failure domains, and verification practices that
make the lab understandable and maintainable over time.

## What this project demonstrates

- **Virtualization and workload isolation** with Proxmox VE, VM/LXC boundaries,
  and explicit storage/failure-domain reasoning.
- **Linux infrastructure** operated as separate lower-change core-services and
  flexible compute environments.
- **Private administration** over an owner-controlled overlay network rather
  than public management exposure.
- **Internal DNS and HTTPS ingress** using DNS filtering/name resolution and a
  dedicated reverse-proxy boundary.
- **Containerized self-hosting** for owner services, monitoring, and experiments.
- **Observability separation** across availability checks, metrics, dashboards,
  and logs.
- **Backup and recovery engineering** that distinguishes local snapshots,
  independent encrypted copies, off-site protection, and restore verification.
- **Gaming infrastructure** with an isolated management/runtime boundary and
  intentionally on-demand game workloads.
- **Security and operations discipline** based on effective-configuration
  inspection, least-exposure defaults, positive/negative verification, and
  dated evidence rather than assumptions.

## Architecture at a glance

```text
Owner devices
    │
    └── private overlay access
             │
             ├── Core-services environment
             │     ├── DNS / network policy
             │     ├── internal HTTPS ingress
             │     ├── availability / metrics / logs
             │     ├── selected owner services
             │     └── backup coordination
             │
             └── Virtualization environment
                   ├── isolated application workloads
                   ├── gaming workload boundary
                   └── disposable / experimental compute

Protected data
    ├── local versioned backup
    └── encrypted independent / off-site copy
             └── dated restore verification
```

This diagram describes architectural roles, not a live inventory or reachable
topology.

## Technology

Representative technologies documented by dated evidence include:

- Proxmox VE
- Linux
- Docker / Docker Compose
- Tailscale
- Pi-hole
- Traefik
- Prometheus
- Grafana
- Loki / Alloy
- Uptime Kuma
- Restic
- Crafty Controller
- Minecraft

Technology names explain engineering choices; they do not imply that every
component is currently online or unchanged.

## Engineering records

- [Infrastructure roles and trust boundaries](docs/architecture/infrastructure.md)
- [Virtualization, storage, and workload isolation](docs/architecture/infrastructure-virtualization.md)
- [Service capability architecture](docs/architecture/services.md)
- [Selected dated engineering evidence](docs/evidence/)
- [Repository instructions and public-safety boundary](AGENTS.md)

The architecture records began as a public-safe extraction of earlier Sahale
infrastructure work and are now owned here. Sahale is a separate peer project
that may use Homelab compute or services when appropriate; it does not define
Homelab architecture or ownership.

## Evidence and freshness

This repository deliberately separates **architecture** from **runtime state**.

Architecture documents describe durable roles, boundaries, and engineering
reasoning. Evidence records describe what was verified on a specific date.
Neither is a promise that a live system still has the same version, inventory,
health, address, route, or recovery readiness.

Current runtime reality belongs to fresh authorized observation of the live
systems. The public repository therefore omits exact private addressing, DNS
identities, credentials, secret locations, live inventory, current health
snapshots, and executable recovery procedures.

A dated September 23, 2026 public-safe baseline is indexed with the
[selected engineering evidence](docs/evidence/).

## Direction

Near-term work is intentionally demand-driven rather than a race to add
services. Candidate directions include a better owner-facing Homelab home,
repeatable configuration management, selected personally useful self-hosted
services, local-AI experimentation on existing hardware, a more cohesive gaming
experience, and future storage/media work when real capacity requirements
justify it.

The goal is a useful personal computing environment that remains understandable
to operate and credible to inspect—not a collection of containers for its own
sake.
