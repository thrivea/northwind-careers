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

| Attribute            | Value (this demo)                                            |
| -------------------- | ------------------------------------------------------------ |
| `data-tenant`        | `stevos` (the dev tenant's careers slug)                     |
| `data-recaptcha-key` | public reCAPTCHA v3 site key (must allow `thrivea.github.io`) |
| `data-locale`        | `en`                                                         |

No `data-api`: the bundle an environment serves already knows its own careers API base (baked in at build
time), so the snippet stays free of infrastructure hostnames. It remains supported as an override if a
page ever needs to point at a different environment.

> The reCAPTCHA **site** key is public by design (it ships in client HTML). The apply form only submits
> when the key's allowed-domains include `thrivea.github.io`.

Other optional attributes — `data-accent`, `data-theme`, `data-layout` — are documented in-product under
Settings → Careers. `data-accent` is intentionally omitted here so the widget shows the Thrivea brand
purple rather than blending into Northwind's palette.

## Live updates

The widget polls the careers API every 30s with `If-None-Match` (cheap 304s), and refreshes immediately
whenever the tab returns to the foreground. Publishing or unpublishing a job in the ATS invalidates the
server-side cache at once, so a change shows up here within about half a minute — instantly if you switch
away and back.

## Local preview

```bash
python3 -m http.server 4173   # then open http://localhost:4173/
```
