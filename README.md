# Northwind Careers — Thrivea ATS embed demo

A standalone, fictional client site ("Northwind Studio") that embeds the **Thrivea careers widget** to
list open roles and funnel applications into the Thrivea ATS — exactly how a real Thrivea customer would
drop it onto their own `/careers` page.

Served as a static page via **GitHub Pages**. Pointed at the Thrivea **dev** careers surface.

```html
<div id="thrivea-careers" data-tenant="stevos"></div>
<script src="https://embed.thrivea.com/careers.js" async></script>
```

## What's here

- `index.html` — the Northwind page + the embed `<div>` + `<script>`.
- `careers.js` — the widget bundle, built from `thrivea-web` (`packages/careers-widget`, `pnpm build`).

## Config (on the `<div data-*>`)

| Attribute            | Value (this demo)                                          |
| -------------------- | ---------------------------------------------------------- |
| `data-tenant`        | `stevos` (the dev tenant's careers slug)                   |
| `data-api`           | `https://app-thrivea-dev.azurewebsites.net/careers/v1`     |
| `data-recaptcha-key` | public reCAPTCHA v3 site key (must allow `thrivea.github.io`) |
| `data-locale`        | `en`                                                       |

> The reCAPTCHA **site** key is public by design (it ships in client HTML). The apply form only submits
> when the key's allowed-domains include `thrivea.github.io`.

## Refreshing the widget bundle

Rebuild in `thrivea-web` and copy the artifact over:

```bash
pnpm --filter @thrivea/careers-widget build
cp packages/careers-widget/dist/careers.js <this-repo>/careers.js
```
