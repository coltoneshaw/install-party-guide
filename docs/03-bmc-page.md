# 3. The BMC console

Each range's BMC console gives you a screen for each appliance node, lets you power a node on and off, and lets you attach an ISO — enough to walk through a product installer as if you were racked next to the hardware.

![The BMC console. One tab per node across the top, remote control on the left, power state and BMC address on the right.](../images/v3-01-bmc.jpg)

## What's on the screen

- A **tab per node** across the top (`palette-vertex-1`, `palette-vertex-2`, `palette-vertex-3` on a VerteX range, `vm-launchpad-1..3` on a VM Launchpad range). Click a tab to select that node.
- For the selected node: **S/N**, **BMC address**, current **power state**, and the Remote Control buttons: **Power On**, **Power Off**, **Reset**, **Launch iKVM**, **Virtual Media**.
- A **live console preview** below the buttons when the node is on.

## Mount the ISO before you power on

The nodes ship with empty disks and no boot media. Before any node will do anything useful you need to attach the vendor ISO.

1. On your **range page** (drop `/bmc` from your BMC URL), upload the vendor ISO once. Every node in the range can attach it — you don't upload per node.
2. Back on the BMC console, pick a node tab. Click **Virtual Media**, pick the uploaded ISO, and mount it.
3. Click **Power On**. The node boots the mounted ISO on its own — there is no boot-order menu to fiddle with.
4. Click **Launch iKVM** to open the node's console in a new window and drive the installer.

After the install finishes and the node reboots, unmount the ISO from **Virtual Media** or the node will boot the installer again on the next reset.

## Network config during the install

The vendor installer's TUI asks for each node's hostname, static IP, subnet mask, gateway, DNS, and a cluster VIP. These are not free choices — the bastion's haproxy is pre-configured for specific values per range, and anything else will not route.

Your party file lists the exact values to type: node hostnames, node IPs, subnet mask, gateway, DNS, and VIP.

## What this page is not

Not a way to reach the operating system after install. Once the appliance is up, you'll ssh to it through the bastion — next section.
