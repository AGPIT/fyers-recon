
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

===== ANALYST 2026-08-07 23:01:32 UTC =====
[CHANGED] Authorization basis: FYERS runs a public bug-bounty program (fyers.in/bug-bounty-program, live 2025-10-04); scope = FYERS Trading Platform (Web & Mobile) + FYERS APIs; submissions only via official Zoho form — supersedes the prior "no VDP/program" blocker.
[CHANGED] H1 (login cb/redirect_uri open redirect + code-interception→ATO chain) → NOT ELIGIBLE: program excludes open redirects and "OAuth flows functioning as designed"; ATO chain is phishing-dependent ("Findings Requiring Independent Compromise").
[CHANGED] x-validate HMAC key (sha256:979cfb8f…) → NOT ELIGIBLE (excluded "non-sensitive keys intended for public client-side usage").
[CHANGED] H10/H14/H16/H8 → Informational only (error/debug disclosure, schema oracles, no demonstrated customer impact).
[CHANGED] H11 (/cdsl/dev/*) → likely OUT OF SCOPE ("Development environments" excluded); needs program scope confirmation.
[CHANGED] IDOR family H13/H15/H17 → conditional, researcher execution prohibited (own-account-only PoC rule, no fake KYC, no cross-account); submit as reproducible-description + FYERS-side validation.
[NEW] Compliance guardrails adopted: no OTP/SMS dispatch, no rate-limit bypass, no cross-account access, no market-hours trading actions, own-account PoCs only, submissions only via official form.
[PRIO] api-a1.fyers.in/signup/v2 KYC (req_id-keyed) — **7.00** = attack 8, business 9, tech 7, gate 4, cloud 5, fresh 6
[PRIO] api-t1.fyers.in/indus/user/v1 saved-chart gallery (chartId object) — **6.15** = attack 7, business 7, tech 6, gate 5, cloud 5, fresh 5
[PRIO] api-a1.fyers.in/marina/v1/ddpi (instruction objects) — **6.00** = attack 7, business 7, tech 5, gate 5, cloud 5, fresh 5
[HYP] signup/v2 KYC cross-application IDOR via `req_id` (H17)
class: IDOR
asset: api-a1.fyers.in/signup/v2/user/{esign-document,pdf/generate,status/poll,esign/accept-name-mismatch}
confidence: 45
reasoning: account-creation objects are keyed by application `req_id` threaded via `digio_doc_id` through eSign/PDF/KYC-status flows (`live-verification?source={verification|pan|esign|edit_address}&req_id=`); pre-auth gate is validation-before-auth only (`1050`/`1500`, OTP dispatch is the auth-gated step) so data-layer authorization scoping is unproven.
evidence_needed: 200 vs 403/404 when the same endpoints are requested with a second applicant's `req_id` under one authenticated applicant session; whether a self-computed `x-validate` (client-embedded constant, attacker-computable) unlocks any pre-auth step.
verify_steps: AUTH_HELPED: (researcher execution prohibited — program rules; FYERS-side design) authenticated own-app session → `GET /signup/v2/user/status/poll`/`esign-document`/`pdf/generate` with own `req_id` baseline, then a foreign application's `req_id`, diff codes. No OTP dispatch / no real-number submission by anyone.
impact: cross-application read of unmasked PAN, eSign documents, KYC PDFs → program Critical ("PAN disclosure") / High ("KYC stage disclosure").
testability: HUMAN_ONLY
[HYP] indus/user saved-chart object-keyed IDOR (H13)
class: IDOR
asset: api-t1.fyers.in/indus/user/v1/gallery + data.fyers.in/dev-fyers/savechart/1.2/{charts,study_templates}
confidence: 42
reasoning: chart/study-template objects are keyed by opaque `chartId`/study-id decoupled from `tokenHash` (`at_hash`) scope; pre-auth gate is content-type-sensitive method-gate (500 invalid-method), no user data reachable pre-auth.
evidence_needed: 200 vs 403 when a second account's `chartId`/study-template id is requested under one authenticated `_FYERS` session; whether gallery DELETE accepts any `token_id`.
verify_steps: AUTH_HELPED: (own-account only; FYERS-side design) `GET /indus/user/v1/gallery` (or `GET /savechart/1.2/charts` with session) to list own ids, then request a foreign `chartId`/study-template id and diff response codes; test DELETE `token_id` scope on gallery.
impact: cross-account read/delete of saved charts and study templates → program High ("viewing another user's watchlists") / Medium (limited IDOR).
testability: HUMAN_ONLY
[HYP] marina/ddpi instruction-object IDOR (H15)
class: IDOR
asset: api-a1.fyers.in/marina/v1/ddpi/{status,timeline,user/listing/file}
confidence: 40
reasoning: DDPI (Demat Debit & Pledge Instruction + Digio eSign) status/timeline/file endpoints are instruction-object-keyed; uniformly `-16` auth-first pre-auth (no pre-auth oracle), so scoping is unproven until an authenticated session exists.
evidence_needed: 200 vs 403 when a second account's instruction id is requested on status/timeline/file under one authenticated session.
verify_steps: AUTH_HELPED: (own-account only; FYERS-side design) create own DDPI instruction, then request a foreign instruction id on `GET /marina/v1/ddpi/status` and `/timeline`, diff codes.
impact: cross-account instruction status/file read → program Medium–High.
testability: HUMAN_ONLY
[PARKED] H1 login `cb`/`redirect_uri` OAuth flow — program terms explicitly exclude open redirects and OAuth-as-designed; ATO chain is phishing-dependent (excluded); no bounty value, informational/hardening only.
[PARKED] x-validate HMAC key (sha256:979cfb8f…) — excluded class (public client-side key); no bounty.
[PARKED] H12 invest/admin presence oracle + `souce` typo; H14 api-a1-prod funds/oracles; H16 email-preference; H8 MCP; H10 chart SQL error oracle — Informational only (no demonstrated impact), not hypotheses.
[PARKED] SmartHunt "SSTI in onload" — rejected false positive (baseline 404 NoSuchKey vs injected 429/errorCode1015 CF block, no `49` rendered).
[PARKED] Third-party hosts (Grammarly/Coda/Superhuman/Zoho) — OOS per scope and program.
[FINAL] survivors re-ranked: H17 (45) > H13 (42) > H15 (40) — all HUMAN_ONLY, FYERS-side validation required; none executed by researcher.
[NEXT] HUMAN: Finalize the official-form submission package — H17 as reproducible-description + explicit request for FYERS-side validation, plus the informational/hardening bundle (H1 redirect handling, H12 `souce`/presence oracle, H14 realtime-funds error-wrap, H10 error oracle, x-validate hardening note), and in the same message request program scope-confirmation for borderline hosts (signup.fyers.in, mcp.fyers.in, /invest/admin/*, /cdsl/dev/*) before any further active work.
[LEARN] ACCEPTED IDOR @ api-a1.fyers.in/signup/v2 (`req_id`-keyed KYC objects): three independent object-keyed IDOR candidates now documented (signup/v2, indus/savechart, marina/ddpi) — class conditionally alive, all unproven, all require FYERS-side validation under program rules.
[LEARN] REJECTED OATH (open-redirect-only) @ login.fyers.in: program terms exclude open redirects and OAuth-as-designed; H1 not bounty-eligible — class dead for scoring purposes.
[LEARN] REJECTED XSS/SSTI @ api-connect-docs.fyers.in: static S3/Docusaurus; injected `onload` → CF 429/errorCode1015 rate-limit/WAF block, no `49` rendered — false positive reaffirmed (second host corroboration).
[RISK] fyers: 62 — A broad 41-host production estate with fragmented per-route×method authorization across ≥19 auth domains (shared numeric code space `-15/-16/-17/-27/-374/-441`, Pydantic `#15`, `1050/1500`), multiple validation-before-auth paths including a fund-withdrawal primitive and a KYC OTP send, raw upstream error-wrap + internal `latency` disclosure, second public config artifact with new live OAuth client_ids, exposed default test/dev hosts, an unauthenticated public IPO/ticker tier, and a confirmed open-redirect primitive on the login host. Now under an explicit public bounty program, but the only genuine High/Critical potential (object-keyed IDOR family) is unproven and researcher-execution-prohibited; the majority of the corpus is informational or not bounty-eligible, and two SSTI reports are rejected false positives.

===== ANALYST 2026-08-07 23:54:12 UTC =====
[NEW] Authorization basis: FYERS public bug-bounty program (`fyers.in/bug-bounty-program`, live since 2025-10-04) — scope = FYERS Trading Platform (Web & Mobile) + FYERS APIs part of trading platform; submission only via official Zoho form (`forms.fyers.in` BugBountyForm1) — supersedes the prior "no VDP/program" blocker.
[NEW] Deliverable `reports/submission-package_fyers-bb.md` written (2026-08-07 23:2x) — H17 primary + H13/H15 PoC designs + informational/hardening bundle; no live requests made.
[NEW] `api-a1.fyers.in/signup/v2/*` KYC account-opening API (auth domain #19, own `1050/1500` code space, application-keyed by `req_id`/`digio_doc_id`) recovered from `signup.fyers.in/main.dart.js`.
[NEW] `config.fyers.in/config/config.gz` — second public config artifact (S3 JSON, 135,008 B, 41 hosts, 989 URLs; 13 URLs unique vs `indus/v1/config`, incl. 3 new live OAuth client_ids) — configs must be diffed each run.
[NEW] `api-a1-prod.fyers.in` FastAPI/Pydantic microservice gateway (auth fingerprints #15/#16/#17); `withdraw-fund` validation-before-auth on a money path; `realtime-funds` HTTP-200 raw trade-core `-16` error-wrap + internal `latency`.
[NEW] `mtfddpi.fyers.in` → `api-a1.fyers.in/marina/v1/ddpi/{esign,esignValidate,approveName,status,timeline,user/listing/file}` + `marina/v1/mtf/send_\otp` (all auth-first, `-16`).
[NEW] `utility/v2/public/email-preference` validation-before-auth (auth fingerprint #18: `401 Invalid or expired authorization token`); schema recovered from 31 MB web bundle.
[NEW] `ipo.fyers.in` public, unauthenticated, object-keyed `offer_details_v2` query on `api-i1`.
[NEW] Static inventory: `fy/cdsl/v2/*` (prod v2 twin of `cdsl/dev`), `user/v3/app/*` OAuth app registry, `nucleus/v1/fia/{chart-insights,option-chain-insights}`, `journal.fyers.in/journal/?token=`, `charts-cdn.fyers.in`.
[CHANGED] H1 (login `cb`/`redirect_uri`) reclassified to NOT ELIGIBLE (open redirects / "OAuth flows functioning as designed" excluded; ATO chain phishing-dependent) — no ATO framing, informational only.
[CHANGED] x-validate client HMAC key (sha256:979cfb8f…) → NOT ELIGIBLE ("non-sensitive keys intended for public client-side usage").
[CHANGED] H10/H12/H14/H16/H8 → Informational only; H11 `/cdsl/dev/*` likely OUT OF SCOPE (dev environments excluded) — scope-confirmation pending.
[CHANGED] IDOR family H13/H15/H17 → conditional, researcher execution prohibited (own-account-only PoC rule); submit as reproducible-description + FYERS-side validation.
[CHANGED] Guardrails in force: no OTP/SMS dispatch, no rate-limit bypass, no cross-account access, no market-hours trading actions, submissions ONLY via official form.
[PRIO] api-a1.fyers.in/signup/v2 KYC (`req_id`-keyed) — **7.00** = attack 8, business 9, tech 7, gate 4, cloud 5, fresh 6
[PRIO] api-t1.fyers.in.inds/user/v1 saved-chart gallery (opaque `chartId`/study-id) — **6.15** = attack 7, business 7, tech 6, gate 5, cloud 5, fresh 5
[PRIO] api-a1.fyers.in/marina/v1/ddpi (instruction objects) — **6.00** = attack 7, business 7, tech 5, gate 5, cloud 5, fresh 5
[HYP] signup/v2 KYC cross-application `req_id` access (H17)
class: IDOR
asset: api-a1.fyers.in/signup/v2/user/{esign-document,pdf/generate,status/poll,esign/accept-name-mismatch,esign-success,esign/clear-status,change-esign-status}
confidence: 45
reasoning: Account-creation objects are keyed by application `req_id` threaded through `live-verification?source={verification|pan|esign|edit_address}&req_id=` and `digio_doc_id` (eSign/PDF/status flows). Pre-auth gate is validation-before-auth only (`1050/1500`, OTP dispatch is the gated step), so data-layer authorization scoping is unproven. If `req_id` is the sole object key and authz is session-scoped, a foreign `req_id` yields cross-app KYC PII/eSign documents/PDFs.
evidence_needed: 200-with-data vs 403/404/authent, when one authenticated applicant session requests another applicant's `req_id` on the listed endpoints; and whether a self-computed `x-validate` (client-embedded key, attacker-recomputable) unlocks any pre-auth step.
verify_steps: AUTH_HELPED: (authorized, FYERS-side only — researcher execution prohibited by program rules) own-app `req_id` baseline on `GET/POST /signup/v2/user/status/poll`, `/user/ping-document`, `/user/pdf/generate`, then substitute a second application's `req_id`; diff response codes. No OTP/real-number contact.
impact: cross-application read of unmasked PAN, eSign documents, KYC PDFs → program Critical ("PAN disclosure")/High ("KYC stage disclosure").
[HYP] indus/user saved-chart object-keyed IDOR (H13)
class: IDOR
confidence: 42
asset: fyi-t1.fyi.fyers.in .. indus/eser/v1/saved-chart/gallery + data.fyers.in/dev-fyers/savechart/1.2/{charts,study_templates}
reasoning: Saved-chart/study-template objects are keyed by opaque `chartId`/study-id decoupled from `tokenHash` (`at_hash`) scope; pre-auth method-gate is content-type-sensitive (500 invalid-method), no user data pre-auth. `_FYERS`-cookie auth with `tokenHash` org scoping is unproven.
evidence_needed: 200-with-data vs 403/404 when a second account's `chartId`/study-template id is requested under one authenticated `_FYERS` session; whether gallery DELETE accepts a foreign `token_id`.
verify_steps: AUTH_HELPED: (FYERS-side/own-account design) `GET /indus/user/v1/gallery` (or `GET /savechart/1.2/charts` with session) to list account A ids, then request a foreign `chartId`/study template id; diff codes; test `token_id` scope on DELETE. Passive re-fetch of the 500 method-gate already recorded.
impact: cross-account read/delete of saved charts and study templates → program High ("viewing another user's watchlists")/Medium (limited IDOR).
testability: HUMAN_ONLY
[HYP] bearer-scope gap: `marina`/DDPI instruction-object IDOR (H15)
class: IDOR
confidence: 40
asset: api-a1.fing/in/marina/v1/ddpi/{status,timeline,user/listing,file/esign}
reasoning: DDPI (Demat Debit & Pledge Instruction + Digio eSign) status/timeline/file endpoints are instruction-object-keyed; uniformly `-16` auth-first pre-final (no pre-auth oracle), so object-scope authorization is unproven until an authenticated instruction exists.
evidence_needed: 200-with-data vs 403/404 when a second account's instruction id is requested on status/timeline/file under one authenticated session.
verify_steps: AUTH_HELPED: (FYERS-side design) own DDPI instruction established (own account), then request a foreign instruction id on `GET /marina/v1/mtp/status` and `/timeline`, diff codes. No execution by researcher.
impact: cross-account DDPI instruction status/documental read → program Medium–High.
testability: HUMAN_ONLY
[PARKED] H1 login `cb`/`redirect_uri` (OAuth) — not eligible under program terms (open redirects; OAuth-as-designed); informational/hardening carryover only.
[PARKED] Hard-coded `x-validate` client key (sha256:…c20ce?) — excluded category "non-sensitive keys intended for public client-side usage"; weaker anti-CSRF note only.
[PARKED] H14 funds tier (`withdraw-fund` validation-before-method-on-money-path, per-route×method auth fragmentation, `realtime-funds` error-wrap) — Informational; no customer-data impact demonstrated, cross-account inaccessible under own-account rule.
[PARKED] email-preference (H16), H10 chart SQL error-oracle, H12 invest/admin `-19`/`souce` presence oracle, H8 MCP initialize — informational/hardening, no demonstrated impact.
[PARKED] SmartHunt "SSTI-in-`onload`" — false positive (baseline 404 NoSuchKey vs injected 429 CF `errorCode 1015`, no `49` rendered), corroborated across two hosts; class excluded.
[PARKED] Third-party host inventory (Grammarly/Coda/Superhuman/Zoho) — out of program scope and run scope.
[FINAL] survivors re-ranked: H17 signup/v2 (60) > H13 saved-chart (45) > H15 marina/dtpi (44) — all HUMAN_ONLY (FYERS-side), none executed by researcher.
[NEXT] HUMAN: review `reports/submission-package_fyers-bb.md` against the program terms, then submit the official-form package via `spreadsheets/forms.fyers.in` Bug if senior participation/priority, but *only after* sending the documented scope-confirmation query to the program contact for the borderline surfaces (signup/v2 KYC, mcp.fyers inclusive, `/invest/uiet`, `/cdsi/started`) — no next action; H17/H13/H15 remain blocked on that confirmation and FYERS-side data-object validation (no OTP/data-dispatch until scoped).
[LEARN] REJECTED XSL/SSTI class @ api-connect-docs.fyers.in (static Docusaurus/S3, NoSuchKey then CF 125/errorCode1015 block, no `49`) — reaffirmed across two hosts; report-generation noise.
[LEARN] ACCEPTED IDOR class @ api-a1.signup/v2 + data/indus-saved-charts + marina/ddpi — three independent object-keyed authorization surfaces exist (req_id, chartId, instruction-id), all unproven, all require FYERS-side validation under program rules (researcher execution prohibited).
[LEARN] REJECTED OATH (open-redirect-only) @ login.fyers.in — program terms exclude open-reflects and OAuth-as-designed; class dead for scoring purposes.
[LEARN] REJECTED hard-coded-key category (`x-validate` HMAC) — excluded by program (public client key); informational only.
[RISK] fyers — 62 — 41-host production estate, now under an explicit public bounty program that authorizes active testing of trading-platform APIs, but every genuine High/Critical lead (object-keyed IDOR family H13/H15/H17, incl. PAN/PDF/eSign exposure) is unproven and researcher-execution-prohibited under the program's own-account rule; validation-before-auth on money (withdraw-fund) and OTP-send (signup/v2) paths, per-route×method authorization fragmentation, raw upstream error-wrap + internal `latency` disclosure, and second public config with new OAuth client ids broaden the likely attack surface. Offset: no confirmed high-severity exploit in corpus, auth-gated data layers, H1 OATH class and SSTI class rejected, majority of corpus informational/not bounty-eligible.

===== ANALYST 2026-08-08 02:27:47 UTC =====
[PRIO] api-t1.fyers.in/user/v3/app/* (OAuth trading-app registry: create/update-trading-app/delete/verify-webhook/get-common-apps/updateAppStatus) — **5.75** = attack 6, business 7, tech 8, gate 2, cloud 5, fresh 5
[PRIO] api-t1.fyers.in/nucleus/v1/fia/{chart-insights,option-chain-insights} — **5.35** = attack 5, business 6, tech 7, gate 3, cloud 5, fresh 6
[PRIO] charts-cdn.fyers.in — **5.00** = attack 4, business 4, tech 4, gate 8, cloud 7, fresh 5
[HYP] Trading-app registry webhook/redirect registration validation gap
class: BUSLOGIC
asset: api-t1.fyers.in/user/v3/app/* (create, update-trading-app, verify-webhook)
confidence: 35
reasoning: Static-inventory only (web-bundle recovery), never live-probed, token-gated. The estate's login OAuth `cb`/`redirect_uri` handler is non-denylisted for FYERS-internal cbs (H1 parked; program excludes "OAuth flows functioning as designed"). If the registry accepts attacker-controlled webhook/redirect_uri URLs it re-opens a parked class, but zero evidence exists yet.
evidence_needed: whether create/update-trading-app allowlists redirect_uri/webhook, and whether verify-webhook fires server-side callbacks (SSRF primitive).
verify_steps: AUTH_HELPED: (blocked until scope-confirmed; own-account only) create a test trading app and submit a foreign/attacker-controlled webhook URL, observe callback; no pre-auth oracle exists on this route family.
impact: OAuth app-registry manipulation or callback SSRF — program Medium at best, likely OAuth-as-designed excluded.
testability: HUMAN_ONLY
[HYP] FIA assistant endpoints: unproven per-user data-scope enforcement
class: AUTH
asset: api-t1.fyers.in/nucleus/v1/fia/{chart-insights,option-chain-insights}
confidence: 38
reasoning: A single FIA_TOKEN is documented to span positions+orders+GTT+alerts+MF (hunter H8 — broad, not least-privilege); `nucleus/v1/fia/*` is the same assistant subclass on api-t1, never live-probed, so its gate and data-scoping are unknown. Given the estate's ≥19 fragmented auth domains, a validation-before-auth or token-presence-only variant cannot be ruled out from static inventory.
evidence_needed: auth-gate fingerprint without a token; whether chart/option-chain insights responses are per-user scoped.
verify_steps: PASSIVE — `GET https://api-t1.fyers.in/nucleus/v1/fia/chart-insights` with no auth header → record status/body and compare error code against known fingerprints (#15 Pydantic, #16 `-374`, #17 `-1`, #19 `1050/1500`). AUTH_HELPED cross-scope test blocked (requires a real FIA_TOKEN).
impact: unauthorized read of chart/option-chain insights (Low); worst-case broad-token cross-data access (Medium, conditional).
testability: PASSIVE
[HYP] charts-cdn.fyers.in: public S3 bucket / listing misconfig
class: MISCONFIG
asset: charts-cdn.fyers.in
confidence: 35
reasoning: Estate precedent: S3-backed static hosts with public objects (`config.fyers.in/config/config.gz` is public S3 JSON; api-connect-docs returns S3 `NoSuchKey`). charts-cdn is a never-probed CDN/static host. If the bucket ACL permits `s3:ListBucket` or write, chart assets/config leak or defacement.
evidence_needed: HTTP 200 with `ListBucketResult` XML on `/?list-type=2`; presence/absence of `x-amz-*` deny headers; `NoSuchBucket` vs `AccessDenied`.
verify_steps: PASSIVE — `GET https://charts-cdn.fyers.in/` and `GET https://charts-cdn.fyers.in/?list-type=2` → inspect for `ListBucketResult`/`AccessDenied`/`NoSuchBucket` and `x-amz-bucket-region`/`x-amz-request-id`.
impact: public chart-asset/config disclosure (Low) or write/defacement (Medium) — conditional.
testability: PASSIVE
[PARKED] user/v3/app registry webhook/redirect gap: confidence 35 < 40; class overlaps program-excluded "OAuth flows functioning as designed"; no pre-auth oracle, AUTH_HELPED blocked on scope confirmation.
[PARKED] FIA endpoint scope: confidence 38 < 40; gate fingerprint is recon, not demonstrated impact; cross-scope test blocked (needs authenticated FIA_TOKEN).
[PARKED] charts-cdn S3 listing: confidence 35 < 40; estate precedent (api-connect-docs S3 NoSuchKey, public config intended-public) makes open listing unlikely; no demonstrated impact.
[FINAL] survivors re-ranked (unchanged, no new survivors this run): H17 signup/v2 req_id IDOR (45) > H13 indus/savechart object IDOR (42) > H15 marina/ddpi instruction IDOR (40) — all HUMAN_ONLY, FYERS-side validation required, researcher execution prohibited.
[NEXT] PROBE: passive, read-only, ≤6 spaced (≥30s) GETs on pending recon surface per state — (1) `GET https://api-t1.fyers.in/nucleus/v1/fia/chart-insights` (no auth → fingerprint gate vs #15/#16/#17/#19); (2) `GET https://journal.fyers.in/journal/` and `GET https://journal.fyers.in/journal/?token=<random>` (observe redirect/4xx, reflection, app fingerprint — no valid session contact); (3) `GET https://charts-cdn.fyers.in/` + `/?list-type=2` (S3 listing check); (4) `GET https://trade2.fyers.in/` (fingerprint). No auth, no OTP, no PII, no volume.
[LEARN] No class newly proven dead or alive this run (docs-only + no live probes). Reaffirmed: ACCEPTED (conditional, unproven) IDOR class @ signup/v2 req_id + indus/savechart + marina/ddpi; REJECTED XSS/SSTI @ api-connect-docs (CF 1015 block, no `49`) and REJECTED OATH @ login.fyers.in (program-excluded) remain closed.
[RISK] fyers: 62 — 41-host production estate under a live public bounty program; all genuine High/Critical potential (object-keyed IDOR family H13/H15/H17 incl. PAN/eSign/PDF exposure) is unproven and researcher-execution-prohibited under the own-account rule; validation-before-auth on a money path (withdraw-fund) and KYC OTP-send (signup/v2), per-route×method auth fragmentation across ≥19 domains, raw upstream error-wrap + internal `latency` disclosure, and two public configs with live OAuth client_ids broaden attack surface. Offset: no confirmed high-severity exploit, auth-gated data layers, OATH and SSTI classes rejected, majority of corpus informational/non-eligible.

===== ANALYST 2026-08-08 04:33:32 UTC =====
[NEW] journal.fyers.in "Journal" trade-journal Flutter SPA mapped (main.dart.js 4.4 MB): bootstrap `getTokenFromQueryParam()` sets `_FYERS` cookie from `?token=` unvalidated; backend = `api-a1-prod.fyers.in/journal-server/*` (12 endpoints: v2/{summary,orders-list,positions-list}, v1/{notes-list,note/create,note/edit/,note/delete/,note/detail,note/search,note/recent-searches,tag/get-or-create,upload-document}) + `api-a1.fyers.in/reports/v2/api/journal_pnl` + `api-t1.fyers.in/gk/verify_token`.
[NEW] Auth fingerprint #20: `journal-server/*` no-auth → HTTP 403 `{"code":401,"message":"unauthorized","response":""}` (distinct from api-a1-prod `-374`/`-17`/Pydantic #15; `gk/verify_token` = trade-core `-16`). Auth-first, no pre-auth oracle.
[NEW] H18 IDOR candidate: per-account notes/tags + `upload-document` file objects behind `_FYERS` (4th object-keyed surface, same class as H13/H15/H17).
[NEW] Client primitives (informational): unvalidated `?token=` setter + SPA logout `_FYERS=-1` at `domain=.fyers.in` → crafted link clears the apex cookie across ALL *.fyers.in properties; hardcoded OAuth `state=sample_state`; no Referrer-Policy on tokenized deep-links.
[NEW] OAuth client_id `3NF8I0C645-101` registered redirect `https://journal.fyers.in/` (H1-adjacent; H1 already not eligible).
[CHANGED] `trade2.fyers.in` → no A record (dead; drop with betatrade/datapub). `charts-cdn.fyers.in` → S3 CDN asset only (NoSuchKey on `/`).
[PRIO] api-a1.fyers.in/signup/v2 KYC (`req_id`-keyed, H17) — **7.00** = attack 8, business 9, tech 7, gate 4, cloud 5, fresh 6
[PRIO] api-t1/indus/user saved-chart gallery (opaque `chartId`, H13) — **6.15** = attack 7, business 7, tech 6, gate 5, cloud 5, fresh 5
[PRIO] api-a1-prod.fyers.in/journal-server (H18, NEW) — **6.00** = attack 7, business 6, tech 6, gate 3, cloud 5, fresh 9
[PRIO] api-a1.fyers.in/marina/v1/ddpi (H15, unchanged) — **6.00** = attack 7, business 7, tech 5, gate 5, cloud 5, fresh 5
[HYP] journal-server note/upload object-keyed IDOR (H18, NEW)
class: IDOR
asset: api-a1-prod.fyers.in/journal-server/v1/{note/detail,note/edit/,note/delete/,upload-document,tag/get-or-create} + v2/{summary,orders-list,positions-list}
confidence: 40
reasoning: Trade-journal objects (notes, tags, uploaded documents) are keyed per-account behind `_FYERS`; endpoint ids are opaque object keys (note id / tag id / document). Pre-auth gate is auth-first HTTP 403 (#20), so data-layer authorization scoping is unproven until an authenticated session exists. Estate precedent: three prior object-keyed surfaces (req_id, chartId, instruction-id) all unproven authz.
evidence_needed: 200-with-data vs 403/404 when one authenticated account's session requests a second account's note/tag/document id on detail/edit/delete/upload-document; whether `upload-document` file URLs are keyed to the owning account.
verify_steps: AUTH_HELPED: (FYERS-side/own-account only — researcher cross-account execution prohibited) baseline `GET /journal-server/v1/notes-list` with own `_FYERS` session, then substitute a foreign note id on `note/detail/{id}`, `note/edit/{id}`, `note/delete/{id}`, and a foreign document reference on `upload-document`; diff codes. No live dispatch.
impact: cross-account read/edit/delete of trade-journal notes, tags and uploaded documents → program Medium–High (conditional, unproven).
testability: HUMAN_ONLY
[HYP] signup/v2 KYC cross-application `req_id` access (H17, carried — compact)
class: IDOR
asset: api-a1.fyers.in/signup/v2/user/{esign-document,pdf/generate,pdf/poll,status/poll,esign/accept-name-mismatch,esign-success,esign/clear-status,change-esign-status}
confidence: 45
reasoning: Account-creation objects keyed by application `req_id` threaded via `digio_doc_id`; pre-auth gate validation-before-auth only (`1050/1500`); authz scoping unproven.
evidence_needed: 200-with-data vs 403/404 on cross-app `req_id` substitution; whether self-computed `x-validate` unlocks any pre-auth step.
verify_steps: AUTH_HELPED: two-applicant R_A/R_B own-session baseline then B-keyed substitution on the listed endpoints (FYERS-side).
impact: unmasked PAN/eSign/PDF cross-application read → program Critical ("PAN disclosure")/High (conditional).
testability: HUMAN_ONLY
[HYP] indus/user saved-chart object-keyed IDOR (H13, carried — compact)
class: IDOR
asset: api-t1/indus/user/v1/gallery + data.fyers.in/dev-fyers/savechart/1.2/{charts,study_templates}
confidence: 42
reasoning: Saved-chart/study-template objects keyed by opaque `chartId` decoupled from `tokenHash` scope; pre-auth gate content-type-sensitive, no user data pre-auth.
evidence_needed: 200-with-data vs 403/404 on foreign `chartId` under one `_FYERS` session; DELETE `token_id` scope.
verify_steps: AUTH_HELPED: own-account gallery list → foreign chartId/study-template read and DELETE delta (FYERS-side).
impact: cross-account read/delete of saved charts → program High/Medium (conditional).
testability: HUMAN_ONLY
[PARKED] SmartHunt "SSTI in `issue_id`" — false positive reaffirmed (4th triage): its own Response 2 is a CF `429` `errorCode:1015` block with no `49`; baseline is 401 `-27`. Class excluded.
[PARKED] journal client primitives (apex `_FYERS=-1` cross-property logout via crafted `?token=` link; hardcoded OAuth `state=sample_state`; missing Referrer-Policy) — informational/hardening only; no demonstrated impact, OAuth-as-designed excluded. Add to informational bundle, not hypotheses.
[PARKED] New OAuth client_id `3NF8I0C645-101` — public identifier; H1 already not bounty-eligible.
[PARKED] charts-cdn.fyers.in S3 listing (prior MISCONFIG) — confirmed S3 asset host, NoSuchKey on `/`; no evidence of open listing.
[FINAL] survivors re-ranked: H17 (45) > H13 (42) > H18 journal (40) > H15 marina/ddpi (40) — all IDOR, all HUMAN_ONLY (FYERS-side), researcher execution prohibited; none executed.
[NEXT] PROBE: passive, read-only — extract exact journal-server request shapes from the locally-cached `journal.dart.js` (4.4 MB, single fetch): note-id/tag-id key names, `upload-document` field names, and auth-header/`_FYERS`-cookie handling, to make H18 `verify_steps` concrete before it is appended to the submission package. No auth, no dispatch, no cross-account access.
[LEARN] ACCEPTED IDOR class @ api-a1-prod/journal-server (journal notes + upload-document file objects) — 4th independent object-keyed authorization surface in the estate (req_id/chartId/instruction-id/note-id); conditionally alive, all unproven, all FYERS-side validation required.
[LEARN] REJECTED SSTI @ api-i1/invest/admin/v1/sgb/issue-list — reaffirmed 4th time: baseline 401 `-27` vs injected 429 CF `errorCode:1015` block, no template evaluation; SmartHunt `issue_id` report noise.
[LEARN] REJECTED OATH (open-redirect-only) @ login.fyers.in + OAuth-as-designed (hardcoded `state`) — program-excluded; class dead for scoring.
[RISK] fyers: 63 — estate grown to 42 live hosts / ≥20 auth domains: new journal-server surface adds a 4th object-keyed IDOR candidate including file upload, an unvalidated `?token=`→`_FYERS` cookie primitive enabling apex-wide cross-property logout via one link, and CF bot cookies scoped `Domain=fyers.in` estate-wide; prior High/Critical potential (IDOR family H13/H15/H17/H18 incl. PAN/PDF/eSign exposure) remains unproven and researcher-execution-prohibited. Offset: auth-first gates on all new surfaces (no pre-auth oracle), no confirmed high-severity exploit, SSTI and OATH classes rejected, majority of corpus informational/not bounty-eligible.
