# 1. Prerequisites

## VPN

The factory and every URL in these docs are on the lab network. Turn the VPN on before anything else, then check:

```
ping -c 1 factory.fedlab.xyz
```

A reply from `10.10.186.117` means you are on:

```
PING factory.fedlab.xyz (10.10.186.117): 56 data bytes
64 bytes from 10.10.186.117: icmp_seq=0 ttl=64 time=92.874 ms

--- factory.fedlab.xyz ping statistics ---
1 packets transmitted, 1 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 92.874/92.874/92.874/0.000 ms
```

A hang, `unknown host`, or `Request timeout` means the VPN is not up. Reconnect and retry.

## DNS for `*.fedlab.xyz`

Once the VPN is up, names ending in `.fedlab.xyz` (the factory, the sign-in page, every per-range console URL) need to be resolved by the lab's DNS server at `10.10.186.100`, not by your default resolver. Without this, `factory.fedlab.xyz` and every range URL will fail even with a working VPN.

### macOS

Create a resolver file. macOS reads any file under `/etc/resolver/` named after a domain and sends queries for that domain to the listed nameserver. `/etc/resolver/` doesn't exist by default — create it first, then drop the file, then flush the cache so the new resolver takes effect immediately:

```
sudo mkdir -p /etc/resolver
echo 'nameserver 10.10.186.100' | sudo tee /etc/resolver/fedlab.xyz
sudo dscacheutil -flushcache && sudo killall -HUP mDNSResponder
```

Verify the resolver is registered:

```
scutil --dns | grep -A 3 fedlab
```

Successful:

```
  domain   : fedlab.xyz
  nameserver[0] : 10.10.186.100
  flags    : Request A records
  reach    : 0x00000002 (Reachable)
```

And that it actually resolves lab names:

```
dscacheutil -q host -a name sso.lab.fedlab.xyz
```

Successful:

```
name: sso.lab.fedlab.xyz
ip_address: 10.10.186.100
```

### Linux

systemd-resolved drop-in:

```
sudo mkdir -p /etc/systemd/resolved.conf.d
printf '[Resolve]\nDNS=10.10.186.100\nDomains=~fedlab.xyz\n' | sudo tee /etc/systemd/resolved.conf.d/fedlab.conf
sudo systemctl restart systemd-resolved
```

### Windows

Elevated PowerShell:

```
Add-DnsClientNrptRule -Namespace ".fedlab.xyz" -NameServers "10.10.186.100"
```

## Browser

Any modern browser. You'll use it to sign into factoryd and open the BMC console. Overlay product UIs (System Console, Tenant Console, Local UI) are reached through an SSH tunnel from your laptop; details in step 6.

## Terminal

macOS Terminal, iTerm, Windows Terminal, whatever you prefer.

## SSH keypair

You need an ed25519 (or RSA) keypair on your laptop. Most people already have one. Check:

```
ls ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.pub 2>/dev/null && echo present
```

On success:

```
/Users/you/.ssh/id_ed25519
/Users/you/.ssh/id_ed25519.pub
present
```

If it prints nothing, make one — accept every default:

```
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519
```

Successful run:

```
Generating public/private ed25519 key pair.
Enter passphrase for "/Users/you/.ssh/id_ed25519" (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/you/.ssh/id_ed25519
Your public key has been saved in /Users/you/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:B6r4z3+7qkXbY2hn8vJ7… you@yourlaptop
The key's randomart image is:
+--[ED25519 256]--+
|            .    |
|           . o   |
|          . = .  |
|         . = E   |
|        S. + . . |
|        .+ . . .o|
|       .o.o +.+++|
|       oo. oo =.o|
|      .. ..o=.o +|
+----[SHA256]-----+
```

Your public key (`~/.ssh/id_ed25519.pub`) gets pasted into factoryd in step 2. The private key never leaves the laptop.
