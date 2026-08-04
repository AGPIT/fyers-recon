
# 8 items on 2026-08-04 23:46:38 UTC
- **Full v3 OpenAPI spec recovered** from `myapi.fyers.in/static/media/v3.fc0a0244d7d288c81e4f.yaml` (1.57 MB, 51 documented API paths) plus two supplemental specs: `v3.1.32a8eeba1fba866d1201.yaml` (SEBI retail-algo regulatory changes, effective Apr 2026) and `fia.9dcf545bc3f508df4db9.yaml` (new **FYERS MCP / Model Context Protocol** product).
- **Auth scheme confirmed live:** OAuth2 `authorization_code`; `appIdHash = SHA-256(app_id + ":" + app_secret)`; POST `/api/v3/validate-authcode {grant_type, appIdHash, code}`; access_token used as `Authorization: Bearer`. Live error fingerprinting: `-441` auth code required, `-442` invalid grant_type, `-16` invalid/expired token, `-501` invalid refresh token. `grant_type=refresh_token` accepted at `/api/v2/validate-refresh-token`; `authorization_code` rejected there (asymmetric v2/v3 handling).
- **New subdomains discovered** (from docs spec + probes): `trade.fyers.in` (200, OAuth redirect-uri host `/api-login/redirect-uri/index.html`), `socket.fyers.in` (`wss://socket.fyers.in/trade/v3`), `rtsocket-api.fyers.in` (`wss://rtsocket-api.fyers.in/versova`), `alerts.fyers.in`, `api-connect-docs.fyers.in` (branded order-placement docs + `fyers-lib.js`), `community.fyers.in`, `direct.fyers.in`, `learn.fyers.in`, `partners.fyers.in`.
- **New product surface:** FYERS MCP ships as a Mac `.pkg` / Windows `.exe` at `assets.fyers.in/mcp/macos/1.0.0/` that installs Node.js + Claude Desktop and performs its own FYERS OAuth login — client-side token storage / supply-chain surface (assets host is in scope).
- **marketdata-api-instaoptions.fyers.in** = Express service self-identifying as `{"service":"loom","version":"1.0.0"}` via `/health`; root 404s all paths probed.
- **api.fyers.in** (AWS ALB) exposed directly (no WAF), unlike api-t1/t2 (Cloudflare). Method enforcement confirmed: `OPTIONS /api/v3/order` -> 200; all others -> 500 with `{"message":"Invalid Request, please provide valid method"}`.
- API app management dashboard lives at `fyers.in/web/api-dashboard/user-apps` (Flutter web trading app on `fyers.in/web`). Docs portal SPA uses Redoc/Redocly and references `X-XSRF-TOKEN` CSRF handling.
- `workdrive.fyers.in`/`projects.fyers.in` redirect to Zoho SaaS (out-of-our-hands tenant; noted, deprioritized). `instantpayout.fyers.in` unreachable this run.

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: deepseek)
Review research_deepseek.md for details
## CVSS Candidates
- [H1 OAuth redirect_uri / auth-code interception] - Estimated CVSS 8.1 (if code->token leakage confirmed)
- [H1 open redirect on generate-authcode] - Estimated CVSS 6.1 (browser-only oracle)
