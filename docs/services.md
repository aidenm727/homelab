# Services

Service inventory from the September 2026 lab snapshot.

## Service map

| Area | Service | Role |
| --- | --- | --- |
| Private access | **Tailscale** | Remote administration of private management surfaces |
| DNS / filtering | **Pi-hole** | Local name resolution and DNS filtering |
| Ingress | **Traefik** | Private HTTPS routing for web services |
| Passwords | **Vaultwarden** | Self-hosted password management |
| Start page | **Homepage** | Owner-facing entry point into the lab |
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

The September 2026 T430 snapshot included the services above plus supporting
health and monitoring containers. CPU, memory, and storage all had headroom for
the current stack.

## Experiments

Immich has been used as an isolated virtualization/application experiment and is
currently low priority. Photo hosting can be revisited once storage and backup
requirements are defined.
