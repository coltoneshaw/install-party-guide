# 6. Opening overlay UIs from your laptop

The System Console, Tenant Console, and each node's Local UI live on the overlay network (`10.240.<IDX>.*`). Everything from your laptop reaches them through the bastion. Two patterns; pick whichever fits.

## Pattern A: SOCKS proxy + a dedicated Chrome window

One tunnel serves every overlay URL. Best when you'll be clicking around several UIs (System Console + each node's Local UI + Headlamp + Keycloak on VMO).

```
ssh -N -D 1080 vertex-tue-am
```

`-N` means "no shell, just the tunnel"; leave it running. Then start a **separate** Chrome window that uses the proxy — this does not touch your normal browser:

macOS:

```
open -na 'Google Chrome' --args --proxy-server=socks5://127.0.0.1:1080 --user-data-dir=/tmp/lab-chrome
```

Linux/Windows: launch Chrome the same way with those two flags. Firefox works too: Settings → Network Settings → Manual proxy, SOCKS Host `127.0.0.1` port `1080`, SOCKS v5, and tick **Proxy DNS when using SOCKS v5**.

In that browser, open the overlay URL directly. Certificate warnings are expected (self-signed for the lab); accept and continue.

## Pattern B: per-port `-L` forward

Better when you already know exactly which URLs you need and don't want to open a second Chrome. Bumps each remote port to a `15xxx` local port. One command opens all four:

```
ssh -N \
  -L 15080:10.240.6.10:5080 \
  -L 15081:10.240.6.11:5080 \
  -L 15082:10.240.6.12:5080 \
  -L 15443:10.240.6.5:443 \
  vertex-tue-am
```

Substitute your range's node IPs and VIP (from your party file). Then browse to:

| what | URL |
| --- | --- |
| Node 1 Local UI | https://127.0.0.1:15080 |
| Node 2 Local UI | https://127.0.0.1:15081 |
| Node 3 Local UI | https://127.0.0.1:15082 |
| System Console | https://127.0.0.1:15443/system |

Certificate warnings are expected on every URL (self-signed for the lab); accept and continue.

**Cookie gotcha.** Browsers key cookies by hostname and ignore the port, so all four tabs on `127.0.0.1` share one cookie jar — signing into node 2's Local UI signs node 1's out. Fine when you're only using one tab at a time; if you need several open at once, use Pattern A (SOCKS) instead — it keeps each URL on its real hostname so cookies stay separated.

## Node consoles

For a serial or VNC view (before the appliance is up), the [BMC console](03-bmc-page.md) has a **Console** button per node. No SSH needed.
