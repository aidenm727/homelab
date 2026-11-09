# Homelab

Personal homelab for self-hosting and systems work. I built most of it from
repurposed hardware while studying computer science, and I use it for private
services, monitoring, backups, Minecraft servers, and infrastructure
experiments.

> **Infrastructure baseline: September 23, 2026.** Two later records add to it:
> Sahale runtime and backup-job observations on [October 14](docs/history/2026-10-14-sahale-observations.yml),
> and a restore test from the off-site backup on [October 30](docs/history/2026-10-30-restore-test.yml).
> Neither refreshes the full infrastructure inventory.

## Lab at a glance

| System | Hardware / platform | Role |
| --- | --- | --- |
| **Core services** | Lenovo ThinkPad T430 · Ubuntu Server · ~8 GiB RAM | DNS, private ingress, monitoring, Vaultwarden, Homepage, Sahale, backups |
| **Virtualization host** | Ryzen 5 2600 · 16 GiB DDR4 · Proxmox VE · SATA SSD + NVMe | VMs/LXCs, gaming infrastructure, application experiments |
| **Gaming VM** | Debian 12 · Docker · 4 vCPU · 10 GiB RAM | Crafty Controller, Minecraft workloads, game ingress |
| **Admin / development** | ASUS ROG Zephyrus G14 (2024) · Windows 11 + WSL | SSH administration, development, maintenance, local experiments |

More detail: [hardware and roles](docs/hardware.md).

## Architecture

```text
Tailscale private administration
    ├── ThinkPad T430 / Ubuntu Server
    │     ├── Docker services
    │     │     ├── Pi-hole: local DNS / filtering
    │     │     ├── Traefik: private HTTPS -> Vaultwarden / Homepage
    │     │     ├── Prometheus -> Grafana
    │     │     ├── Alloy -> Loki
    │     │     └── Uptime Kuma: reachability checks
    │     ├── systemd: Sahale (Node.js / SQLite)
    │     │     └── private web access via Tailscale Serve
    │     └── Restic -> local backup + encrypted Backblaze B2
    └── Proxmox / Ryzen host
          ├── VMs / LXCs / experiments
          ├── node exporter -> T430 Prometheus
          └── Debian gaming VM / Docker
                ├── Crafty -> Minecraft <- Playit player ingress
                └── scheduled Proxmox backup -> host-local archive
```

The T430 carries the lightweight services I want available most of the time.
The Proxmox host carries VMs, game servers, and experiments that need more
resources or stronger isolation.

Sahale Fitness is published through Tailscale Serve, separately from Traefik.
That configuration was recorded from the live host on October 30.

[Architecture details](docs/architecture.md)

## Services

- **Networking and access:** Tailscale, Pi-hole, Traefik
- **Observability:** Prometheus, node exporter, Grafana, Loki, Alloy, Uptime Kuma
- **Core services:** Vaultwarden, Homepage, supporting Docker services
- **Personal application:** [Sahale Fitness](https://github.com/aidenm727/sahale-fitness), a systemd-managed
  Node.js/SQLite workout logger on the T430
- **Backup:** Restic with local and encrypted Backblaze B2 copies, plus scheduled
  Proxmox VM backups
- **Gaming:** Crafty Controller, Minecraft, Playit

[Full service map](docs/services.md)

## Screenshots

These captures illustrate the September baseline. They show separate moments,
not a synchronized health check or the October Sahale deployment.

### Services and monitoring

<p align="center">
  <img src="assets/screenshots/homepage.png" alt="Homelab Homepage service dashboard" width="49%">
  <img src="assets/screenshots/grafana.png" alt="Grafana host metrics dashboard" width="49%">
</p>

*Homepage groups private service links. Grafana shows CPU, memory, network, and
disk measurements from the core-services host during a low-load period.*

### Virtualization and gaming

<p align="center">
  <img src="assets/screenshots/proxmox.png" alt="Sanitized Proxmox gaming VM summary showing high memory use" width="36%">
  <img src="assets/screenshots/crafty.png" alt="Crafty Controller showing Vanilla Eval and StoneBlock 4 stopped" width="61%">
</p>

*Proxmox shows the 10 GiB gaming VM at 98% memory use during the active-workload
period. Crafty shows Vanilla Eval and StoneBlock 4 stopped in a separate capture.
The Proxmox image masks private host and address details.*

## Selected engineering work

### SSH hardening and configuration precedence

A new OpenSSH drop-in specified `PasswordAuthentication no`, while the effective
configuration still reported password authentication enabled. An earlier
cloud-init fragment was taking precedence. I moved the owner policy earlier in
the drop-in order, validated the daemon configuration, confirmed a fresh
key-only login, and confirmed a fresh password login failed.

### Backup and restore testing

The core-services host uses Restic for local and encrypted B2 backups. I have
restored the Vaultwarden database from both paths and checked SQLite integrity on
the restored copy. The Proxmox host also keeps scheduled VM backups for the
gaming VM.

On October 30 I restored the T430's configuration and the Sahale Fitness
database from the encrypted off-site copy onto a different machine, using only
an offline recovery kit. The restored database passed a SQLite integrity check
and the restored configuration files matched the recorded ones. Both backup
jobs now report to an outside dead-man's-switch check, so a failed run sends an
alert. See the [dated record and its limits](docs/history/2026-10-30-restore-test.yml).

### External exposure testing

For a September 2026 check, I disconnected Tailscale and tested selected
SSH/DNS/HTTP/HTTPS ports from a cellular network. The tested IPv4 paths did not
reach the lab. IPv6 remained unresolved because the external client did not have
a working IPv6 route.

### On-demand game workloads

A modded StoneBlock server accounted for most of the gaming VM's memory use while
it was running. Stopping the server returned the VM to a low idle footprint, so
large game servers stay on demand.

[Operations and reliability](docs/operations.md)

## Gaming

Minecraft is one of the main workloads on the virtualization host. Crafty
Controller runs inside a dedicated Debian VM, administration stays on Tailscale,
and Playit handles player-facing game traffic.

[Gaming infrastructure](docs/gaming.md)

## Remaining reliability work

The gaming VM's Proxmox archives need an off-host recovery copy to cover host
loss. The October 30 restore test used the off-site copy; the local repository
and a full service rebuild from restored files have not been rehearsed.

## Documentation

- [Hardware & roles](docs/hardware.md)
- [Architecture](docs/architecture.md)
- [Services](docs/services.md)
- [Operations & reliability](docs/operations.md)
- [Gaming infrastructure](docs/gaming.md)
- [Selected changes & snapshots](docs/history/)

The public repo omits credentials, private addresses, internal DNS names, exact
management endpoints, and sensitive recovery details. Dated snapshots provide
historical context; live systems determine current state.
