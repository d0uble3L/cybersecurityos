# Security headers for `www.cybersecurityos.net`

GitHub Pages can't set custom response headers, so the site currently sends no
HSTS, CSP, X-Frame-Options or Referrer-Policy. `layouts/_default/baseof.html`
sets `Referrer-Policy` with a `<meta>` tag, but the others only work as real
headers.

## 1. Turn on "Enforce HTTPS" in GitHub Pages

Repository → Settings → Pages → check **Enforce HTTPS**. This redirects
`http://` to `https://`.

## 2. Put Cloudflare (free plan) in front of GitHub Pages

1. Add `cybersecurityos.net` to Cloudflare and switch the domain's nameservers.
2. Keep the existing DNS records (`www` CNAME → `d0uble3l.github.io`, apex A
   records → GitHub Pages IPs) and set them to **Proxied**. Leave `join` (Kit)
   and `store` (Gumroad) as **DNS only**: those services issue their own
   certificates and break when proxied. Turn off DNSSEC at the registrar before
   switching nameservers.
3. SSL/TLS → Overview → **Full (strict)**.
4. SSL/TLS → Edge Certificates → enable **Always Use HTTPS** and **HSTS**
   (max-age 6 months to start, include subdomains only once every subdomain is
   on HTTPS, preload off until you're sure).
5. Rules → **Transform Rules → Modify Response Header** → add a rule for
   hostname `www.cybersecurityos.net` that sets:

| Header                                | Value                                                  |
| ------------------------------------- | ------------------------------------------------------ |
| `X-Frame-Options`                     | `SAMEORIGIN`                                           |
| `X-Content-Type-Options`              | `nosniff`                                              |
| `Referrer-Policy`                     | `strict-origin-when-cross-origin`                      |
| `Permissions-Policy`                  | `camera=(), microphone=(), geolocation=(), payment=()` |
| `Content-Security-Policy-Report-Only` | see below                                              |

## 3. Content-Security-Policy

Start in **report-only** mode: the site loads Kit forms, Google Fonts, Disqus
and Formspree, and has inline scripts, so a strict policy will break things if
it's enforced straight away. A starting point:

```text
default-src 'self';
script-src 'self' 'unsafe-inline' 'wasm-unsafe-eval' https://theghettoflower.kit.com https://f.convertkit.com https://*.disqus.com https://*.disquscdn.com;
style-src 'self' 'unsafe-inline' https://fonts.googleapis.com https://*.disquscdn.com;
font-src 'self' https://fonts.gstatic.com;
img-src 'self' data: https:;
connect-src 'self' https://app.kit.com https://*.kit.com https://*.convertkit.com https://*.disqus.com;
frame-src https://disqus.com https://*.disqus.com;
form-action 'self' https://app.kit.com https://formspree.io;
frame-ancestors 'self';
base-uri 'self';
object-src 'none';
upgrade-insecure-requests
```

`'wasm-unsafe-eval'` is for the Pagefind site search, which runs WebAssembly.

Watch the browser console for a week, add any blocked origins you actually
use, then rename the header to `Content-Security-Policy` to enforce it.

## 4. Check it

```bash
curl -sI https://www.cybersecurityos.net/ | grep -iE 'strict-transport|content-security|x-frame|referrer|x-content'
```

Then run the site through <https://securityheaders.com>.
