# Install Party — 29 Sept to 1 Oct 2026, San Jose

Planning sheet: [Install Party](https://docs.google.com/spreadsheets/d/1PWtJY5ZmOC57KGogt_osac3cVStNS_WjHzS3m6yyqKI/edit)

Read your assigned group's file first — everything you need to start is in that one page. This README is the event overview.

## Install groups (one file per group)

| slot | product | installer | account | file |
| --- | --- | --- | --- | --- |
| Mon 28 Sept PM | Dry run (VerteX shape) | Bill DeCoste | `party-guest-1` | [mon-dry-run.md](mon-dry-run.md) |
| Tue 29 Sept AM | VerteX mgmt appliance | Joe MacLennan | `party-guest-2` | [tue-am-vertex.md](tue-am-vertex.md) |
| Tue 29 Sept AM | VerteX mgmt appliance | Barbara Iheme | `party-guest-5` | [tue-am-vertex-2.md](tue-am-vertex-2.md) |
| Tue 29 Sept PM | VM Launchpad | Jacob Helton | `party-guest-4` | [tue-pm-vmo.md](tue-pm-vmo.md) |
| Wed 30 Sept AM | VM Launchpad | Joseph Valeriano (JV) | `party-guest-3` | [wed-am-vmo.md](wed-am-vmo.md) |

All accounts share password `Spectro123!!`. Host accounts (`party-host-1..3`) are collaborators on every range — see [hosts.md](hosts.md).

## Rules for the party

- **Work from the public product docs only** during the install. Log doc gaps in the Issue tab of the planning sheet.
- **Narrate as you go**: what you're looking for, what you expected, when you're unsure. The observer times you; you don't time yourself.
- **Stuck for 15 minutes**: log what stopped you, then ask a host.
- Nobody installs a product they own.

## Break-glass contact

If the factoryd sign-in page is down, or a range is unreachable and you've already reconnected the VPN: Colton

## After the party

Accounts, keypairs, and ranges are single-use. On or after 2 Oct 2026:

- The 9 `party-*` Keycloak accounts get deleted.
- The 6 factoryd ranges get destroyed.
- Delete any range-specific certs from your `~/.ssh/`.
