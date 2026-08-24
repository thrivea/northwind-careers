# Northwind Careers — Thrivea ATS embed demo

A standalone, fictional client site ("Northwind Studio") that embeds the **Thrivea careers widget** to
list open roles and funnel applications into the Thrivea ATS — exactly how a real Thrivea customer would
drop it onto their own `/careers` page.

Served as a static page via **GitHub Pages**. Pointed at the Thrivea **dev** careers surface.

```html
<div id="thrivea-careers" data-tenant="stevos"></div>
<script src="https://app-dev.thrivea.com/careers.js" async></script>
```

Those two lines are the whole integration, and they are what Thrivea's **Settings → Careers** page hands
a customer verbatim — no API URL, no build step, nothing to keep in sync.

## What's here

- `index.html` — the Northwind page + the embed `<div>` + `<script>`.

There is deliberately **no `careers.js` in this repo**. It used to hold a copy of the bundle, which meant
the demo ran on whatever build was last copied here — it sat on a months-old one, so widget fixes never
reached it and published jobs took longer to appear than they should have. The page now loads the bundle
the Thrivea environment serves at `{origin}/careers.js`, so it is always current.

## Config (on the `<div data-*>`)

| Attribute     | Value (this demo)                        |
| ------------- | ---------------------------------------- |
| `data-tenant` | `stevos` (the dev tenant's careers slug) |

That is the whole configuration — the same one line Settings → Careers generates, nothing added.

This page used to also pin `data-recaptcha-key` and `data-locale`. Both were removed: each was already
the dev environment's own default, so the only thing they added was a second copy to keep in sync. The
reCAPTCHA **site** key is public (it ships in client HTML), but it is baked into the bundle per
environment precisely so it can be rotated — pinned in a customer's HTML it would outlive the next
rotation and silently take the apply form down. `data-locale` is gone so the widget does what an
unconfigured embed really does: follow the visitor's browser language.

No `data-api`: the bundle an environment serves already knows its own careers API base (baked in at build
time), so the snippet stays free of infrastructure hostnames. It remains supported as an override if a
page ever needs to point at a different environment.

Other optional attributes — `data-accent`, `data-theme`, `data-layout` — are documented in-product under
Settings → Careers. `data-accent` is intentionally omitted here so the widget shows the Thrivea brand
purple rather than blending into Northwind's palette.

## Content-Security-Policy

The page runs under a deliberately strict CSP (`default-src 'none'`), delivered as a `<meta>` tag because
GitHub Pages cannot send headers. A real site would send it as a header; the effect is the same. The point
is to prove the embed under the conditions a security-reviewed customer will actually impose.

```
script-src  'self' https://app-dev.thrivea.com https://www.google.com https://www.gstatic.com
connect-src 'self' https://app-thrivea-dev.azurewebsites.net https://www.google.com
style-src   'self' 'unsafe-inline'
img-src     'self' data: https://www.gstatic.com
frame-src   https://www.google.com
```

Settings → Careers currently names only the first two sources (`app-dev.thrivea.com` for the script,
`app-thrivea-dev.azurewebsites.net` for the roles). Verified in Chromium: those two **are** enough to list
roles, open a role and open the apply form — but not to submit one. The form mints an invisible reCAPTCHA
v3 token, so Google's script is blocked, the widget submits tokenless, and the backend rejects it
(`RecaptchaVerifier` returns false on an empty token whenever the environment has a full key pair). The
candidate fills the form and is turned away with no way to tell why.

`style-src 'unsafe-inline'` is needed because the widget injects its stylesheet into its Shadow DOM as a
`<style>` element; without it the widget renders, unstyled.

## Live updates

The widget polls the careers API every 30s with `If-None-Match` (cheap 304s), and refreshes immediately
whenever the tab returns to the foreground. Publishing or unpublishing a job in the ATS invalidates the
server-side cache at once, so a change shows up here within about half a minute — instantly if you switch
away and back.

## Local preview

```bash
python3 -m http.server 4173   # then open http://localhost:4173/
```
