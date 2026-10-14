# Operations & Reliability

This page records the operating practices and troubleshooting examples that have
become useful across the lab.

Infrastructure evidence comes from the [September 23, 2026 baseline](history/2026-09-23-lab-baseline.yml).
The [October 14 record](history/2026-10-14-sahale-observations.yml) adds Sahale
runtime and backup-job observations.

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
followed by a SQLite integrity check. These restore tests concern Vaultwarden;
they do not demonstrate Sahale recovery.

The Proxmox host also runs scheduled backups for the gaming VM. Those archives
cover guest-level recovery and remained on the same physical host in the
September baseline. Off-host VM protection is a future improvement.

The Restic/B2 recovery material also needs an independent safe recovery path so
it is available if the core-services machine is unavailable.

### October 14 Sahale observations

`sahale.service` was **active/running** on the T430. Sahale's public showcase
describes a Node.js/SQLite application with a separate private Tailscale Serve
access path. Live ingress configuration and the deployed application revision
were not verified in these observations.

The October 14 systemd observations explicitly used **EDT (UTC−04:00)**. The
SQLite snapshot file timestamp carried **`-0400`**, consistent with that offset.

| Job / systemd unit | Observed result | Completion time (EDT) |
| --- | --- | --- |
| SQLite snapshot / `sahale-snapshot.service` | Successful | 02:45:02 |
| Local Restic / `homelab-restic-backup.service` | Successful | 03:00:10 |
| Off-site Restic / `homelab-restic-backup-b2.service` | Successful | 04:00:29 |

The SQLite snapshot file was modified at **02:45:02 EDT** and measured
**274,432 bytes**.

These are separate job results. They do not verify that either Restic repository
included the Sahale SQLite snapshot, and no successful Sahale restore was
demonstrated. Recovery verification still requires checking snapshot inclusion
and restoring Sahale from the backup path. The earlier Vaultwarden restore
tests remain separate evidence.

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
