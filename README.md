# Install Party trainee guide

You've been given a range for the install party. This is how you reach it — sign in to the factory, get to your BMC console, and SSH to the bastion. **Nothing in this repo teaches you how to install the product itself; that comes from the vendor's public product docs.**

The party organizer gave you two things:

- a **username** (like `party-guest-3`) and password `Spectro123!!`
- a **BMC page URL** like `https://factory.fedlab.xyz/ranges/vertex-tue-am/bmc`

Generate your own SSH keypair if you don't already have one (see [docs/01-prereqs.md](docs/01-prereqs.md)). You need it before you start.

## This event

Your assigned file in [`parties/`](parties/) is the launch board: sign-in URL, username, password, range page, BMC console. Nothing else. Open it, follow the links.

If you get stuck on the mechanics (how to mint an ssh cert, how to open the overlay UIs, why the bastion refuses your key), [`docs/`](docs/) is the general walkthrough.

**One heads-up on the factoryd home page:** the "Ranges" table only shows ranges you own. Your collaborated range will look missing — that is expected. Use the **Range page** link in your group's file to reach yours directly.

## Contents

1. [Prerequisites: VPN, browser, terminal](docs/01-prereqs.md)
2. [First sign-in: SSO](docs/02-sign-in.md)
3. [The BMC page: what you're looking at](docs/03-bmc-page.md)
4. [SSH to the bastion (store your key, mint your cert)](docs/04-ssh-bastion.md)
5. [Hop to an appliance node](docs/05-node-hop.md)
6. [Opening overlay UIs from your laptop](docs/06-overlay-uis.md)
7. [When something breaks](docs/07-when-stuck.md)
8. [Load the content bundle from the bastion](docs/08-content-bundle.md)

## Rules for the party

- **Work from public product docs only** when installing. If the docs don't say it, log the gap in the Issue tab of the sheet.
- **Narrate as you go**: what you're looking for, what you expected, when you're unsure. Your observer is timing you, you don't time yourself.
- **Stuck for 15 minutes**: log what stopped you, then ask a host.
- Two wrong password tries in the login page: **stop and ask a host**. Keycloak has brute-force lockout on this realm.
