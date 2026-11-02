---
title: "I Replaced My Homelab VPN With Cloudflare One's Free Plan. Here's the Setup."
date: 2026-11-02
draft: true
author: "Michael Tayo"
tags:
  [
    "Cloudflare",
    "Cloudflare One",
    "Zero Trust",
    "ZTNA",
    "Cloudflare Tunnel",
    "Cloudflare Access",
    "Secure Web Gateway",
    "Homelab",
    "VPN",
  ]
categories: ["Security Engineering", "Cloud Security"]
description: "A hands-on walkthrough of setting up Cloudflare One on the free plan for a homelab: enrolling a Mac with the Cloudflare One Client, filtering DNS and HTTP with Gateway, publishing a self-hosted app through Cloudflare Tunnel, and locking it behind Access. Plus what you actually get for $0 and where the free tier stops."
slug: "cloudflare-one-free-plan-homelab-zero-trust"
show_reading_time: true
# TODO: add featured_image + images once we have a social card
keywords: "Cloudflare One free plan, Cloudflare Zero Trust free, Cloudflare One Client setup, WARP client macOS, Cloudflare Tunnel homelab, Cloudflare Access self-hosted app, Cloudflare Gateway DNS filtering, replace VPN with zero trust, homelab zero trust, ZTNA for free"
faq:
  - q: "Is Cloudflare One really free?"
    a: "Yes. Cloudflare's Zero Trust Free plan covers up to 50 users and includes the Cloudflare One Client (formerly WARP), Gateway DNS and HTTP filtering, Cloudflare Tunnel, and Access. The main trade-offs are short log retention (24 hours for Access, DNS, network, and HTTP logs) and community-only support."
  - q: "Do I need to open ports on my router to use Cloudflare Tunnel?"
    a: "No. The cloudflared connector makes outbound-only connections to Cloudflare, so you can publish a service without port forwarding or exposing your home IP address."
  - q: "What's the difference between Gateway and Access?"
    a: "Gateway filters traffic leaving your devices (DNS, network, and HTTP), like a secure web gateway. Access decides who can reach your applications, checking identity on every request before traffic reaches the app. Gateway protects you when you browse; Access protects what you host."
  - q: "Why aren't my Gateway HTTP policies doing anything?"
    a: "HTTP policies need the Cloudflare One Client running in Traffic and DNS mode, and, for most rules, TLS decryption turned on with the Cloudflare root certificate trusted on the device. Without the certificate, Gateway can only see the hostname, not the full request."
  - q: "Is the WARP client the same as the Cloudflare One Client?"
    a: "Yes. Cloudflare renamed the WARP client to the Cloudflare One Client. You'll still see 'WARP' in places like the warp-cli command and the macOS app bundle name."
---

<!--
DRAFT NOTES (delete before publishing)
Anything marked TODO needs your real details. I didn't want to make up what you configured.
Screenshots to grab: Zero Trust overview, Gateway policy list, Tunnel "Healthy" status, Access login page, the Mac client "Connected".
-->

For years my homelab remote access was <!-- TODO: what you used before: port forwards? WireGuard? Tailscale? nothing? -->. It worked, but it meant open ports on my router, a home IP address anyone could scan, and no real record of who reached what.

Yesterday I moved it to [Cloudflare One](https://developers.cloudflare.com/cloudflare-one/), Cloudflare's Zero Trust platform, on the free plan. One evening, $0, and the result is better than what I was running before. This post walks through what I set up, the order I'd do it in, the spots where I got stuck, and why I think it's worth it even for a single-person lab.

## What you get for $0

The Zero Trust Free plan covers up to 50 users. That's not a demo tier. It includes the same core building blocks the paid plans use:

| Component                                 | What it does                                                    | What I used it for                                                                   |
| ----------------------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| **Cloudflare One Client** (formerly WARP) | Runs on the device and sends its traffic through Cloudflare     | Enrolled my Mac so it's covered on any network                                       |
| **Gateway**                               | DNS, network, and HTTP filtering: a secure web gateway          | Blocking malware, phishing, and <!-- TODO: other categories -->                      |
| **Cloudflare Tunnel** (`cloudflared`)     | Outbound-only connector from your network to Cloudflare         | Publishing <!-- TODO: app name, e.g. Proxmox / Home Assistant --> with no open ports |
| **Access**                                | Checks identity on every request before traffic reaches the app | Putting the app behind <!-- TODO: Google / GitHub / one-time PIN --> login           |

The way I think about it: **Gateway protects me when I go out to the internet. Tunnel and Access protect what I host when the internet comes in.** A traditional VPN does a rough version of the second job and none of the first.

## The setup, in the order I'd do it

### 1. Create the Zero Trust organization

Sign in to the Cloudflare dashboard, open **Zero Trust**, and pick a **team name**. The team name becomes `<team-name>.cloudflareaccess.com` and it's what every device uses to enroll, so pick something short you won't want to change. Then choose the Free plan. Cloudflare asks for a payment method even on the free tier; you aren't charged.

<!-- TODO: anything surprising here? Did the payment-method step trip you up? -->

### 2. Add a login method

Before enrolling devices or protecting apps, Cloudflare needs to know how people prove who they are. Under **Integrations** > **Identity providers**, you can add Google, GitHub, Microsoft, and others, or use the built-in **one-time PIN**, which emails a code.

I went with <!-- TODO: which IdP and why -->. For a homelab, one-time PIN is the fastest start. A real IdP gets you MFA you already trust.

### 3. Enroll the Mac

First, decide who's allowed to enroll. Go to **Team & Resources** > **Devices**, open the **Management** tab, and under **Device enrollment permissions** add a policy. Mine is a single rule: allow my email address. <!-- TODO: confirm, or "emails ending in @yourdomain" -->

Then on the Mac:

1. Download and install the [Cloudflare One Client](https://developers.cloudflare.com/cloudflare-one/team-and-resources/devices/cloudflare-one-client/download/).
2. Launch it and choose **Zero Trust security**.
3. Enter the team name.
4. Complete the login in the browser, then go back to the client.

If you'd rather use the terminal:

```bash
warp-cli registration new <your-team-name>
# after the browser login completes:
warp-cli registration show
```

Once it shows **Connected**, the Mac's DNS and traffic go through your Gateway policies, whether you're at home, on hotel Wi-Fi, or tethered to a phone.

Gotchas I hit or would warn about:

- <!-- TODO: anything you hit. Ideas: macOS asking to approve the system extension / VPN profile; registration hanging at "Checking your organization configuration" (a reboot fixes it, and it's a known issue in the current macOS release); split tunnel confusion with local network devices like printers or the NAS. -->
- The new macOS client UI doesn't have split tunnel settings yet. If you need to exclude a local subnet, set it in the dashboard device profile or use `warp-cli tunnel ip`.

### 4. Turn on Gateway filtering

Under **Traffic policies**, I started with DNS policies because they work immediately and don't need anything extra on the device:

| Policy                                                     | Action | Why                                                  |
| ---------------------------------------------------------- | ------ | ---------------------------------------------------- |
| Security risks (malware, phishing, C2, newly seen domains) | Block  | The obvious baseline. This alone is worth the setup. |
| <!-- TODO: e.g. ads/trackers, specific categories -->      | Block  | <!-- TODO -->                                        |
| <!-- TODO: any allow rules -->                             | Allow  | <!-- TODO -->                                        |

<!-- TODO: did you test a block? A good screenshot is the Gateway block page, plus the matching entry in the DNS logs. -->

**HTTP policies need one more step.** To inspect full web requests (not just the domain name), Gateway has to decrypt TLS, which means:

1. Turning on the proxy and TLS decryption in Gateway settings.
2. Installing and trusting the Cloudflare root certificate on the Mac. The client can deploy it for you.

That's the step most people skip, and then they wonder why HTTP rules never fire. It's also a real decision: you're letting Cloudflare see decrypted traffic. For my own lab I'm fine with that. <!-- TODO: what you decided, and any do-not-inspect exceptions you added, e.g. banking, Apple services, apps that pin certificates. -->

### 5. Publish a homelab app with Cloudflare Tunnel

This is the part that replaced my port forwards.

1. Go to **Networking** > **Tunnels** and select **Create a tunnel**.
2. Name it (mine is `<!-- TODO -->`) and pick the OS of the machine that will run the connector.
3. Copy the install command and run it on that machine. On a Linux box it installs `cloudflared` as a service using the tunnel token.
4. Wait for the tunnel to show **Healthy**, then select **Continue**.
5. On the **Routes** tab, add a **Published application**: pick a subdomain on a domain you have in Cloudflare, and set the service URL to the local app, for example `http://localhost:8123` or `https://192.168.1.10:8006`.

I ran `cloudflared` on <!-- TODO: which machine: Raspberry Pi, Proxmox LXC, Docker container, NAS --> and published <!-- TODO: app -->.

Two things to know:

- **No inbound ports.** `cloudflared` only makes outbound connections to Cloudflare, so I closed <!-- TODO: which ports --> on my router. My home IP isn't in public DNS anymore.
- **The moment you add a published route, the app is on the internet.** Don't stop here. Step 6 isn't optional.

<!-- TODO: if you also added a private network (CIDR) route so the enrolled Mac can reach LAN IPs directly, add a short paragraph. That's the true "VPN replacement" piece. -->

### 6. Put Access in front of it

Go to **Access controls** > **Applications**, select **Create new application**, choose **Self-hosted and private**, and add the same public hostname you published through the tunnel.

Then add a policy. Access applications deny by default, so nothing gets in until an Allow policy matches. Mine:

| Action | Rule    | Selector | Value                     |
| ------ | ------- | -------- | ------------------------- |
| Allow  | Include | Emails   | <!-- TODO: your email --> |

<!-- TODO: did you add a Require rule? e.g. require the login method you set up, or require a Gateway-enrolled device. Requiring the enrolled device means only your Mac with the client running can reach the app, even with a stolen password. That's the strongest config on the free plan and worth mentioning if you did it. -->

Now visiting the hostname shows a Cloudflare login page first. The request never reaches my server until Access approves it.

## Why this is better than what I had

**No attack surface at home.** Port forwards and a public IP mean every scanner on the internet knocks on your router. With Tunnel there's nothing listening to knock on.

**Identity on every request, not just at connect time.** A VPN checks you once and then trusts the session to reach the whole network. Access checks identity for each app, and only grants that one app.

**Protection that follows the laptop.** Gateway filtering runs wherever the Mac goes. The coffee-shop network gets the same DNS blocking as my home network.

**A log of what happened.** Access logs show who reached what and when. Gateway logs show what got blocked. For a homelab that's a nice-to-have. For a small business it's the start of an audit trail.

**It's the same architecture enterprises buy.** If you're learning Zero Trust for work, there's no better lab than running the real product. Device enrollment, identity providers, deny-by-default app policies, and egress filtering are the same concepts on the paid tiers.

## Where the free plan stops

Be clear-eyed about the limits:

- **Log retention is 24 hours** for Access, DNS, network, and HTTP logs on the free plan (paid Standard is 30 days). If you want history, you'll need to check the dashboard daily or upgrade. Admin logs are kept for 18 months.
- **50 users.** Plenty for a household or homelab. A small team will feel it eventually.
- **Community support only.**
- **TLS inspection is a trust decision.** It's on you to decide what to decrypt and what to exempt.
- **You need a domain on Cloudflare** to publish apps with a public hostname.

<!-- TODO: anything else you ran into that felt like a free-plan wall? -->

## What I'd do next

- Enroll <!-- TODO: phone / other machines --> so every device gets the same Gateway policies.
- Add a device posture check, so Access requires the enrolled, client-connected Mac.
- Move more self-hosted services behind the same tunnel instead of creating one tunnel per app.
- <!-- TODO: your own next steps -->

If you have a homelab and you're still forwarding ports, this is the evening project I'd recommend. It costs nothing, closes your inbound attack surface, and teaches you Zero Trust with the real product instead of slides.

<!-- TODO: closing CTA. Newsletter signup? Link to a related post? -->
