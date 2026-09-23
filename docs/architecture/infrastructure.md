# Homelab Public Infrastructure Architecture

**Public scope:** durable architecture, trust boundaries, engineering patterns,
and sanitized dated evidence.

**Continuity claim:** none; current runtime state requires fresh authorized
observation.

## Purpose

This document describes how the Homelab is structured without publishing the
details required to reach, enumerate, or administer live systems.

The public record may describe roles, capability classes, representative
technology, failure boundaries, and dated verification outcomes. It deliberately
omits private addressing, private DNS identities, current inventory, exact
endpoints, ports, host or guest identifiers, storage paths, backup destinations,
credential references, and executable recovery procedures.

## Role topology

```text
Owner devices
    │
    └── private access boundary
             │
             ├── Core-services environment
             │     ├── local network policy / name resolution
             │     ├── internal HTTPS ingress
             │     ├── availability / metrics / logs
             │     ├── selected owner services
             │     └── backup coordination
             │
             └── Virtualization environment
                   ├── isolated application workloads
                   ├── gaming workload boundary
                   └── disposable / experimental compute
```

The core-services role favors continuity and lower change frequency. The
virtualization role favors isolation, experimentation, and workloads that need a
stronger resource boundary.

Role names are architectural aliases, not reachable system identities.

## Capability classes

| Class | Public pattern | Representative technology |
| --- | --- | --- |
| Private access | Administrative paths stay behind owner-controlled overlay access | Tailscale |
| Network services | Local name resolution and filtering are separate from public Internet ingress | Pi-hole |
| Internal ingress | A dedicated reverse proxy terminates private HTTPS and routes approved services | Traefik |
| Availability | Simple service checks answer whether an expected surface is responding | Uptime Kuma |
| Metrics | Host/service measurements are collected independently from presentation | Prometheus / node exporter |
| Dashboards | Detailed operational visualization is separate from health truth | Grafana |
| Logs | Centralized logs support diagnosis without becoming canonical state | Loki / Alloy |
| Owner services | Selected private applications run behind the same network and evidence boundaries | Vaultwarden- and media-workflow classes |
| Gaming | Game management and runtime workloads are isolated from core services and can remain on-demand | Crafty / Minecraft-class workloads |
| Backup / recovery | Local versioning, independent encrypted copies, off-site protection, and restore proof are separate controls | Restic-class tooling |

Representative technology is included because the engineering choices are useful
to understand. This table is not a current service inventory.

## Trust boundaries

- The owner is the authority for access, changes, acceptance, and recovery
  decisions.
- Private overlay access reduces management exposure but does not replace
  application authentication, patching, least privilege, or backup.
- Internal ingress is a routing/certificate boundary, not a source of truth for
  service health.
- Dashboards and monitoring are observations, not authoritative system state.
- Application data, logs, configuration, snapshots, and backup copies have
  different retention and recovery concerns.
- Public documentation does not grant operational authority and cannot prove
  current reachability.

## Security and operational discipline

The project favors explicit verification over configuration-by-assumption.

Public-safe dated records may preserve patterns such as:

- inspecting the effective daemon configuration rather than trusting a file
  fragment;
- validating syntax before reload/restart;
- preserving an existing administrative session while changing remote-access
  policy;
- testing both an expected-success path and an expected-failure path;
- checking Internet reachability from a genuinely external network before
  describing a surface as non-public;
- keeping unresolved facts, such as an untested address family, explicit rather
  than normalizing them into a stronger claim.

Exact commands, addresses, firewall state, and current exposure results remain
private/live unless a sanitized dated record is deliberately published.

## Backup and recovery pattern

```text
Application-owned data
    ├── portable export when supported
    ├── local versioned backup
    ├── encrypted independent copy
    └── encrypted off-site copy
             │
             └── dated integrity / restore verification

Virtual machine state
    └── scheduled hypervisor backup
             └── explicitly documented failure domain
```

Snapshots, local backup, independent backup, off-site protection, and restore
proof are different controls. A successful backup job is not equivalent to a
verified restore, and a hypervisor-local copy is not protection from complete
host loss.

## Dated evidence

Selected public-safe records include:

- storage-pool creation with an explicit preservation boundary;
- private overlay administration for the virtualization environment;
- virtualization-host metrics integrated into the monitoring model;
- an isolated application workload deployed on the virtualization environment;
- a September 2026 operational baseline covering service reachability,
  observability, SSH hardening, public-IPv4 exposure testing, backup continuity,
  and on-demand gaming behavior.

See [selected dated engineering evidence](../evidence/). Each record is bounded
to its observation date.

## Known limits

- Public documentation is intentionally not a live inventory and does not
  establish current health, exposure, or deployed optional capabilities.
- Hypervisor-local VM backup remains in the virtualization host's failure domain.

## Related records

- [Virtualization and storage](infrastructure-virtualization.md)
- [Service capability architecture](services.md)
- [Selected dated engineering evidence](../evidence/)
- [Sahale](https://github.com/aidenm727/sahale) — related peer project that may
  use Homelab infrastructure where appropriate
