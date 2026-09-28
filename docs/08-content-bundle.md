# 8. Load the content bundle from the bastion

The bundle is 12 to 14 GB. Don't upload it from your laptop through the appliance's **Upload Content** dialog: one request, 60-minute server-side timeout, and 12 GB over the VPN takes far longer than that. Do it from the bastion.

## SSH to the bastion

```
ssh <range-name>
```

Setup (SSH config, cert): [docs/04-ssh-bastion.md](04-ssh-bastion.md).

## SSH to the appliance nodes (via the bastion)

```
ssh <range-name>-n1
ssh <range-name>-n2
ssh <range-name>-n3
```

## Artifact Studio

<https://artifact-studio.spectrocloud.com/>
