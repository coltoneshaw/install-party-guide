# 2. First sign-in

## Sign in with SSO

Open <https://factory.fedlab.xyz/>. Click **Sign in with SSO**.

![The lab sign-in page](../images/tt-02-keycloak.jpg)

- Username: the `party-guest-N` or `party-host-N` in your group's file
- Password: `Spectro123!!`

## First-login profile prompt

On your first sign-in Keycloak asks you to complete an **Update Account Information** page: email, first name, last name. Fill it in with your real details and submit. You only see this once.

## What's next

Factoryd hands you a home page with a "Ranges" table on it. That table only shows ranges you own — your collaborated range will look missing, that is expected. Open the **Range page** link from your group's file (in [`parties/`](../parties/)) to reach yours directly.

The SSH key upload lives on the range's Access tab, not the profile menu — [docs/04-ssh-bastion.md](04-ssh-bastion.md) walks you through it once you're on your range page.
