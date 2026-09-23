# Homelab Service Architecture

**Public scope:** service capability classes, separation of responsibilities,
representative technology, and sanitized dated verification.

**Continuity claim:** none; this is not a live service catalog.

## Purpose

The Homelab uses small services with intentionally different responsibilities
rather than one all-purpose control plane. This record explains those roles
without publishing how to reach or administer live systems.

## Capability matrix

| Capability | Engineering role | Representative technology | Evidence pattern |
| --- | --- | --- | --- |
| Owner entry / status | Gives the owner a concise front door without becoming the source of truth | Homepage-class start page | Rendering and service links checked in dated baselines |
| Availability | Answers whether expected service surfaces are responding | Uptime Kuma | Monitor state checked independently from metrics |
| Metrics | Collects host/service measurements | Prometheus / node exporter | Core and virtualization metrics verified |
| Dashboards | Visualizes metrics and trends | Grafana | Metric queries checked in dated baselines |
| Central logs | Supports diagnosis across services | Loki / Alloy | Log ingestion/query behavior verified in historical evidence |
| Local network policy | Provides owner-controlled DNS filtering/name resolution | Pi-hole | Resolution and active query handling checked |
| Private ingress | Routes approved internal services behind an HTTPS boundary | Traefik | Router/service health and certificate behavior checked |
| Password management | Provides an owner data service behind the private access/ingress model | Vaultwarden | Application reachability and backup/restore evidence checked |
| Gaming management | Separates game administration from game workload runtime | Crafty Controller | Management runtime and on-demand workload behavior checked |
| Backup / recovery | Separates creation, retention, independent copies, and restore proof | Restic-class encrypted backup tooling | Dated job-success records plus bounded restore evidence |

The matrix is intentionally capability-oriented. It is not a promise that every
named component is currently running, at a particular version, or reachable.

## Observability separation

The monitoring stack is intentionally layered:

```text
Availability checks  ──>  "is the expected surface responding?"
Metrics collection   ──>  "what is the host/service measuring?"
Dashboards           ──>  "how do those measurements look over time?"
Central logs         ──>  "what happened around an event?"
```

These signals complement each other. None of them alone establishes canonical
system state, backup correctness, or recovery readiness.

## Service boundaries

- Private network access and application authentication are separate controls.
- Reverse proxy reachability does not imply service correctness.
- Monitoring data is observational and can itself be stale or incomplete.
- Application data, configuration, logs, and backups have different retention
  and recovery requirements.
- Game-player ingress and administrative access are separate trust paths.
- Critical services should not gain broad Docker/host authority merely for
  dashboard convenience.
- Secret values and credential references never belong in public Git.

## Backup and owner-data boundary

Password-management data is treated as a recovery-sensitive owner-data class.
Historical and September 2026 evidence demonstrates that backup success is not
accepted solely from a green monitor: local/off-site jobs, repository visibility,
and bounded restore/integrity checks are separate observations.

Other personal-data services remain experimental until their storage,
independent backup, and restore requirements are clear.

## Evidence interpretation

The records demonstrate operation of a small but real network, observability,
ingress, owner-service, recovery, and gaming stack on constrained hardware.

They do not establish:

- continuous availability;
- production scale;
- current patch level or versions;
- complete inventory;
- current public exposure;
- a current recovery-time guarantee; or
- readiness of future local-AI, media, or dedicated-storage systems.

See [the infrastructure architecture](infrastructure.md),
[virtualization architecture](infrastructure-virtualization.md), and
[selected dated evidence](../evidence/).
