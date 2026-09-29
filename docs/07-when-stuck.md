# 7. When something breaks

Two rules first:

- **Log it before anyone fixes it.** One row per problem in the Issue tab of the party sheet.
- **Stuck for 15 minutes**: log what stopped you, then ask a host.

## Common symptoms and what they mean

| what you see | cause | do |
| --- | --- | --- |
| `Can't assign requested address` on every ssh | VPN up without an address | reconnect, confirm `inet` line on `utun6` |
| Bastion refuses your key after a week (or overnight) | certificate expired (168h) or minted for another range | **Get certificate** on the range page, replace `~/.ssh/<range>-cert.pub` |
| `ssh -J <range> pubsec@10.240.N.x` refused | ProxyJump presents your cert to the node | run the second hop from the bastion |
| Node refuses `~/.ssh/pubsec-lab` | nodes never accept the lab key | use `ssh -t <range> ssh 10.240.N.x` |
| Range page 404s | you are not a collaborator on that range | ask the host — you may have been given the wrong URL |
| Login page rejects your password | one typo | try once more, then **stop** and ask a host — two more failed tries locks you out |
| Overlay UI never loads in the browser | tunnel closed, wrong port, or the appliance is not up | check the ssh session, check `rangectl cluster status` on the bastion |
| BMC page loads but no node preview | node powered off, or you haven't clicked a node in the list | power on, then wait |
| BMC console modal stuck on `AUTHED`, black screen | the first frame can take up to a minute after preview hands off | wait, or close and reopen the console |
| BMC console stopped taking keys | focus lost, or the modal flipped to Serial | click inside the graphical console; if it's on Serial, click Graphical |
| `ssh <range>-nN` refuses your password | node admin account locked after 3 wrong tries in 15 min (DISA STIG) | check the password via the node's Local UI (Local UI is unaffected by the SSH lockout); if it stays refused, Reset the node from the BMC page |
| `ssh <range>-nN` gives `Connection refused` from a node that used to answer | bastion banned by the node after 5 password failures | wait 10 minutes |
| A login that worked yesterday fails today | the range was rebuilt overnight | tell your host — the accounts, IPs and certs are all new |
| **Sign in with SSO** button gives "load failed" (or the browser can't reach `sso.lab.fedlab.xyz`) | the lab-DNS resolver isn't set up on your laptop, so `sso.lab.fedlab.xyz` doesn't resolve even though `factory.fedlab.xyz` does | run the macOS/Linux/Windows commands in [docs/01-prereqs.md](01-prereqs.md#dns-for-fedlabxyz), then flush DNS and refresh the browser |

