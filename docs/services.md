# Services

This is a service-oriented view of the lab: what each piece is for and how the
pieces relate. It is not a live status page.

## September 2026 service snapshot

| Area | Service | Role |
| --- | --- | --- |
| Private access | **Tailscale** | Remote administration without exposing management surfaces publicly |
| DNS / filtering | **Pi-hole** | Local name resolution and DNS filtering |
| Ingress | **Traefik** | Private HTTPS routing for web services |
| Passwords | **Vaultwarden** | Self-hosted password-management service |
| Start page | **Homepage** | Current owner-facing entry point into the lab |
| Metrics | **Prometheus + node exporter** | Host/service metrics collection |
| Dashboards | **Grafana** | Metrics visualization and trends |
| Logs | **Loki + Alloy** | Centralized log collection/querying |
| Availability | **Uptime Kuma** | Simple service reachability monitoring |
| Backup | **Restic** | Versioned encrypted local/off-site backups |
| Gaming | **Crafty Controller** | Minecraft server management inside the gaming VM |
| Game ingress | **Playit** | Explicit player-facing game connectivity without exposing admin services |
| Game workload | **Minecraft** | Vanilla/modded worlds and game-server experiments |

## How the core services fit together

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

The separation matters. A Grafana dashboard looking healthy does not prove a
service is reachable. A Kuma check being green does not prove a backup can be
restored. Traefik routing successfully does not replace application-level
authentication.

## Core-services container stack

The September 2026 core-services snapshot included the main services above plus
supporting health/monitoring containers. The machine had enough CPU, memory, and
storage headroom that the stack did not need to be moved simply for capacity.

## Experiments

Not every service that gets tried becomes permanent infrastructure. Immich, for
example, has been used as an isolated virtualization/application experiment but
is not treated as a critical current service. Photo hosting only becomes worth
revisiting once storage and backup requirements are strong enough to support it
properly.

That is the general rule for the lab: experiments are cheap; permanent services
have to earn their operational cost.
