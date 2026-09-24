# hcp-staging-proxy

A Shopify app whose only job is to own an **App Proxy** registration for staging and forward requests to the `hcp-middleware` staging Worker. It has no backend logic of its own — all the actual customer/sample-request handling lives in [`Dose-Daily-HCP-Middleware`](https://github.com/Dose-Daily-Co/Dose-Daily-HCP-Middleware).

## Why this app exists

On production, `doseforclinicians.com` has a separate (unowned, "black box") Shopify app installed whose App Proxy config forwards `/apps/hcp/*` requests straight to the `hcp-middleware` production Worker. This app recreates that same pattern for staging: it forwards to `hcp-middleware-staging` instead.

This works because the app that owns an App Proxy registration doesn't need to run the code behind it — Shopify just forwards the signed request to whatever URL is configured. `hcp-middleware-staging`'s Worker is initialized with this app's client credentials, so it validates those signed requests as if it were this app.

## Configuration

See `shopify.app.toml`:

- `application_url` / `[auth].redirect_urls` point at `hcp-middleware-staging.dose-daily.workers.dev` — OAuth install runs through that Worker's `/auth` routes, not any code in this repo.
- `[app_proxy]` forwards `apps/hcp/*` to `hcp-middleware-staging.dose-daily.workers.dev/hcp`.

## Local development

```shell
npm install
npm run dev
```

This is mainly useful for installing the app on a store (which drives the OAuth handshake) or re-linking/deploying config changes — not for running app logic, since there isn't any here.

```shell
npm run deploy   # shopify app deploy — pushes shopify.app.toml changes to Partner Dashboard
```
