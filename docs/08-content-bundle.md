# 8. Load the content bundle from the bastion

The bundle is 12 to 14 GB. Do not try to upload it from your laptop through the appliance's **Upload Content** dialog: the dialog is one request with a 60-minute server-side timeout, and 12 GB over the VPN takes far longer than that. SSH to the bastion (it sits on the range network, no VPN in the path), download the bundle there from Artifact Studio, and push it into node-1 from there.

## Prereqs

- SSH to the bastion working — see [docs/04-ssh-bastion.md](04-ssh-bastion.md).
- The appliance's node-1 is installed and reachable at `10.240.<IDX>.10`.
- An Artifact Studio account and the direct download URL for the bundle you need. Bundle names by product:
  - VerteX 4.10.17: `palette-vertex-appliance-4.10.17.tar.zst` (~12 GB)
  - VM Launchpad 4.10.13: `launchpad-for-vms-4.10.13.tar.zst` (~14 GB)

Vendor product docs and the tools' own `--help` cover the rest.
