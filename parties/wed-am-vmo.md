# Wednesday AM — VM Launchpad (second pass)

Installer: Joseph Valeriano
Slot: Wed 30 Sept, 9:00 to 12:00 PT

## Sign in

- URL: <https://factory.fedlab.xyz/> — **Sign in with SSO**
- Username: `party-guest-3`
- Password: `Spectro123!!`

## Your range

- Range page: <https://factory.fedlab.xyz/ranges/vmo-wed-am>
- BMC console: <https://factory.fedlab.xyz/ranges/vmo-wed-am/bmc>
- Product: VM Launchpad 4.10.13, 3 nodes, empty drives (upload the ISO from the range page)

## Network config for the TUI

haproxy on the bastion is pre-configured for these exact IPs. Assign anything else in the subnet and nothing will route.

| node hostname | IP |
| --- | --- |
| vm-launchpad-1 | 10.240.9.10 |
| vm-launchpad-2 | 10.240.9.11 |
| vm-launchpad-3 | 10.240.9.12 |

- Subnet mask: `255.255.255.0`
- Gateway and DNS: `10.240.9.1`
- Cluster VIP (assign during cluster create, not per node): `10.240.9.5`
