
===== ANALYST 2026-08-07 15:53:49 UTC =====

===== ANALYST 2026-08-07 16:13:57 UTC =====

===== ANALYST 2026-08-07 17:14:39 UTC =====
[HYP] H1 — OAuth `redirect_uri`/`cb` flow: open redirect confirmed, auth-code interception conditional
class: OATH
asset: login.fyers.in + api-t1.fyers.in/api/v3/generate-authcode + api.fyers.in/api/v2/direct-login
confidence: 60
reasoning: `generate-authcode` returns HTTP 200/60,917 B accepting any non-empty `redirect_uri` (only Go `required` presence check; value never reflected or registry-checked pre-auth); deobfuscated `login.min.js?v=1.3` routes post-auth nav from `location.search` `redirect_uri`/`cb` with only a 5-value exact-match denylist that `evil.example.com/cb` and `savedcharts.fyers.in` bypass; `LoginClient.redirection_url = params.redirect_uri` forwards hostile URI into the direct-login body, never malformed-rejected at any pre-auth gate. v2 is scope-first, v3 is auth-first (`-441`).
evidence_needed: program-side authenticated observation of `data.Url` (does it embed the OAuth `auth_code`?), and whether the server registry-checks `redirect_uri` at issuance.
verify_steps: AUTH_HELPED: (only after authorization confirmed) victim logs in via crafted `cb=https://evil.example.com/cb` → confirm post-auth navigation; inspect `data.Url` from `/api/v2/direct-login`/`/api/v3/direct-login`/`/tradingview/auth/direct-login` for `code`/`auth_code` and attempt redemption with attacker-controlled `code_verifier`. Passive-only until then: re-fetch of `login.min.js` for bundle-diff is allowed.
impact: indicator 1 = open redirect from trusted login host (6.1, confirmed); indicator 2 = auth-code interception → full trading-account takeover (8.1–9.0, conditional; PKCE moot since attacker controls challenge+verifier).
testability: AUTH_HELPED
[HYP] signup/v2 KYC application-object IDOR via `req_id`
class: IDOR
asset: api-a1.fyers.in/signup/v2/user/{esign-document,pdf/generate,status/poll,esign/accept-name-mismatch}
confidence: 45
reasoning: account-creation objects keyed by application `req_id` threaded via `digio_doc_id` through eSign/PDF/KYC-status flows (`live-verification?source={verification|pan|esign|edit_address}&req_id=`); pre-auth gate is a validation-before-auth 1050/1500 oracle (OTP dispatch is the auth-gated step); authorization scoping at the data layer is unproven. If `req_id` is the only object key and authz is session-scoped to the applicant, cross-app reads succeed.
evidence_needed: 200 vs 403/404 when requesting `/user/esign-document` or `/user/pdf/generate` with a second applicant's `req_id` under one authenticated applicant session.
verify_steps: AUTH_HELPED: (only after authorization confirmed) with an authenticated applicant session and own `req_id`, request the same endpoints with another application's `req_id` and diff response codes; also test whether a correctly-HMAC'd `x-validate` (key `979cfb8f1e1a3aff2a59299d1a0791bafa8552e99db9471008c16cfc4d855587`) unlocks any pre-auth step. No OTP dispatch / no real-number submissions pre-authorization.
impact: cross-application read of KYC PII, PAN, eSign documents, generated PDFs (5.3–7.5 conditional).
testability: AUTH_HELPED
[HYP] api-a1-prod `myaccount/prod` funds tier — validation-before-auth on a money path + per-route authz fragmentation
class: MISCONFIG
asset: api-a1-prod.fyers.in/myaccount/prod/{withdraw-fund,realtime-funds,user-withdrawal-history,my-funds}
confidence: 45
reasoning: `withdraw-fund` POST rejects missing `amount` with HTTP 400 *before* any token check (validation-before-auth on a money-movement primitive); `user-withdrawal-history` is GET `-374` but POST `-17` (method-dependent auth gate); `realtime-funds` returns HTTP 200 wrapping raw upstream trade-core `-16` JSON plus an internal `latency` field, proving the proxy chain and leaking internal timing; `verified-pnl/get-data` is client-side-only JWT gating.
evidence_needed: whether any no-token request on this tier reaches a data operation, and whether a low-privilege session can cross into another account's funds via the fragmented gate map.
verify_steps: AUTH_HELPED: (only after authorization confirmed) walk the full route×method matrix with (a) no token and (b) a low-privilege session, recording gate code per cell; specifically re-test `withdraw-fund` with `amount` present but no auth and observe whether it passes validation into the auth layer or executes. Passive-only re-probe of the error-wrap body is allowed pre-auth.
impact: authorization confusion on fund withdrawal/report surfaces → unauthorized fund operations or cross-account funds disclosure (5.3–6.5 conditional).
testability: AUTH_HELPED
[NEXT] HUMAN: confirm authorization with FYERS before any further step. KB finding stands: no security.txt, no VDP, no public bounty; the only documented channel is `api-support@fyers.in` (from the v3.1 SEBI API spec), plus GitHub `fyers/claude-installer` issue reporting. Until written authorization (scope letter/SOW/contract) is provided, no PROBE may be run against auth/OTP/KYC/trading flows — the three live hypotheses above are all AUTH_HELPED and remain unexecuted. Passive-only work (config.gz vs indus/v1/config diff; JS bundle refresh for drift) may continue.
[RISK] fyers: 62 — broad 41-host estate with fragmented per-route×method authorization across multiple auth domains (`-15/-16/-17/-27/-374/-441`, `1050/1500`, Pydantic `#15`), pre-auth validation oracles including on money-movement and KYC-OTP paths, a large public `screeners`/`koshi` tier, second public config artifact, exposed default-server hosts, no VDP/security.txt, and a confirmed OAuth open-redirect primitive (6.1) with a conditional code-interception path to account takeover (8.1–9.0). Offset by auth-gated data layers, absence of any proven high-severity exploit, and rejection of both SSTI false positives.

===== ANALYST 2026-08-07 18:08:56 UTC =====
[PRIO] `api-appl.fyers.a` signup/v2 KYC (`req_id`-keyed) — **7.05** = attack 8, business 8, tech 7, gate 6 (validation-before-auth public), cloud 6, fresh 6
[PRIO] `api-a1-prod.fyers.in` myaccount/prod funds+withdraw tier — **6.50** = attack 5, business 8, tech 5, gate 7, cloud 5, fresh 8
[PRIO] `api-t1.fyers.in/utility/v2/public` email-preference — **5.55** = attack 5, business 5, tech 5, gate 8, cloud 5, fresh 6
[HYP] signup/v2 KYC application-object IDOR via `req_id`
class: IDOR
asset: api-a1.fyers.in/signup/v2/user/{esign-document,pdf/generate,status/poll,esign/accept-name-mismatch}
confidence: 45
reasoning: account-creation objects are keyed by application `req_id` threaded through eSign/PDF/KYC flows (`live-verification?source={verification|pan|esign|edit_address}&req_id=`); the pre-auth gate is validation-before-auth only (1050/1500), so data-layer authorization scoping is unproven. If `req_id` is the sole object key and authz is applicant-session-scoped, a second application's `req_id` yields cross-app KYC PII/eSign/PDFs.
evidence_needed: 200 vs 403/404 when `GET /signup/v2/user/esign-document|pdf/generate` is issued with a foreign `req_id` under one authenticated applicant session.
verify_steps: AUTH_HELPED: (ONLY after authorization confirmed) submit own `req_id`, then a second application's `req_id` on `/user/esign-document`, `/user/pdf/generate`, `/user/status/poll`, `pdf/generate`, diff codes; separately test whether a self-computed `x-validate` (key is client-embedded, see [LEARN]) unlocks any pre-auth step. No real-number OTP dispatch prior to authorization.
impact: cross-application read of KYC PII, PAN, eSign documents, generated PDFs (5.3–7.5 conditional).
testability: AUTH_HELPED
[HYP] api-a1-prod `withdraw-fund` validation-before-auth on a money path + per-route×method authorization fragmentation
class: MISCONFIG
asset: api-a1-prod.fyers.in/myaccount/prod/{withdraw-fund,realtime-funds,user-withdrawal-history}
confidence: 50
reasoning: `withdraw-fund` POST rejects missing `amount` (HTTP 400) before any token check — validation-before-auth on a fund-withdrawal primitive; `user-withdrawal-history` is GET `-374`/POST `-17` (method-dependent gate); `realtime-funds` returns HTTP 200 wrapping raw trade-core `-16` JSON plus an internal `latency` field, leaking proxy timing and proving the gateway chain. Fragmented gate map across routes×methods may permit cross-account funds reads.
evidence_needed: whether any no-token request on the tier reaches a data operation; whether `amount` present + no auth passes validation into the data layer; whether a low-priv session crosses account isolation.
verify_steps: AUTH_HELPED: after authorization only; walk route×method gate matrix with (a) no token and (b) low-priv session, recording gate code per cell; retest `withdraw-fund` with `amount` present/no auth and observe if it passes validation into the data path. Pre-auth passive re-fetch of the error-wrap body is allowed.
impact: authorization confusion on fund withdrawal/report surfaces (5.3–6.5 conditional).
testability: AUTH_HELPED
[HYP] `utility/v2/public/email-preference` reaches a schema-opaque validation-before-auth gate on a notification-preference endpoint
class: MISSING_AUTHZ
asset: api-mt1.fyers.in/util:utility/v2/public/email-preference
confidence: 45
reasoning: POST `{}`/`{email}`/`{token}`/`{action}` all return `400 Validation failed…` with no token — validation-before-auth on a per-user notification-preference endpoint; response shape `data.{email_disabled,sms_disabled,whatsapp_disabled}` recovered from the web bundle. Body schema is opaque (no field oracle), so reaching the data layer needs a validated body plus a bound identity.
evidence_needed: a schema-valid body that passes validation unauthenticated and changes/reads a user's notification prefs.
impact: schema oracle at best; cross-user notification-preference read/write if a validated body reaches the data layer (3.1–5.3 conditional).
testability: AUTH_HELPED
[PARKED] H1 OAuth `redirect_uri`/auth-code interception — already final in prior analyst (6.1 confirmed open redirect; 8.1–9.0 conditional, PoC design ready); not a NEW asset this run; carried, not re-listed.
[PARKED] DDPI `marina`/`mtfddpi`, IPO `offer_details_v2`, `screeners/koshi` — public/auth-first tiers with no pre-auth oracle (mine read-only, no new exploitation path).
[PARKED] Third-party Grammarly/Coda/Superhuman hosts — OOS (rejected class) per scope `${TARGET}`.
[PARKED] `insurance`/admin-path `souce` typo + presence oracle — reclassified as low hardening/schema-only remm 3.7, kept for report body, not a standalone hypothesis.
[PARKED] email-preference kept, but flagged low; if evidence shows only field-schema leakage and no user-data reachant, retire to hardening.
[PARKED] SmartHunt "SSTI in onload" — REJECTED (see [LEARN]).
[FINAL] survivor re-ranked:
[NEXT] HUMAN: confirm written authorization with FYERS before any active step. Per KB: no VDP/security.txt/no public bounty; only documented channel `api-support@fyers.in` (v3.1 SEBI spec) + GitHub `fyers/claude-installer` issuer. Until authorization (scope letter/SOW) is provided, all three hypotheses remain unexecuted (AUTH_HELPED). Passive continuation order: HEAD `GET config.fyers.in/config/config.gz` vs `indus/v1/config` diff and refresh `signup.fyers.in/main.dart.js` fingerprint for drift — no auth touched.
[LEARN] REJECTED AUTH (server-side SSTI) @ api-connect-docs.fyers.in (static S3/Docusaurus, NoSuchKey; 429/errorCode1015 = CF rate-limit, no `49`) — corroborated across two hosts (api-connect-docs, invest admin).
[LEARN] REJECTED encryption class (client-embedded `x-validate` HMAC keyed by a public bundle constant, alongside a recomputed client tamper-check) — attacker can recompute, no protective value; hardening-only. Not a real secret (hash omitted per rule; it is public).
[LEARN] ACCEPTED OBJECT-KEYED IDOR class as a viable conditional lead on two independent surfaces: `signup/v2` `req_id` (this run) and `data.fpers/savechart` `chartId` (prior) — both require authenticated confirmation.
[RISK] fyers: 62 — 41-host production estate with fragmented per-route×method authorization across ≥19 auth domains (fingerprints sharing a shared numeric code space), multiple validation-before-auth paths including a money-movement primitive and a KYC OTP send, raw upstream error-wrap + internal `latency` disclosure, second public config and three new live OAuth client_ids, exposed default-server hosts, an unauthenticated public IPO/ticker/`schema` tier, an OAuth open-redirect primitive (6.1) with a conditional code-interception path to account takeover (8.1–9.0). Offset by auth-gated data layers, the absence of any proven high-severity exploit, a narrow-object IDOR authorization characterization, and two rejected SSTI false positives.

===== ANALYST 2026-08-07 19:25:57 UTC =====

===== ANALYST 2026-08-07 21:06:13 UTC =====
[HYP] signup/v2 KYC cross-application IDOR via `req_id`
class: IDOR
asset: api-a1.fyers.in/signup/v2/user/{esign-document,pdf/generate,status/poll,esign/accept-name-mismatch}
confidence: 45
reasoning: account-creation objects are keyed by application `req_id`, threaded via `digio_doc_id` through eSign/PDF/KYC-status flows (`live-verification?source={verification|pan|esign|edit_address}&req_id=`). Pre-auth gate is validation-before-auth only (`1050`/`1500`, OTP dispatch is the auth-gated action), so data-layer authorization scoping is unproven. If `req_id` is the sole object key and authz is applicant-session-scoped, a second application's `req_id` yields cross-app KYC PII/eSign docs/PDFs.
evidence_needed: 200 vs 403/404 when the same endpoints are requested with a second applicant's `req_id` under one authenticated applicant session; whether a correctly-HMAC'd `x-validate` (client-embedded constant, attacker-computable) unlocks any pre-auth step.
verify_steps: AUTH_HELPED: (ONLY after authorization confirmed) with an authenticated applicant session, request `/user/esign-document`, `/user/pdf/generate`, `/user/status/poll` with own `req_id` then a foreign `req_id`, diff codes. No OTP dispatch / no real-number submission pre-authorization. Passive-only re-fetch of `signup.fyers.in/main.dart.js` (drift) allowed.
impact: cross-application read of KYC PII, PAN, eSign documents, generated PDFs (5.3–7.5 conditional).
testability: AUTH_HELPED
[HYP] `api-a1-prod` funds tier — validation-before-auth on a money path + per-route×method authorization fragmentation
class: MISCONFIG
asset: api-a1-prod.fyers.in/myaccount/prod/{withdraw-fund,realtime-funds,user-withdrawal-history,my-funds}
confidence: 50
reasoning: `withdraw-fund` POST rejects missing `amount` (HTTP 400) before any token check — validation-before-auth on a fund-withdrawal primitive; `user-withdrawal-history` is GET `-374`/POST `-17` (method-dependent gate); `realtime-funds` returns HTTP 200 wrapping raw upstream trade-core `-16` JSON plus internal `latency`, proving the gateway chain and leaking internal timing. Fragmented gate map across routes×methods may permit cross-account funds reads.
evidence_needed: whether any no-token request on the tier reaches a data operation; whether `amount` present + no auth passes validation into the data layer; whether a low-priv session crosses account isolation.
verify_steps: AUTH_HELPED: (ONLY after authorization confirmed) walk the route×method gate matrix with (a) no token and (b) a low-privilege session, recording the gate code per cell; re-test `withdraw-fund` with `amount` present and no auth to see if validation passes into the data path. Pre-auth passive re-fetch of the error-wrap body is allowed.
impact: authorization confusion on fund withdrawal/report surfaces → unauthorized fund operations or cross-account funds disclosure (5.3–6.5 conditional).
testability: AUTH_HELPED
[HYP] `indus/user` saved-chart gallery object-keyed IDOR behind auth
class: IDOR
asset: api-t1.fyers.in/indus/user/v1/gallery + savedcharts.fyers.in
confidence: 42
reasoning: `savedcharts.fyers.in` authenticates via `_FYERS` cookie, extracts `tokenHash` from JWT `at_hash`, and GET/DELETEs `indus/user/v1/gallery`; `data.fyers.in/dev-fyers/savechart/1.2/{charts,study_templates}` is object-keyed (TradingView 1.2 protocol) with content-type-sensitive method gate, no user data reachable pre-auth. If chart objects are keyed by `chartId` without owner-scope checks, cross-account chart/study read succeeds.
evidence_needed: 200 vs 403 when a second account's `chartId`/study-template id is requested under one authenticated session; whether gallery DELETE accepts any `token_id`.
verify_steps: AUTH_HELPED: (ONLY after authorization confirmed) list own charts via `GET /savechart/1.2/charts` with `_FYERS` session, then request a second account's `chartId`/study-template id and diff response codes. No pre-auth contact beyond the previously recorded 500 method-gate.
impact: cross-account read/deletion of saved charts and study templates (5.3–7.5 conditional).
testability: AUTH_HELPED
[FINAL] survivors re-ranked:
[NEXT] HUMAN: confirm written authorization with FYERS (documented channel `api-support@fyers.in` per v3.1 SEBI spec; GitHub `fyers/claude-installer` for issue reporting; no security.txt/VDP on-target) before any active auth/OTP/KYC/trading probing. All three live hypotheses are AUTH_HELPED and unexecuted pending that. Passive continuation meanwhile: re-fetch `config.fyers.in/config/config.gz` vs `indus/v1/config` for drift (new OAuth client_ids/URLs) and re-fetch `signup.fyers.in/main.dart.js`/`login.min.js?v=1.3` for bundle drift — no auth touched.
[RISK] fyers: 62 — 41+ host production estate with fragmented per-route×method authorization across ≥19 auth domains (shared numeric code space `-15/-16/-17/-27/-374/-441`, Pydantic `#15`, `1050/1500`), multiple validation-before-auth paths including a fund-withdrawal primitive and a KYC OTP send, raw upstream error-wrap + internal `latency` disclosure, exposed default test/dev hosts, second public config and multiple live OAuth client_ids, an unauthenticated public IPO/ticker tier, and a confirmed OAuth open-redirect primitive (6.1) with a conditional auth-code-interception path to account takeover (8.1–9.0). Offset by auth-gated data layers, absence of any proven high-severity exploit, and two rejected SSTI false positives.

===== ANALYST 2026-08-07 22:01:31 UTC =====
