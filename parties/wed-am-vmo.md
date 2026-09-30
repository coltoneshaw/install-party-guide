# Wednesday AM — VM Launchpad (second pass)

Installer: Joseph Valeriano
Slot: Wed 30 Sept, 9:00 to 12:00 PT

**How to work through this:** the [trainee guide](../README.md) walks the flow end to end (sign in, BMC console, SSH bastion, node hop, overlay UIs). This file is only your launch board — everything below is what makes _your_ range different from the walkthrough.

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

## NICs and bonding

Each node has four virtio NICs, all on the same range network (VXLAN overlay, MTU 1450). Build the production shape: two bonds of two.

| NICs (by Proxmox slot) | bond | carries |
| --- | --- | --- |
| net0 + net2 | management bond | node IP from the table above, Kubernetes cluster traffic |
| net1 + net3 | VM data bond | tenant VM traffic (the `br0` bridge), no address on the node |

- The node IP, mask, gateway and DNS above go on the **management bond**, not on a single NIC. haproxy still expects those exact IPs.
- Use bond mode **active-backup**. The NICs land on a Proxmox bridge, which has no LACP partner, so 802.3ad will not negotiate.
- In the installer the NICs show up by Linux name (`enp6s18`, `enp6s19`, ...) in PCI order, which matches net0..net3. Check the MAC if unsure: net1's MAC is the one that starts `02:B0`.
