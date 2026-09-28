# 8. Load the content bundle from the bastion

The bundle is 12 to 14 GB. Do not try to upload it from your laptop through the appliance's **Upload Content** dialog: the dialog is one request with a 60-minute server-side timeout, and 12 GB over the VPN takes far longer than that. Fetch the bundle onto the bastion (bastion sits on the range network, no VPN in the path) and push it into node-1 with **palette-cli** from there. On a warm range this takes under five minutes end to end.

This page is bastion mechanics for the party. **What to do next with the bundle** (create the cluster, wizard steps, profile config) is in the vendor's product docs.

## Prereqs

- SSH to the bastion working ([docs/04-ssh-bastion.md](04-ssh-bastion.md) done).
- The appliance's node-1 is installed and reachable at `10.240.<IDX>.10` (you finished the vendor TUI + local-UI first-boot).
- An Artifact Studio account and the direct download URL for the bundle you need. Bundle names by product (find them in Artifact Studio):
  - VerteX 4.10.17: `palette-vertex-appliance-4.10.17.tar.zst` (~12 GB)
  - VM Launchpad 4.10.13: `launchpad-for-vms-4.10.13.tar.zst` (~14 GB)

## 1. Install palette-cli on the bastion

From your laptop:

```
ssh <range-name>
```

Then on the bastion:

```
mkdir -p ~/bin
curl -fL -o ~/bin/palette https://software.spectrocloud.com/palette-cli/v4.10.0/linux/cli/palette
chmod +x ~/bin/palette
~/bin/palette version
```

A successful run of `palette version` prints something like:

```
Palette CLI Version: 4.10.0
```

## 2. Put the bundle on the bastion

Fetch it straight from Artifact Studio (the bastion has lab-network egress, and Artifact Studio is reachable). Don't hand-build the URL — the path segments (FIPS vs non-FIPS, product code, sub-directory) vary per artifact and change over time. Open Artifact Studio in a browser, find the bundle by name, and copy its direct download URL, then run on the bastion:

```
curl -fL -o <bundle-filename>.tar.zst \
  '<paste URL from Artifact Studio>' \
  -u "$ARTIFACT_USER:$ARTIFACT_PW"
```

Set `$ARTIFACT_USER` and `$ARTIFACT_PW` to your own Artifact Studio credentials — `export` them once at the start of the session so the password stays out of shell history. On success:

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 11.4G  100 11.4G    0     0  38.2M      0  0:05:04  0:05:04 --:--:-- 42.1M
```

If your only copy is on your laptop, `scp` it up instead. Over the VPN that takes over an hour:

```
scp palette-vertex-appliance-4.10.17.tar.zst <range-name>:
```

If the copy is interrupted, resume with sftp instead of starting over:

```
echo 'reput palette-vertex-appliance-4.10.17.tar.zst' | sftp -b - <range-name>
```

## 3. Upload it to node-1 with palette-cli

Push the bundle from the bastion into node-1 (`10.240.<IDX>.10`). Only node-1 accepts uploads; it shares the content out to node-2 and node-3 from there. `palette content upload --help` on the bastion has the flags, and the appliance surfaces the auth material you'll need — work out the exact invocation from those two.

## 4. Wait for the linked hosts to sync

**VerteX only.** In the Local UI on node-1, open **Linked Edge Hosts**. Wait until the **Content** column reads **Synced** for all three hosts (a few minutes). Only then create the cluster.

**VM Launchpad:** there is no sync to wait for (it is a one-node cluster). As soon as the upload finishes, move on to the cluster wizard.

## When something looks off

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| Upload stalls at 0 B/s for minutes | Node-1 disk full or agent wedged | `ssh <range-name>-n1 df -h /opt/spectrocloud` — if the disk is full, ask a host |
| `Content: Not Synced` on node-2/3 after 10+ min (VerteX) | Overlay routing between nodes | Ask a host before waiting longer; a rebuild is faster than a diagnosis |
