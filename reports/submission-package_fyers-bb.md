# FYERS Bug-Bounty Program — Submission Package Draft (docs-only; no live requests)
Generated 2026-08-08 (deepseek). Read-only research only. Submission channel: official Zoho form
(forms.fyers.in BugBountyForm1). Program: fyers.in/bug-bounty-program. All tests below are
DESIGNS for FYERS Security Team validation or researcher OWN-ACCOUNT use only — none executed.

## A. Conditional object-access (IDOR) designs — researcher cross-account execution PROHIBITED
Method (all): own-account baseline first; then a SECOND OWNED account object-id substitution
under the FIRST session. Success = 200-with-data (foreign object returned); Fixed = 403/404/
auth-error. Two accounts must be researcher-owned; no other user's data touched.

### A1 (H17) — signup/v2 KYC application-object IDOR (req_id-keyed)  [program Critical/High potential]
- Host: api-a1.fyers.in/signup/v2/user/{esign-document,esign-success,esign/accept-name-mismatch,
  esign/clear-status,change-esign-status,pdf/generate,pdf/poll,status/poll} (+ /nri/get-document)
- Object key: application `req_id` (passed through `digio_doc_id`/`live-verification?req_id=`)
- Design: R_A creates application A (req_id_A); R_B creates application B (req_id_B). Under
  R_A's authenticated session call the esign-document/pdf/status endpoints with req_id_B.
- Success indicator: A's session returns a non-empty B-keyed eSign/PDF/status object.
- Severity: Critical/High if unmasked PAN/eSign/PDF exposed (program "PAN disclosure" category);
  Medium if limited. CVSS 8.1–9.1 (conditional).

### A2 (H13) — savedcharts indus/savechart object-keyed IDOR  [program High/Medium]
- Host: api-t1.fyers.in (indus savechart family, `_FYERS` cookie auth on savedcharts.fyers.in)
- Object key: chartId / study-template id
- Design: own-account GET own chartId (baseline 200-with-data) → GET foreign account chartId
  under same session. Also DELETE-substitution.
- Success: foreign chart returned / deleted. Fixed: 403/404.
- Severity: High if viewing another user's watchlists/charts; Medium (limited IDOR) otherwise.

### A3 (H15) — marina/v1/ddpi instruction-object IDOR  [program Medium–High]
- Host: api-a1.fyers.in/marina/v1/ddpi/{status,timeline,user/listing/file,esignValidate,...}
- Object key: DDPI instruction id
- Design: own instruction baseline → foreign instruction id under own session.
- Success: foreign DDPI instruction/status/file returned. Fixed: 403/404.

### A4 (H18) — journal-server note/upload object-keyed IDOR  [program Medium–High]
- Host: api-a1-prod.fyers.in/journal-server/v1/{note/detail?note_ids=&gs=,note/edit/{id},
  note/delete/{id},upload-document} + v2/{summary,orders-list,positions-list}
- Auth: Authorization: Bearer on every route (fingerprint #20: HTTP 403 `{"code":401,...}` pre-auth)
- Object key: note_id / document-id (POST note/create body: position_ids,order_ids,document_ids,
  linked_symbols,tag_ids,trade_date,title,body,emoji)
- Design: own note_id baseline → foreign note_id (note/detail, note/edit, note/delete) and
  uploaded-document read under own session.
- Success: foreign trade-journal note or uploaded document returned. Fixed: 403/404.
- Severity: Medium–High if cross-account notes/uploads exposed. CVSS 5.3–7.5 (conditional).

### A5 (H19) — api-testing-prod KYC upload tier: unauth object-write + SSRF candidate  [program Medium–High]
- Host: api-testing-prod.fyers.in (NEW host, absent from both public configs — scope-confirm first)
- Routes (POST-only, GET→405; no auth gate observed at any depth):
  - /signup/upload/api/v1/user/general/upload-image   body {file:<b64-dataURL>,fileName,key}
  - /signup/upload/api/v1/user/fetch-pan              body {base64_image,fileName,key}
  - /signup/upload/signature-to-bmp                   body {base64_image,fileName,key:"user/signature/bmp"}
  - /signup/upload/api/v1/pdf/is-password-protected   body {file_url:<url>}  ← SSRF candidate
  - /signup/upload/api/v1/user/general/zip-all-images body {}  → 200 {"status":"Success"} no-auth
- Design (FYERS-side; researcher executes none of the file/URL side-effects):
  (a) SSRF diff-oracle: file_url = http://127.0.0.1:1/x.pdf (refused) vs https://api-t1.fyers.in/
      (in-scope internal) vs "not-a-url" → distinct status/body proves server-side fetch; test
      scheme/host allow-listing.
  (b) Object-write: upload valid tiny image under a controlled `key`, then retrieve WITHOUT
      Access-Token (unauth GET on object URL / zip-all-images) → proves unauth persist.
  (c) signature-to-bmp uses fixed key `user/signature/bmp` → cross-caller object-clobber candidate.
- Fixed = auth gate rejects tokenless write (never observed), SSRF constrained to allow-list, or
  key bound to authenticated session. CVSS 5.3–7.5 (conditional).

### A6 (H21) — nucleus/v1 FIA conversation/drawing object-keyed IDOR  [program Medium, conditional]
- Host: api-t1.fyers.in/nucleus/v1 — auth fingerprint #21 (401 {"code":-1,"message":"Invalid token
  or authentication failed","s":"error"}); route shapes from in-scope fyers.in/web/main.dart.js:
  - GET  /nucleus/v1/history/{cid}                 (object-keyed; GET /history/99999 → -1, not 404)
  - POST /nucleus/v1/chat          body {cid,prompt[,timeframe,layout,chart_id,chart_symbol,...]}
  - POST /nucleus/v1/chat/auto-suggest
  - GET  /nucleus/v1/chat/request-limit
  - POST /nucleus/v1/oldfiachat/history  body {old_fia:true,uuid}
  - POST /nucleus/v1/drawings       body {client_id,comment,flag:true,text:[...]}
  - DELETE /nucleus/v1/drawings/{a}/{b}
  - POST /nucleus/v1/cancel-chat/{cid}/{mid}
  - GET  /nucleus/v1/fia/chart-insights?symbol=&timeframe=   (also option-chain-insights)
- Object keys: {cid} (history/cancel-chat), {a}/{b} (drawings delete), client_id (drawings save)
- Design: own conversation baseline (GET /history/{own_cid}) → GET /history/{foreign_cid} under own
  session; same for drawings delete/cancel-chat key substitution.
- Success: foreign conversation/drawing object returned or deleted. Fixed: 403/404.
- Severity: Medium if cross-account chat-history/drawing read demonstrated. CVSS 5.3–6.5 (conditional).

## B. Informational / hardening bundle (single low submission — no demonstrated customer impact)
- B1 (H10) chart SQL oracle: api-t1 chart data endpoint returns `Number:1103`/SQLState `42000`
  (PostgreSQL error strings) on crafted params. Debug-info disclosure only.
- B2 (H12) invest/admin presence oracle: /invest/admin/v1/sgb/issue-list — `-19` presence oracle,
  `souce` var typo, `-27` auth-domain split. Enumeration without data exposure.
- B3 (H14) realtime-funds error-wrap: api-a1-prod /myaccount/prod/realtime-funds returns HTTP 200
  with `-16` + internal `latency` debug field; funds tier split (-374/-17); withdraw-fund
  validation-before-auth schema oracle. Internal debug-info disclosure.
- B4 (H16) email-preference validation-before-auth: api-t1 /utility/v2/public/email-preference →
  400 "Validation failed..." without token; schema oracle only (email_disabled/sms_disabled/...).
- B5 (H1) login cb/redirect_uri handling: login.fyers.in `cb`/`redirect_uri` nav = open-redirect
  class — NOT bounty-eligible per program rules (no auth bypass demonstrated). Informational only.
- B6 x-validate HMAC key: client-side HMAC key 1YJKSPg9IcPyyb6N9JHXGAfikiVylWCs embedded in
  signup bundle — public client-side key, no protective value (weak anti-CSRF shaping). NOT eligible.
- B7 journal.fyers.in client primitives: unvalidated `?token=` setter → crafted link clears apex
  `_FYERS=-1` across all *.fyers.in (cross-property logout); hardcoded OAuth `state=sample_state`;
  no Referrer-Policy on token deep-links. Informational.
- B8 signup/v1 JSON-parse leak: verification/email/send-otp 400 message echoes
  "Expecting value: line 1 column 1 (char 0)". Informational.

## C. Scope-confirmation questions (to program contact BEFORE any active validation)
- C1 signup/v2 KYC (api-a1.fyers.in/signup/v2/*) — in scope? KYC req_id object model?
- C2 api-testing-prod.fyers.in — in scope? (absent from public configs; KYC upload microservice)
- C3 journal-server (api-a1-prod.fyers.in/journal-server/*) — in scope?
- C4 open-account.fyers.in + signup/v1 (api-a1-prod) + open-account/staging (api.fyers.in) — in scope?
- C5 /invest/admin/* — in scope? (admin-adjacent surface)
- C6 /cdsl/dev/* (api-t1) — dev surface on prod; explicitly excluded as "development environments"?
- C7 mcp.fyers.in — in scope?
- C8 api-connect-docs.fyers.in — informational only; leave out of scope?

## D. Excluded (do not report)
- SmartHunt SSTI (${7*7} → CF 429 errorCode:1015 rate-limit/WAF; no "49" rendered) — false positive,
  4 independent triages. onload/issue_id variants same class.
- Third-party hosts (Grammarly/Coda/Superhuman/Zoho-assist .env) — out of scope by program AND rules.
- api-a1.fydev.tech (dev twin) — non-*.fyers.in, out of scope.
- Public config artifacts (indus/v1/config, config.gz) — public configs.
- OAuth/open-redirect/no-impact categories per program Expected Behaviour list.

## Compliance guardrails (all phases)
No OTP/SMS/email dispatch to any number. No cross-account access by researcher. No high-volume or
automated scanning. No rate-limit bypass. No market-hours trading-system actions. Own-account PoCs
only. Submissions only via official form. Strict confidentiality (no external disclosure).

## E. POC request-format appendix (exact shapes — FYERS-side / own-account validation only)
Reference implementation for the designs in section A. Every request below is a
DOCUMENTED SHAPE, not an executed action. Parameterize all ids/tokens. Run each
baseline on the researcher's OWN session first; substitution step uses the SECOND
researcher-owned account's object id ONLY per A. method. None of these are live.

### E1 (A1/H17 signup/v2 req_id substitution) — base https://api-a1.fyers.in
- Session: signup/v2 authenticated session for applicant A (own account), header `x-validate` is
  client-side HMAC (informational; not required for validation of object scoping).
- Shape 1 POST /signup/v2/user/esign-document
  body {"digio_doc_id":"<APPLICANT_B_REQ_ID>","source":"esign"}   (or the live-verification
  query form: /signup/v2/user/esign-document?source=esign&req_id=<APPLICANT_B_REQ_ID>)
- Shape 2 POST /signup/v2/user/pdf/generate  body {"req_id":"<APPLICANT_B_REQ_ID>",...}
- Shape 3 POST /signup/v2/user/pdf/poll      body {"req_id":"<APPLICANT_B_REQ_ID>",...}
- Shape 4 GET  /signup/v2/user/status/poll?req_id=<APPLICANT_B_REQ_ID>
- Shape 5 GET  /signup/v2/nri/get-document?req_id=<APPLICANT_B_REQ_ID>&doc=<name>
- Baseline: repeat with APPLICANT_A_REQ_ID (own) → expect non-empty eSign/PDF/status payload.
- Success indicator: with B's req_id, A's session returns a non-empty document/PDF/status
  object (any 2xx with data). FIXED: 403/404/401/session-expired or "not found" for B's id.
- Safe: no account created, no OTP dispatched, no eSign action executed; only object-id
  substitution on A's own authenticated session responses.

### E2 (A2/H13 savedcharts object-keyed IDOR) — base https://data.fyers.in
- Shape 1 GET /dev-fyers/savechart/1.2/charts (own chart list; `_FYERS` cookie)
- Shape 2 GET /dev-fyers/savechart/1.2/charts/<OWN_CHART_ID>
- Shape 3 GET /dev-fyers/savechart/1.2/charts/<SECOND_ACCOUNT_CHART_ID>
- Shape 4 DELETE /dev-fyers/savechart/1.2/charts/<SECOND_ACCOUNT_CHART_ID>
- Shape 5 GET /dev-fyers/savechart/1.2/study_templates/<FOREIGN_TEMPLATE_ID>
- Auth: `_FYERS` session cookie (savedcharts.fyers.in login) ONLY. No cross-account login.
- Success: 200-with-data on foreign chart id; FIXED: 403/404/empty.

### E3 (A3/H15 marina/ddpi instruction-object IDOR) — baseline https://api-a1.fyers.in
- Shape 1 GET  /marina/v1/ddpi/user/listing (own instructions)
- Shape 2 GET  /marina/v1/ddpi/status?instruction_id=<OWN>
- Shape 3 GET  /marina/v1/ddpi/status?instruction_id=<SECOND_ACCOUNT_INSTRUCTION_ID>
- Shape 4 GET  /marina/v1/ddpi/timeline?instruction_id=<SECOND_ACCOUNT_INSTRUCTION_ID>
- Success: foreign instruction status/timeline/file returned; FIXED: 403/404/-16-scoped.

### E4 (A4/H18 journal-server note/upload) — base https://api-a1-prod.fyers.in
- Header on all: Authorization: Bearer <OWN_SESSION_TOKEN>
- Shape 1 GET  /journal-server/v1/notes-list?date=<TODAY_ISO>            (own)
- Shape 2 GET  /journal-server/v1/note/detail?note_ids=<OWN_NOTE_ID>&gs=  (own)
- Shape 3 GET  /journal-server/v1/note/detail?note_ids=<SECOND_ACCOUNT_NOTE_ID>&gs=
- Shape 4 GET  /journal-server/v1/note/detail?note_ids=<SECOND_ACCOUNT_DOCID>&gs=(document id)
- Success: B's note/document payload in A's response; FIXED: 403 {"code":401...} / 404.

### E5 (A5/H19 api-testing-prod unauth upload tier) — base https://api-testing-prod.fyers.in
- NOTE: scope-confirm first (C2). No file payload or external URL fetch should be performed by
  the researcher; these are the service-side validation probes.
- Shape 1 POST /signup/upload/api/v1/user/general/upload-image
   body {"file":"<MINIMAL_1x1_PNG_or_set of null>","fileName":"<NAME>","key":"<KEY>"}
- Shape 2 POST /signup/upload/api/v1/user/fetch-pan
   body {"base64_image":"<NULL/EMPTY>","fileName":"<NAME>","key":"<KEY>"}
- Shape 3 POST /signup/upload/signature-to-bmp
   body {"base64_image":"<NULL/EMPTY>","fileName":"<NAME>","key":"user/signature/bmp"}
- Shape 4 POST /signup/upload/api/v1/pdf/is-password-protected
   body {"file_url":"http://127.0.0.1:1/x.pdf"}          (connection-refused oracle)
   body {"file_url":"https://api-t1.fyers.in/"}           (in-scope internal fetch oracle)
   body {"file_url":"not-a-url"}                            (validation oracle)
- Shape 5 POST /signup/upload/api/v1/user/general/zip-all-images (expected 200 no-auth no-op)
- Success indicators: distinct status/body across file_url cases (proves server fetch);
  foreign-object read/zip includes a caller-uploaded object → unauth write. FIXED: token
  rejection / allow-listed hosts / session-bound keys.

### E6 (A6/H21 nucleus/v1 object-keyed IDOR) — base https://api-t1.fyers.in
- Header all: Authorization: Bearer <USER_SESSION_TOKEN>, version: 1.0.0
- Shape 1 GET  /nucleus/v1/history/{OWN_CID}              (baseline)
- Shape 2 GET  /nucleus/v1/history/{SECOND_ACCOUNT_CID}
- Shape 3 DELETE /nucleus/v1/drawings/{A}/{B}  (own drawing key → foreign key substitution)
- Shape 4 POST /nucleus/v1/chat  body {"cid":"<FOREIGN_CID>","prompt":"<EMPTY>||<DOC_QUERY>"}
- Success: foreign conversation/drawing object in response; FIXED: 403/404/empty.

## CVSS (program rubric governs; CVSS reference only)
- A1/H17 8.1–9.1 (Critical/High if unmasked PAN/eSign/PDF) · A5/H19 5.3–7.5 (Medium–High)
- A2/H13, A3/H15, A4/H18 5.3–7.5; A6/H21 5.3–6.5 — all conditional on FYERS-side validation

## F. 2026-08-08 eIPO surface (ipo.fyers.in / api-i1.fyers.in) — NEW host, scope-confirm first
- NEW host `api-i1.fyers.in` (three microservices: /invest/v1/ipo, /investment/jhelum/v1/api, /investment/tapi/v1/eipo) + `ipo.fyers.in` Next.js SPA (routes /home /ipo /orders /details /updateipo).
- C9 scope-confirm: api-i1.fyers.in / ipo.fyers.in eIPO order surface in scope? (host absent from indus/v1/config and config.gz public lists).

### A7 — H22 eIPO order/offer-id object-keyed IDOR (conditional, FYERS-side/own-session only)
- Host: api-i1.fyers.in (own session token; plain `Authorization:` header, NO Bearer — as SPA does).
- Shape 1 POST /investment/tapi/v1/eipo/order-book  body {} (auth-first -100 gate) → with OWN token + own order_id → 200-with-data; substitution with second account's order_id (baseline → delta).
- Shape 2 DELETE /investment/tapi/v1/eipo/cancel-order?order_id=<FOREIGN_ORDER>&offer_id=<FOREIGN>
- Shape 3 GET /investment/jhelum/v1/api/offer_details?offer_type=1&offer_id=<FOREIGN> — verify intentionally-public feed (informational; offer data public by design).
- Success indicator: foreign order/instruction object returned/modified under own session. FIXED: 403/404/-100.
- NOTE: /invest/v1/ipo/place-order is validation-before-auth (schema oracle pre-auth) but the auth gate DOES reject tokenless full payloads (401 "Authorization header is missing") — no order-placement primitive pre-auth; do NOT send a valid order payload without a token (order confusion risk).

### B9 — eIPO informational / hardening
- validation-before-auth Pydantic field-required full-field oracle on /invest/v1/ipo/{place-order (31 fields), order-book (pageNumber,OrderId,IssueId,FormFromId,FormToId)} — schema disclosure only, no data leak, no pre-auth order primitive (auth gate runs after schema, rejects tokenless).
- api-i1 auth fingerprints: -100 "Authorisation token required." (tapi trade ops, auth-first) · -441 "auth code is required" (validate-authcode) · Pydantic-field JSON oracles (/invest) · invest wrapper 401-in-HTTP-200 (investors-details with dummy token → HTTP 200 body {"code":401,...}).
- Public jhelum offer_list/offer_details = live IPO feed (offer_type -1/1/2/3/4; symbol, isin, price bands, offer_id UUID) — public by design, informational.
- ipo.fyers.in OAuth client_id `EFR7964223-101`, redirect_uri https://ipo.fyers.in, prod appIdHash 2a88a14a353274a2f35430038b6d81725e2d17d8064785d62965e4da78033e9f, hardcoded `state=abcdefg` — informational hardening (H1-class open-redirect/state, NOT bounty-eligible per program Expected Behaviour).

### E7 — exact eIPO request shapes (SPA registry + InitialObj; FYERS-side / own-account)
- POST api-t1.fyers.in/api/v3/validate-authcode  body {"grant_type":"authorization_code","appIdHash":"2a88a14a353274a2f35430038b6d81725e2d17d8064785d62965e4da78033e9f","code":"<AUTH_CODE>"} → access_token (own-account only).
- GET  api-i1.fyers.in/invest/v1/ipo/issue-list?IssueId=&IsActive=          (public; uuid-style IssueId → CF 502 upstream-error candidate, FYERS-side confirm)
- GET  api-i1.fyers.in/invest/v1/ipo/issue-details?IssueId=                 (public)
- GET  api-i1.fyers.in/invest/v1/ipo/investors-details                      (token; 401 missing-header / 200-wrap could-not-auth)
- POST api-i1.fyers.in/invest/v1/ipo/place-order  body = InitialObj (36 fields, defaults above) — token; own-account only
- POST api-i1.fyers.in/invest/v1/ipo/order-book  body {"pageNumber":<n>,"OrderId":<OWN>,"IssueId":<OWN>,"FormFromId":<...>,"FormToId":<...>}  (schema oracle pre-auth; substitution delta on OrderId/IssueId)
- GET  api-i1.fyers.in/investment/jhelum/v1/api/offer_list?offer_type=-1|1|2|3|4&is_active=1   (public feed)
- GET  api-i1.fyers.in/investment/jhelum/v1/api/offer_details?offer_type=1&offer_id=<OFFER_UUID>  (public)
- POST api-i1.fyers.in/investment/tapi/v1/eipo/order-book  (auth-first -100; own-session order_id baseline → foreign delta)
- DELETE api-i1.fyers.in/investment/tapi/v1/eipo/cancel-order?order_id=<OWN>&offer_id=<OWN>  (auth-first; substitution delta)

### CVSS addendum (program rubric governs)
- A7/H22 5.3–6.5 conditional (Medium if cross-account eIPO order read demonstrated) · B9 informational.
