# Operations & Reliability

This page records the operating practices and troubleshooting examples that have
become useful across the lab.

## Operating approach

- Check effective state after configuration changes.
- Make small changes and verify the result.
- Keep core services on the lower-change T430 role.
- Run experiments and heavier workloads behind VM/LXC boundaries.
- Use independent signals for reachability, metrics, logs, and recovery.
- Keep access and recovery details out of public Git.
- Add hardware when workload measurements show a sustained constraint.

## SSH hardening and configuration precedence

During the September 2026 baseline, a new SSH hardening drop-in specified
`PasswordAuthentication no`, while effective configuration still reported
`PasswordAuthentication yes`.

An earlier cloud-init fragment had already set the global value. The fix was to
place the owner-controlled hardening policy earlier in the drop-in order and
leave the cloud-init-managed file untouched.

Verification covered four checks:

1. daemon syntax validation passed;
2. effective configuration showed password authentication disabled;
3. a fresh key-only login succeeded; and
4. a fresh password login failed.

## Exposure testing

Container networking can add packet-filtering rules outside a host firewall's
normal view, so external reachability is checked from an external network.

For the September 2026 baseline, selected SSH/DNS/HTTP/HTTPS ports were tested
from a cellular network with Tailscale disconnected. The tested IPv4 paths did
not reach the lab. IPv6 stayed unresolved because the external client lacked a
working IPv6 route.

## Monitoring model

| Tool | Question |
| --- | --- |
| **Uptime Kuma** | Is the service reachable? |
| **Prometheus** | What metrics are being collected? |
| **Grafana** | How are those metrics changing? |
| **Loki / Alloy** | What logs were recorded around an event? |

The tools cover different parts of diagnosis. Recovery readiness is checked
through restore tests.

## Backup and recovery

The core-services host uses Restic with a local repository and an encrypted
Backblaze B2 copy. Both paths have been used for Vaultwarden restore tests,
followed by a SQLite integrity check.

The Proxmox host also runs scheduled backups for the gaming VM. Those archives
cover guest-level recovery and currently remain on the same physical host.
Off-host VM protection is a future improvement.

The Restic/B2 recovery material also needs an independent safe recovery path so
it is available if the core-services machine is unavailable.

## Resource management

StoneBlock 4 can use an 8 GiB Java heap and accounted for most of the gaming VM's
memory pressure while active. VM memory returned to a low idle footprint after
the server stopped.

Large game servers therefore run on demand. Additional RAM remains an option if
future concurrent workloads create sustained memory pressure.

## Maintenance

Security and package updates are handled during normal maintenance. Phased
updates are allowed to follow their normal rollout, and firmware work is handled
separately from routine package updates.

Ansible is the next likely configuration-management step for repeated host
policy such as SSH settings, packages, Docker prerequisites, monitoring agents,
firewall rules, backup timers, and validation.
