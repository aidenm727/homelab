# Operations & Reliability

A lot of the useful engineering in this project is not installing software—it is
figuring out how to operate it safely on imperfect hardware without creating
more complexity than the lab needs.

## Operating principles

- Prefer **effective state** over assumptions about configuration files.
- Make one bounded change at a time and verify the result.
- Keep core services boring; experiment behind stronger isolation boundaries.
- Separate monitoring from authoritative state.
- Treat backup creation and restore confidence as different problems.
- Keep exact operational access/recovery detail out of public Git.
- Buy hardware when a real workload demonstrates the constraint.

## SSH hardening: a useful configuration-precedence failure

During the September 2026 baseline, the core-services host appeared to have a
new SSH hardening drop-in with password authentication disabled. Effective
configuration still reported `PasswordAuthentication yes`.

The cause was not that OpenSSH ignored the file. An earlier cloud-init fragment
set the value first, and OpenSSH kept the first obtained global value.

The fix was to put the owner-controlled hardening policy earlier in the drop-in
order rather than editing the cloud-init-managed file. The change was then
verified in four separate ways:

1. daemon syntax validation passed;
2. effective configuration showed password authentication disabled;
3. a fresh key-only login succeeded; and
4. a fresh password-only login failed as expected.

That incident is a good summary of how I want to operate the lab: inspect what
the system is actually doing, understand why, then verify both the success path
and the failure path.

## Exposure testing

Firewall configuration alone is not proof that a service is unreachable from
the Internet—especially when container networking can create its own packet
filtering rules.

For the September baseline, selected SSH/DNS/HTTP/HTTPS ports were tested from a
genuinely separate cellular network with the private overlay disconnected. The
IPv4 attempts did not reach the lab. IPv6 remained explicitly unresolved because
the available external client path did not have working IPv6 routing.

The lesson is durable even though the observation is dated: test exposure from
the side you are making a claim about.

## Monitoring model

The monitoring stack answers different questions:

- **Uptime Kuma:** is the expected service surface responding?
- **Prometheus:** what is being measured?
- **Grafana:** how are those measurements behaving over time?
- **Loki / Alloy:** what happened around an event?

No single green dashboard is treated as proof that the rest of the system is
healthy.

## Backup and recovery

The core-services environment uses Restic for versioned backups with both a
local repository and an encrypted Backblaze B2 copy. The meaningful part is that
both paths have been used for bounded restores of Vaultwarden data, followed by
a SQLite integrity check.

The virtualization host also runs a scheduled snapshot-style backup for the
gaming VM. That is valuable for guest recovery, but because the archive lives on
the same physical host it is not protection from total host loss.

Current improvement candidates include keeping the Restic/B2 recovery material
available independently of the core-services machine and deciding whether the
gaming VM eventually deserves an off-host copy.

## Resource management

The gaming VM is intentionally allowed to be heavy when a game actually needs
it. StoneBlock 4, for example, ran with a Java heap that could grow to 8 GiB and
accounted for almost all of the VM's apparent memory pressure while active.

Once the server was stopped, VM memory returned to a low idle footprint. That
made the right immediate fix "stop the on-demand workload when nobody is using
it," not "buy more RAM because the graph is high."

## Maintenance

Maintenance is intentionally boring. Security/package updates are handled in a
normal maintenance window, phased updates are not forced just to reach an empty
update list, and firmware changes are treated separately from routine package
work.

Future configuration management will probably start with Ansible because the
lab now has enough repeated host policy—SSH, packages, Docker prerequisites,
monitoring agents, firewalls, backup timers, validation—to justify making those
expectations reproducible.
