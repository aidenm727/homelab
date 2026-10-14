# Services

Infrastructure service inventory from the [September 23, 2026 baseline](history/2026-09-23-lab-baseline.yml),
with Sahale added from the [October 14 observations](history/2026-10-14-sahale-observations.yml).
The later observations do not establish current state for the full inventory.

## Service map

| Area | Service | Role |
| --- | --- | --- |
| Private access | **Tailscale** | Remote administration of private management surfaces |
| DNS / filtering | **Pi-hole** | Local name resolution and DNS filtering |
| Ingress | **Traefik** | Private HTTPS routing for web services |
| Application access | **Tailscale Serve** | Documented separate private HTTPS path to Sahale's loopback listener; live configuration unverified |
| Passwords | **Vaultwarden** | Self-hosted password management |
| Start page | **Homepage** | Owner-facing entry point into the lab |
| Personal application | **[Sahale](https://github.com/aidenm727/sahale)** | Fitness, career tracking, and AI-assisted workflows; Node.js/SQLite under systemd on the T430 |
| Metrics | **Prometheus + node exporter** | Host/service metrics collection |
| Dashboards | **Grafana** | Metrics visualization and trends |
| Logs | **Loki + Alloy** | Centralized log collection/querying |
| Availability | **Uptime Kuma** | Service reachability monitoring |
| Backup | **Restic** | Versioned encrypted local/off-site backups |
| Gaming | **Crafty Controller** | Minecraft server management inside the gaming VM |
| Game ingress | **Playit** | Player-facing game connectivity |
| Game workload | **Minecraft** | Vanilla/modded worlds and server experiments |

## Core service relationships

```text
Pi-hole
  └── local names / filtering

Tailscale
  └── private administrative access

Traefik
  └── internal HTTPS routing

Tailscale Serve
  └── Sahale (systemd / Node.js / SQLite)
        └── scheduled SQLite snapshots

Prometheus ──> Grafana
     │
     └── node/service measurements

Alloy ──> Loki
  └── logs

Uptime Kuma
  └── reachability checks

Restic
  ├── local versioned backup
  └── encrypted off-site backup
```

Each layer has a specific job. Kuma checks reachability, Prometheus collects
metrics, Grafana visualizes them, Loki stores logs, and Restic provides backup
history. Restore testing is the recovery check.

## Core-services stack

The September 2026 T430 snapshot included the Docker infrastructure services
and supporting health and monitoring containers. CPU, memory, and storage had
headroom for that stack at the time of the baseline. Sahale's runtime comes from
the October observations; the Tailscale Serve path follows its public showcase
design, with live ingress configuration and deployed revision unverified.

The October observations verified Sahale's systemd runtime and successful
SQLite snapshot, local Restic, and off-site Restic jobs. They did not verify that
the Sahale snapshot was included in Restic or that Sahale had been restored.
See [operations](operations.md#october-14-sahale-observations) for those limits.

## Experiments

Immich was deployed as an isolated virtualization/application experiment in
[June 2026](history/2026-06-24-immich-experiment.yml). Its later runtime state is
not established by the September or October records.
