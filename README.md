# Public site for the OAuth consent screen

Three static pages, served at **https://412446845.xyz/**.

They exist for one reason: Google will not let an OAuth consent screen leave
**Testing** mode without a public home page and a privacy policy, and Testing
is what expires the refresh token every seven days.

## Why a domain had to be bought

Google requires the **top private domain** to be verified in Search Console
and rejects shared suffixes — `github.io`, `wixsite.com`, `vercel.app` —
because ownership of a domain used by thousands of unrelated sites cannot be
established. Pages may live on a subdomain or a path, but only once the apex
is verified, and the apex has to be one you control.

`412446845.xyz` is nine digits, which puts it in XYZ's 1.111B Class at
$0.99/year *including renewals* — not a first-year rate.

## Setup

**1. Push this directory to a public GitHub repo** and enable Pages on it
(Settings → Pages → deploy from branch, root). The `CNAME` file here tells
Pages which domain to answer on.

**2. DNS at the registrar.** Four `A` records on the apex:

```
A   @   185.199.108.153
A   @   185.199.109.153
A   @   185.199.110.153
A   @   185.199.111.153
```

Optionally `CNAME www → <username>.github.io`. Verified live against GitHub's
published Pages addresses; if Pages ever reports a DNS error, re-check them
rather than trusting this file.

**3. Search Console.** Add `412446845.xyz` as a **Domain property** and create
the `TXT` record it gives you. A Domain property covers every subdomain, so
this is the only verification needed.

**4. Google Auth Platform → Branding.**

| Field | Value |
|---|---|
| Application home page | `https://412446845.xyz/` |
| Privacy policy | `https://412446845.xyz/privacy.html` |
| Terms of service | `https://412446845.xyz/terms.html` |
| Authorized domain | `412446845.xyz` |

**5. Audience → Publish app.** Stay unverified: Google documents a
personal-use exemption below 100 users, so the only consequence is the
"Google hasn't verified this app" interstitial, once.

**6. Re-authorise once** so a non-expiring refresh token is issued:

```
.\scripts\dev.ps1 reauth
```

The old token keeps its seven-day clock; publishing does not retroactively
extend it. The countdown in the Status tab should read "known: false" briefly
and then restart from a fresh grant.

## Keeping it honest

`privacy.html` lists the scopes the agent actually requests. If the scope list
in `auth/google.py` changes, that table must change with it — `tests/unit/
test_site.py` fails otherwise. A privacy policy that has drifted from the code
is a false claim rather than merely an incomplete one, and nothing else in the
project reads it, so nobody would notice.

## One caveat

A numeric `.xyz` is fine for an unverified personal-use app, because no human
at Google reviews it. If this were ever submitted for full verification, a
cheap numeric domain with no history would attract more scrutiny than a
boring one. Not a reason to avoid it here.
