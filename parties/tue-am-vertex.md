# Tuesday AM — VerteX management appliance

Installer: Barbara Iheme
Slot: Tue 29 Sept, 9:00 to 12:00 PT

**How to work through this:** the [trainee guide](../README.md) walks the flow end to end (sign in, BMC console, SSH bastion, node hop, overlay UIs). This file is only your launch board — everything below is what makes _your_ range different from the walkthrough.

## Sign in

- URL: <https://factory.fedlab.xyz/> — **Sign in with SSO**
- Username: `party-guest-2`
- Password: `Spectro123!!`

## Your range

- Range page: <https://factory.fedlab.xyz/ranges/vertex-tue-am>
- BMC console: <https://factory.fedlab.xyz/ranges/vertex-tue-am/bmc>
- Product: VerteX 4.10.17, 3 nodes, empty drives (upload the ISO from the range page)

## Network config for the TUI

haproxy on the bastion is pre-configured for these exact IPs. Assign anything else in the subnet and nothing will route.

| node hostname | IP |
| --- | --- |
| palette-vertex-1 | 10.240.6.10 |
| palette-vertex-2 | 10.240.6.11 |
| palette-vertex-3 | 10.240.6.12 |

- Subnet mask: `255.255.255.0`
- Gateway and DNS: `10.240.6.1`
- Cluster VIP (assign during cluster create, not per node): `10.240.6.5`