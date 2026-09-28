# 5. SSH to an appliance node

Once the installer finishes, each node is reachable through the alias you set up in step 4. The alias uses ProxyJump, so a single command hops laptop → bastion → node.

## From your laptop

```
ssh vertex-tue-am-n1
```

The node asks for the `admin` password — the one you set during the TUI step of the install. The first time you reach each node, ssh asks you to confirm its host key; type `yes`.

```
The authenticity of host 'vertex-tue-am-n1 (…)' can't be established.
ED25519 key fingerprint is SHA256:…
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
admin@vertex-tue-am-n1's password:

Welcome to Palette VerteX 4.10.17
Last login: Mon Sep 28 04:16:00 2026 from 10.240.6.1
admin@palette-vertex-1:~$
```

The admin user has sudo, so root-owned logs are readable.

## Copy things off

```
scp vertex-tue-am-n1:/etc/os-release .
ssh vertex-tue-am-n1 'sudo journalctl -b --no-pager' > n1-journal.txt
```

## What does NOT work

- `ssh -J vertex-tue-am pubsec@10.240.N.10`. ProxyJump with the wrong user (`pubsec` instead of `admin`) and no host key entry will look like it should work and hang; use the alias.
- `~/.ssh/pubsec-lab` on the node. Nodes never accept the lab key.

Node IPs are listed on your party file's Network config table.
