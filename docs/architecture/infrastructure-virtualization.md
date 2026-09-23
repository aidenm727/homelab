# Homelab Virtualization Architecture

**Public scope:** virtualization roles, storage/failure-domain reasoning, and
sanitized dated evidence.

**Continuity claim:** none; current version, health, capacity, and guest state
require fresh authorized observation.

## Role

The virtualization environment is the flexible compute boundary for isolated
VM/LXC workloads, gaming infrastructure, application experiments, and future
higher-resource work. It is intentionally distinct from the lower-change
core-services environment.

Dated records document Proxmox VE on repurposed
workstation-class hardware. Public documentation keeps the technology and
engineering reasoning while omitting real host identity, exact hardware
inventory, management paths, private addresses, guest identifiers, and device or
storage paths.

## Storage and failure boundaries

The June 2026 storage record documents a deliberate transition of existing
solid-state capacity into a workload-oriented virtualization pool only after an
existing archive copy was verified on separate media.

The important engineering distinction is failure domain, not disk count:

- workload storage provides capacity and isolation for guests;
- hypervisor-local snapshots/backups improve rollback and guest recovery;
- a copy on the same physical host is not protection from complete host loss;
- preservation data should be verified before storage ownership changes;
- durable independent/off-site protection is a separate control.

## Workload isolation

Virtualization is used to keep workloads with different change rates and failure
profiles from becoming coupled to the core-services host.

Documented examples include:

- an isolated Debian-class LXC application boundary;
- Docker Compose deployed inside a guest rather than directly on the hypervisor;
- node-level metrics added to the existing monitoring model;
- private overlay administration of the virtualization management surface; and
- a dedicated gaming VM that separates game-server management/runtime work from
  always-on network and owner services.

The public record intentionally does not enumerate the current guest inventory.

## Gaming workload pattern

The gaming environment is treated as a distinct workload class rather than an
extension of the core-services host.

A September 2026 dated baseline documented:

- an isolated VM hosting a Crafty-based game-server management runtime;
- separation between private administration and explicit player ingress;
- an on-demand modded Minecraft workload whose high memory use was traced to
  the active Java server process rather than unexplained host pressure; and
- successful return to low idle memory after the game workload was stopped.

This supports a practical operating model: persistent management infrastructure
can remain available while expensive game workloads run only when needed.

## Backup pattern

The virtualization environment has a scheduled compressed snapshot-style backup
for the gaming VM. The September 2026 baseline verified that the job existed,
was enabled, and had produced a scheduled archive.

That is useful guest-level recovery evidence, but the copy remains local to the
virtualization host. It therefore does not protect against complete host or
local-backup-media loss.

## Migration and experimentation principles

- Keep application data ownership explicit and prefer portable exports.
- Separate guest configuration, application state, attachments, and backup
  ownership.
- Treat hypervisor-local snapshots as rollback/recovery tools, not independent
  protection.
- Verify restores before describing a workload as dependable.
- Avoid unnecessary dependence on a specific host identity, private route,
  device path, or hypervisor-local storage layout.
- Keep experimental or learning environments disposable when possible.
- Record exact restricted operational artifacts only when repeated use proves
  they need durable private version control.

## Public boundary

The public record owns virtualization roles, representative technology,
dated outcomes, failure-domain reasoning, and migration principles.

Exact guest inventory, resource assignments, addressing, private DNS,
management endpoints, device/storage paths, backup destinations, executable
recovery steps, and current runtime state remain private/live.

See [the infrastructure architecture](infrastructure.md),
[service capability architecture](services.md), and
[selected dated evidence](../evidence/).
