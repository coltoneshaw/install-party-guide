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

## 3. Read node-1's upload token

The token rotates every time the node's agent restarts, so read it just before you upload:

```
ssh <range-name>-n1 sudo cat /opt/spectrocloud/.upload-auth-token
```

A successful read prints one hex-ish line, no trailing newline:

```
a1b2c3d4e5f6789012345678abcdef01
```

Copy it. That is the value for `--token` in the next step.

## 4. Upload to the leader

Only node-1 accepts the bundle; it then shares it with node-2 and node-3.

```
~/bin/palette content upload \
  -f palette-vertex-appliance-4.10.17.tar.zst \
  --token <token> \
  10.240.<IDX>.10
```

The CLI splits the file into chunks, sends them in parallel, checksums each one, and resumes if interrupted. A healthy run tails off with:

```
chunk 245/245  ok
extraction complete: content is ready on the host
```

Substitute `launchpad-for-vms-4.10.13.tar.zst` for the VM Launchpad range. Same steps otherwise.

## 5. Wait for the linked hosts to sync

**VerteX only.** In the Local UI on node-1, open **Linked Edge Hosts**. Wait until the **Content** column reads **Synced** for all three hosts (a few minutes). Only then create the cluster.

**VM Launchpad:** there is no sync to wait for (it is a one-node cluster). As soon as `extraction complete` prints, move on to the cluster wizard.

## When something looks off

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| `palette content upload` fails with `401 unauthorized` | Token stale (agent restarted since you read it) | Re-read the token (step 3) and retry the upload — palette-cli resumes from the last verified chunk |
| Upload stalls at 0 B/s for minutes | Node-1 disk full or agent wedged | `ssh <range-name>-n1 df -h /opt/spectrocloud` — if the disk is full, ask a host |
| `Content: Not Synced` on node-2/3 after 10+ min (VerteX) | Overlay routing between nodes | Ask a host before waiting longer; a rebuild is faster than a diagnosis |
