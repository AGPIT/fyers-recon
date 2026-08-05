# New host surface discovered 2026-08-05 (manual deep-dig round)

Read-only verified. Program-consistent scope: target + subdomains.

## Superhuman / Grammarly
- `dox.grammarly.com` — LIVE undiscovered API (doc-processing service). Stack: nginx-clojure + Jetty (`x-powered-by: Jetty`), AWS-style `x-request-id`, `default-src 'none'` CSP. Gate: `{"status":"error","info":"missing required headers"}` on every path except `/health` → 200 `{"status":"200 ok"}`. With `Referer`/`Origin` the error changes to `"unknown client type"` → there is a required client-type header (not in list tried: X-Client-Type/X-Grammarly-*/User-Agent/Api-Key/Bearer). Gate closed read-only; hostile-but-real service discovery.
- Gateway alias cluster all route to same TGW (`{"error_msg":"404 Route Not Found"}`, `/health`=200, `/user/settings`=401): `api.grammarly.com`, `gate.grammarly.com`, `gates.grammarly.com`, `goldengate.grammarly.com`, `irbis.grammarly.com`, `discount.grammarly.com`, `auth.grammarly.com`, `gateway.grammarly.com`.
- `apps-public.grammarly.com` → 401 (real auth service, 0b).
- `applet-bundles*`, `apps-uploads` → S3 (NoSuchKey). `chargeback-prevention/datareport/fridge` → 403 nginx.
- `developer.grammarly.com` → 200 public API docs (Review API etc).
- Coda: `acme.coda.io`, `head.coda.io` → live Coda app SPAs (canary/test domains); `help.coda.io`→support, `community.coda.io`→forum, `build-links.coda.io`→404, `api.coda.io`/`developers.coda.io` = no A record (deprecated post-acquisition). `codapacks.com`, `help.coda.io` (Zendesk) resolve.
- `g-mail.internal.grammarly.com` (cert, internal), `access-manager.private.grammarly.com`, `auth-private`, `facade-private` (certs only; edge 403/000).
- Superhuman: `docs.superhuman.com` = Coda ("kr") CloudFront docs, CSP `script-src 'none'`, SPA, auth 302 → mediator CAPI. All gateway base routes (`/nomos/v1/*`, `/scim/credentials`, `/ssoConfigurations`, `/domains`, `/user/*`, `/auth/v3/*`, `/passport/*`) 401; only `/health` and `/user/location/data-regulation`(=200 `{"dataRegulation":false}`) are unauthenticated.

## FYERS (40 hosts from `indus/v1/config` + certspotter)
- `api-y1.fyers.in` → **default RHEL Apache "Test Page"** (7.1KB), `/index.html` 200 everything else 404. Publicly exposed test host.
- `dev.fyers.in` → **default nginx "Welcome" page** (1.5KB). Publicly exposed dev host.
- `data.fyers.in` → fund-transfer/quotes backend. `fy/v1/fundtx/v1/{view,bankdetails,marginutilized,addfunds,withdraw}` all session-gated (`withCredentials`, POST; 500 "Invalid Request, please provide valid method" pre-auth). Login redirect: `login.fyers.in/?cb=https://fundtransfer.fyers.in`.
- `fundtransfer.fyers.in` SPA (Add/Withdraw funds, UPI/netbank, jQuery). `/addfunds`, `/bankdetails`, `/withdraw`.
- `mtfddpi.fyers.in` — Flutter app with **DigiLock CDSL + Digio eSign SDK** (`app.digio.in/sdk/v11/digio.js`, digilocker) — the EDIS/eSign flow mimo is chasing.
- `debt.fyers.in` — Debt Market SPA (Flutter). `marketsmith.fyers.in` — product subscriptions page. `insights.fyers.in` — "Trading Widgets" iframe host. `savedcharts.fyers.in` — React SPA (small, image gallery, `_FYERS` cookie-token). `status.fyers.in` — status page (components enum, ids only). `instaoptions.fyers.in` — discontinued notice. `open-account.fyers.in` — account opening SPA (58KB). `partners.fyers.in` — Partners Dashboard v3.0 SPA.
- Zoho-hosted: `people/admin/works/projects.coda.meetings, cliq`(302), `workdrive`, `recruit`, `forms`, `learn` (404-notfound), `supportdesk` — third-party platforms under same apex.
- `api-d1.fyers.in` → CF 530/1016; `api-t1-cdn` → 404 (CDN); `betatrade/datapub/instantpayout` → no DNS.
- All + config: `api-t1/t2/a1/a1-prod/g1/i1/y1`, `api-d1/` etc.

## Closed gates (read-only, no leak)
- `dox` client-type header unknown; `nomos` 401-auth; fundtx session-gated; Discourse search no secrets; superhuman `.grammarlyaws` 000; `ignite` (mimo EDIS) unvalidated.