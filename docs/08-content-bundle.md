# 8. Load the content bundle from the bastion

The bundle is 12 to 14 GB. Don't upload it from your laptop through the appliance's **Upload Content** dialog: one request, 60-minute server-side timeout, and 12 GB over the VPN takes far longer than that. Do it from the bastion.

## SSH to the bastion

```
ssh <range-name>
```

For a range called `vertex-tue-am`:

```
ssh vertex-tue-am
```

Setup (SSH config, cert): [docs/04-ssh-bastion.md](04-ssh-bastion.md).

## SSH to the appliance nodes (via the bastion)

```
ssh <range-name>-n1
ssh <range-name>-n2
ssh <range-name>-n3
```

Same range, node-1:

```
ssh vertex-tue-am-n1
```

## Upload with the palette CLI (from the bastion)

The bastion has the `palette` CLI and, for the party, the bundle already sits in the `pubsec` home directory. Run the upload there, against the node's **overlay IP from your party file** (the `10.240.x.10` address you set in the TUI), not the address of any other machine:

```
cd ~
palette content upload --file <bundle>.tar.zst --token <token> --tls=false 10.240.<N>.10
```

- **Token**: open the node's Local UI (`https://10.240.<N>.10:5080` through the overlay UI page), go to the content upload dialog and copy the token shown there. It is per node: a token from node 2 gets `401 Unauthorized` on node 1.
- **Watch the end of the token.** A terminal that wraps the line can show a `$` after it. That `$` is not part of the token and gives a `401 Unauthorized`.
- `connection timed out` on port 5082 means the address is wrong or you are running from your laptop. Only the bastion reaches the nodes on 5082.
- The upload is chunked and resumable: if it stops, run the same command again and it continues where it left off.
- To keep it running after you close the terminal:

```
nohup palette content upload --file <bundle>.tar.zst --token <token> --tls=false 10.240.<N>.10 > ~/upload.log 2>&1 &
tail -f ~/upload.log
```

## Artifact Studio

<https://artifact-studio.spectrocloud.com/>
