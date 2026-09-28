# 8. Load the content bundle from the bastion

The bundle is 12 to 14 GB. Do not try to upload it from your laptop through the appliance's **Upload Content** dialog: the dialog is one request with a 60-minute server-side timeout, and 12 GB over the VPN takes far longer than that. SSH to the bastion (it sits on the range network, no VPN in the path), download the bundle there, and push it into node-1 from there. On a warm range this takes under five minutes end to end.

This page is bastion mechanics for the party. **What to do next with the bundle** (create the cluster, wizard steps, profile config) is in the vendor's product docs.

## Prereqs

- SSH to the bastion working ([docs/04-ssh-bastion.md](04-ssh-bastion.md) done).
- The appliance's node-1 is installed and reachable at `10.240.<IDX>.10` (you finished the vendor TUI + local-UI first-boot).
- An Artifact Studio account and the direct download URL for the bundle you need. Bundle names by product:
  - VerteX 4.10.17: `palette-vertex-appliance-4.10.17.tar.zst` (~12 GB)
  - VM Launchpad 4.10.13: `launchpad-for-vms-4.10.13.tar.zst` (~14 GB)

## 1. Do it on the bastion

SSH in, download the bundle directly from Artifact Studio, and upload it to node-1 (`10.240.<IDX>.10`). Only node-1 accepts uploads; it shares the content out to node-2 and node-3 from there.

The bastion has lab-network egress and Artifact Studio is reachable, so the download runs from there without VPN in the path. Work out the exact download and upload invocations from the vendor's product docs and the tools' own `--help`. Keep both on the bastion — never through your laptop.

## 2. Wait for the linked hosts to sync

**VerteX only.** In the Local UI on node-1, open **Linked Edge Hosts**. Wait until the **Content** column reads **Synced** for all three hosts (a few minutes). Only then create the cluster.

**VM Launchpad:** there is no sync to wait for (it is a one-node cluster). As soon as the upload finishes, move on to the cluster wizard.

## When something looks off

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| Upload stalls at 0 B/s for minutes | Node-1 disk full or agent wedged | Check disk on node-1 — if the disk is full, ask a host |
| `Content: Not Synced` on node-2/3 after 10+ min (VerteX) | Overlay routing between nodes | Ask a host before waiting longer; a rebuild is faster than a diagnosis |
