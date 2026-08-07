# Knowledge Base (seed)

## AUTHORIZATION (2026-08-07, verified by analyst)
- fyers.in has NO security.txt, NO VDP/disclosure policy, NO bug bounty program; only a support inbox. Live probing of production auth/OTP/KYC/trading APIs requires explicit authorization — analyst MUST NOT emit PROBE steps against auth flows until authorization is confirmed; emit [NEXT] HUMAN: confirm authorization instead.
- Passive recon (DNS/CT/headers/public JS corpus from url-fyers) is fine.

## REJECTED CLASSES
- Third-party hosts (auth.grammarly.com, auth.ppgr.io, auth.qagr.io, coda.io, Zoho/GA4/GTM keys, CleverTap) — OOS, never report.
- Client-side-only analytics keys (GA4/GTM/Zoho) — public by design.
- Guest JWTs with null permissions — public by design.
- OAuth client IDs + appIdHash — public identifiers, not secrets.
- Demo/datafeed token_id in public bundles — alive ONLY if a passive probe shows it authenticates datafeed requests (pending test).

## ALIVE SURFACE
- www.fyers.in: Next.js SPA (_next/static chunks)
- app.fyers.in: main app shell, flutter packages, clevertap assets
- alerts.fyers.in/web: flutter web app
- community.fyers.in: _ws/inbox, _ws/sio, auth/signup, sw.js, locales/en.js (guest JWT)
- ipo.fyers.in, sgb.fyers.in: OAuth env maps
- trade.fyers.in: datafeed bundles (demo Fernet token_id x15+)
- api-a1-prod.fyers.in/myaccount/prod/verified-pnl/get-data: client-side-only JWT gating — top AUTH_HELPED lead
- 2026-08-07 REJECTED XSS/SSTI @ api-connect-docs.fyers.in: static S3/Docusaurus; injected `onload` → CF 429/errorCode1015 rate-limit/WAF block, no `49` rendered — false positive reaffirmed (second host corroboration).
