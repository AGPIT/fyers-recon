
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

# 2 items on 2026-08-05 01:23:50 UTC
- `api-t2.fyers.in/vagator/v2/*` — full endpoint set live (`get_user_id_v3`, `send_login_otp_v3`, `verify_otp_v2`, `verify_pin_v2`, `create_pin_v2`, `forgot_pin_v2`, `change_pin_v2`, `refresh_token_v2`, `verify_token_v2`, `totp`, `generate_qr`, `validate_qr`, `get_session_devices`, `guest_user/login_v2`, `guest_user/register`). Error shapes: `-1025 invalid request`, `-2 Missing request key`, `-1018 something went wrong`, `-1044 invalid input`, `verify_token_v2` returns DRF-style `{"detail":"Not authenticated"}`.
- `api.fyers.in/api/v2/*` (AWS ALB, no WAF) — same login verbs, form-POST validated, JSON-POST→500 invalid method.

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: deepseek)
Review research_deepseek.md for details

# 7 items on 2026-08-05 04:42:22 UTC
- **H1 redirect_uri oracle fully mapped (read-only).** For real clients `SOFG221ZX4-101` and `EFR7964223-101`, `GET /api/v3/generate-authcode` returned an identical 60,818-byte login page for three classes of `redirect_uri`: registered (`pledge.fyers.in/index.html`), external hostile (`evil.example.com/cb`), and foreign fyers.in subdomain (`direct.fyers.in/auth/redirect`). No server-side allowlist rejection at step-1; the unregistered URI is accepted. `V71C1UQU24-101` and subsequent calls hit CF **429/1015** rate limit — confirms a genuine unauthenticated public surface (no WAF app-layer filter, Cloudflare edge only). Invalid-format client_id → `302 trade.fyers.in/api-login/error/index.html?error_msg=invalid appType`, then 429.
- **No server-side URI reflection or echo in the login HTML.** Response body contains no reflection of the supplied `redirect_uri`, `client_id`, or `state`; the values are held client-side in JS. The login POST body itself includes `client_id`, `redirect_uri`, and `appType` (decrypted from string table). This means the *decision of where to deliver the auth_code is made client-side* off the URL param — consistent with step-1 not binding URI → code.
- **SSO bundle decrypted.** `login.fyers.in/new-sso/17.0/api_v3_login/login.min.js` (310 KB) uses a 1,257-element string-array obfuscation. Decoded backend routes: `api-t2.fyers.in/vagator/v2/*`, `api-t1.fyers.in/api/v3/{token, direct-login}`, `api.fyers.in/api/v2/*`, `tradingview/auth/*`. Confirmed string tokens: `refresh_token_v2`, `validate_refresh_token`, `REFRESH_TOKEN_VALIDITY`, `totp`, `get_user_id_v3`, `identifier_value`. Auth-flow fields: `#fy_client_id`, `client_id_flow`, `registered_client_id_and_pan`, `login_guest_user`, `register_guest_user`.
- **OTP-gate fingerprint proven.** `send_login_otp_v3` with the correct key `identifier_value` triggers CF **1015** ("error code: 1015" on the rendered page), whereas `mobile_number`/`client_id` keys return `-1025 invalid request`. This delineates a valid request-key vs invalid-key oracle AND confirms an active rate limiter on the OTP send path.
- **`api-g1.fyers.in/settings/*` new surface.** ~44 per-user settings endpoints (charts, key_ratio, key_metrics, screeners/favourites, one-click, scalper, order-window, qsgs, preferences, quick-trade). All return a **distinct auth fingerprint `{"code":-401,"message":"Unauthorized"}`** without a token — separate microservice, separate auth domain → candidate for cross-service authz gaps.
- **`api-i1.fyers.in/investment/*/fd/*` auth fingerprint.** Returns `{"error_code":40101,"Authorization token is required"}` — a third distinct auth error scheme (`-16`, `-401`, `40101`) across api-t1/t2, api-g1, api-i1.
- **Public `indus/v1/config` still live (H4 reproducible).** `https://api-d1-cdn.fyers.in/indus/v1/config` → 113,497 bytes, 73 endpoint blocks, 35 unique `*.fyers.in` hosts, `data_socket.cl_cug` (27 IDs: XS53027, XV15456, XT05527…), `analytics_exceptions.exceptional_clients` (14 IDs), `hsm_config.cug_mob` (9 IDs), plus feature/permission matrices (prime/basic `fundWithdrawal` limits), maintenance windows. The raw `api-d1.fyers.in` origin returns CF 1016 (origin-unreachable) — config is served only via the CDN wrapper.

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: deepseek)
Review research_deepseek.md for details
## CVSS Candidates
- [H1 OAuth code interception] — CVSS 8.1–9.0 (full ATO if code delivery confirmed)
- [H1 open-redirect / non-binding redirect_uri on generate-authcode] — CVSS 6.1 (confirmed step-1)
