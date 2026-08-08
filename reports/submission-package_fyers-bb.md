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
