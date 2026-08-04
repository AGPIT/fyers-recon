
===== RUN 2026-08-04 21:14:36 UTC =====
Model: mimo | Phase: RECON | Target: NONE

[0m
> build · mimo-v2.5-free
[0m
[0m% [0mWebFetch https://fyers.in
[0m✗ [0mWebFetch https://fyers.in/security failed
[91m[1mError: [0mStatusCode: non 2xx status code (404 GET https://fyers.in/security)
[0m✗ [0mWebFetch https://fyers.in/responsible-disclosure failed
[91m[1mError: [0mStatusCode: non 2xx status code (404 GET https://fyers.in/responsible-disclosure)
I'll begin reconnaissance on fyers.in. Let me gather information about this target.
[0m◈ [0mExa Web Search "fyers.in bug bounty vulnerability disclosure program"
[0m◈ [0mExa Web Search "site:fyers.in subdomains API endpoints"
[0m✗ [0mWebFetch https://api-t1.fyers.in failed
[91m[1mError: [0mStatusCode: non 2xx status code (404 GET https://api-t1.fyers.in)
[0m✗ [0mWebFetch https://api-t2.fyers.in failed
[91m[1mError: [0mStatusCode: non 2xx status code (503 GET https://api-t2.fyers.in)
[0m✗ [0mWebFetch https://api.fyers.in failed
[91m[1mError: [0mStatusCode: non 2xx status code (500 GET https://api.fyers.in)
[0m◈ [0mExa Web Search "fyers.in API documentation v3 endpoints authentication"
[0m◈ [0mExa Web Search "github.com fyers api repository"
[0m✗ [0mWebFetch https://myapi.fyers.in/docsv3 failed
[91m[1mError: [0mStatusCode: non 2xx status code (404 GET https://myapi.fyers.in/docsv3)
[0m% [0mWebFetch https://fyers.in/bug-bounty-program/
[0m✗ [0mWebFetch https://myapi.fyers.in/dashboard/ failed
[91m[1mError: [0mStatusCode: non 2xx status code (404 GET https://myapi.fyers.in/dashboard/)
[0m% [0mWebFetch https://trade.fyers.in
[0m◈ [0mExa Web Search "subdomains of fyers.in trading platform app"
[0m◈ [0mExa Web Search "fyers.in technology stack web application framework"
# Target: fyers.in
# Current Phase: RECON

## Key Findings
- **Bug Bounty Program**: Active program at https://fyers.in/bug-bounty-program/
- **Security Contact**: security@fyers.in
- **Submission**: Via Zoho Form only
- **In-Scope**: FYERS Trading Platform (Web & Mobile), FYERS APIs part of trading platform
- **Out-of-Scope**: Third-party systems, deprecated systems, internal corporate systems
- **Bounty Range**: Critical (₹1,00,000) | High (₹50,000) | Medium (₹20,000) | Low (₹5,000)

## Discovered Subdomains
| Subdomain | Purpose |
|-----------|---------|
| fyers.in | Main marketing site |
| login.fyers.in | Authentication portal |
| trade.fyers.in | Trading platform (Web) |
| api.fyers.in | API endpoint (v2 legacy) |
| api-t1.fyers.in | API v3 endpoint |
| api-t2.fyers.in | Auth/OTP endpoint (vagator) |
| myapi.fyers.in | API dashboard/docs |
| support.fyers.in | Support portal (Zendesk) |
| community.fyers.in | Community forum |

## Tech Stack
- **Frontend**: React, Next.js, Flutter (mobile)
- **UI**: Bootstrap, TradingView integration
- **Build**: Webpack
- **CDN**: Cloudflare
- **Analytics**: Google Analytics 4, GTM
- **Backend**: AWS, Ruby on Rails (partial)
- **SSL**: Google Trust Services (DV), TLS 1.3

## API Endpoints
| Endpoint | Method | Purpose |
|----------|--------|---------|
| /vagator/v2/send_login_otp | POST | Send OTP for login |
| /vagator/v2/verify_otp | POST | Verify OTP code |
| /vagator/v2/verify_pin | POST | Verify PIN |
| /api/v3/token | POST | Generate access token |
| /api/v3/login | POST | Login endpoint |
| /api/v2/validate-authcode | POST | Validate auth code (legacy) |

## GitHub Assets
- **Organization**: FyersDev (9 repos)
- **Repos**: fyers-api-sample-code, fyers-java-sdk, fyers-go-sdk, fyers-c-sdk
- **NPM**: fyers-api-v3, fyers-web-sdk-v3

## Previous Bug Bounty Reports
1. **Refresh Token appIdHash bypass** - SHA256 secret not validated during token refresh
2. **Refresh Token reuse** - Same refresh token usable multiple times in a day
3. **No token revocation API** - No way to invalidate running API tokens

## Security Observations
- **Security Headers Score**: 35/100 (below average)
- **Missing Headers**: CSP, X-Frame-Options, Referrer-Policy, Permissions-Policy
- **Proper Headers**: HSTS, X-Content-Type-Options
- **Login**: Passwordless (QR code, OTP-based)

---

## STATE UPDATE FOR NEXT RUN

STATUS_TARGET: fyers.in
STATUS_PHASE: RECON
STATUS_STATE: IN_PROGRESS

NEXT_STEP_1: Fetch API documentation at myapi.fyers.in/docs
NEXT_STEP_2: Enumerate additional subdomains via crt.sh
NEXT_STEP_3: Analyze trading platform JavaScript for API routes

===== RUN 2026-08-04 22:33:41 UTC =====
Model: mimo | Phase: RECON | Target: fyers.in

[0m
> build · mimo-v2.5-free
[0m
I'll continue the research on fyers.in, moving into Phase 2: SURFACE ANALYSIS. Let me analyze the API documentation, auth mechanisms, and permission models in depth.
[0m✗ [0mWebFetch https://myapi.fyers.in/docsv3 failed
[91m[1mError: [0mStatusCode: non 2xx status code (404 GET https://myapi.fyers.in/docsv3)
[0m% [0mWebFetch https://trade.fyers.in
[0m◈ [0mParallel Web Search "fyers.in API v3 documentation authentication endpoints permissions"
[0m% [0mWebFetch https://api-docs.fyers.in/
[0m% [0mWebFetch https://support.fyers.in/portal/en/kb/fyers-api-integrations/fyers-api/api-v3/general
[0m◈ [0mParallel Web Search "fyers.in API authentication OAuth2 token refresh rate limiting vulnerabilities"
[0m% [0mWebFetch https://github.com/FyersDev/fyers-api-sample-code
[0m% [0mWebFetch https://support.fyers.in/portal/en/kb/articles/how-does-the-authentication-and-login-flow-work-for-user-apps-on-fyers
[0m◈ [0mParallel Web Search ""api.fyers.in" OR "api-t1.fyers.in" OR "api-t2.fyers.in" endpoint parameters authentication"
[0m% [0mWebFetch https://raw.githubusercontent.com/marketcalls/fyers-scanner/master/docs/fyersdashboard%20-%20sample%20app/docs/authentication.md
[0m◈ [0mParallel Web Search "fyers API rate limiting per minute limit endpoints blocked"
[0m% [0mWebFetch https://support.fyers.in/portal/en/kb/articles/what-are-the-consequences-if-i-exceed-the-per-minute-rate-limit-more-than-three-times-in-a-day-20-11-2023
[0m◈ [0mParallel Web Search ""fyers" "api/v3" endpoints orders positions holdings funds profile"
Now I'll compile a comprehensive Phase 2 SURFACE ANALYSIS report based on all the gathered intelligence.

# Target: fyers.in
# Current Phase: SURFACE ANALYSIS

## Key Findings

### Authentication & Authorization Architecture
- **OAuth2 Flow**: Standard authorization_code grant with SHA-256 hashed app credentials
- **Token Format**: JWT-based access tokens (1-day validity) + refresh tokens (15-day validity)
- **Auth Header**: `Authorization: <app_id>:<access_token>` format
- **appIdHash**: SHA-256 of `api_id:app_secret` used for token exchange
- **PIN Required**: Trading PIN required for refresh token operations
- **Passwordless Login**: OTP-based authentication (no traditional passwords)

### API Surface Map

#### Primary Endpoints (api-t1.fyers.in)
| Category | Endpoint | Method | Auth |
|----------|----------|--------|------|
| **User** | `/api/v3/profile` | GET | Bearer |
| | `/api/v3/funds` | GET | Bearer |
| | `/api/v3/holdings` | GET | Bearer |
| | `/api/v3/logout` | POST | Bearer |
| **Orders** | `/api/v3/orders/sync` | POST/PATCH/DELETE | Bearer |
| | `/api/v3/orders/async` | POST/PATCH/DELETE | Bearer |
| | `/api/v3/multi-order/sync` | POST/PATCH/DELETE | Bearer |
| | `/api/v3/multileg/orders/sync` | POST | Bearer |
| **Positions** | `/api/v3/positions` | GET/DELETE | Bearer |
| **GTT** | `/api/v3/gtt/orders/sync` | POST/PATCH/DELETE | Bearer |
| | `/api/v3/gtt/orders` | GET | Bearer |
| **Smart Orders** | `/api/v3/smart-order/{type}` | POST/PATCH/DELETE | Bearer |
| **Reports** | `/api/v3/order-history` | GET | Bearer |
| | `/api/v3/trade-history` | GET | Bearer |
| | `/api/v3/charges-history` | GET | Bearer |
| | `/api/v3/realised-pnl-history` | GET | Bearer |
| | `/api/v3/tax-pnl-history` | GET | Bearer |
| | `/api/v3/ledger-history` | GET | Bearer |

#### Data Endpoints (api-t1.fyers.in/data)
| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/data/history` | GET | Historical candles |
| `/data/quotes` | GET | Real-time quotes (≤50 symbols) |
| `/data/depth` | GET | Market depth (1 symbol) |
| `/data/options-chain-v3` | GET | Option chain data |
| `/data/marketStatus` | GET | Market status |

#### Legacy v2 Endpoints (api.fyers.in)
| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/v2/span_margin` | POST | Span margin calculation |
| `/api/v2/tpin` | GET | EDIS TPIN generation |
| `/api/v2/details` | GET | EDIS details |
| `/api/v2/index` | GET/POST | EDIS index |
| `/api/v2/inquiry` | POST | EDIS inquiry |

#### Authentication Endpoints
| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/v3/generate-authcode` | GET | Generate authorization code |
| `/api/v3/validate-authcode` | POST | Exchange auth code for tokens |
| `/api/v3/token` | POST | Refresh access token |

### Rate Limits (Documented)
| Scope | Limit | Time Window |
|-------|-------|-------------|
| User level | 500 requests | 5 seconds |
| User level | 1500 requests | 1 minute |
| Pseudo/Account level | 60 requests | 5 seconds |
| Pseudo/Account level | 130 requests | 1 minute |
| Pseudo/Account level | 300 requests | 5 minutes |
| Daily | 100,000 requests | Per day |

**Enforcement**: Exceeding per-minute limit 3+ times = blocked for remainder of day

### Order Types & Parameters
```json
{
  "symbol": "NSE:SBIN-EQ",    // Exchange:Symbol-Type
  "qty": 1,                    // Quantity
  "type": 2,                   // 1=Limit, 2=Market, 3=SL-M, 4=SL-L
  "side": 1,                   // 1=Buy, -1=Sell
  "productType": "INTRADAY",   // CNC, INTRADAY, MARGIN, CO, BO, MTF
  "limitPrice": 0,             // For limit orders
  "stopPrice": 0,              // For stop orders
  "validity": "DAY",           // DAY or IOC
  "offlineOrder": false,       // false=normal, true=AMO
  "orderTag": "string"         // Custom tag
}
```

### WebSocket Endpoints
| Endpoint | Purpose | Limits |
|----------|---------|--------|
| `wss://api.fyers.in/socket/v2/data/` | Market data stream | 5000 symbols/connection |
| Order Socket | Order/Position updates | 3 connections/user, 5 symbols/connection |

### Third-Party Integrations (In-Scope)
- **MCP Server**: quantabox/fyers-mcp-server (AI assistant integration)
- **TradingView**: Via API Bridge application
- **Python SDK**: fyers-apiv3 (PyPI)
- **Java SDK**: fyers-api-v3
- **Node.js SDK**: fyers-web-sdk-v3

### Previous Bug Bounty Findings (Reference)
1. **Refresh Token appIdHash bypass** - SHA256 secret not validated during token refresh
2. **Refresh Token reuse** - Same refresh token usable multiple times in a day
3. **No token revocation API** - No way to invalidate running API tokens

### Security Observations
- **Missing Headers**: CSP, X-Frame-Options, Referrer-Policy, Permissions-Policy
- **Proper Headers**: HSTS, X-Content-Type-Options
- **SSL**: Google Trust Services (DV), TLS 1.3
- **CDN**: Cloudflare protection

---

## API Endpoints (Comprehensive)

### Authentication Flow
```
Step 1: GET /api/v3/generate-authcode?client_id={app_id}&redirect_uri={uri}&response_type=code&state={random}
Step 2: User completes OTP login → redirected with auth_code
Step 3: POST /api/v3/validate-authcode
  Body: {"grant_type":"authorization_code","appIdHash":"sha256(api_id:secret)","code":"{auth_code}"}
  Response: {"access_token":"...","refresh_token":"..."}
Step 4: Use access_token in header: Authorization: {app_id}:{access_token}
```

### Token Refresh Flow
```
POST /api/v3/token
Body: {"grant_type":"refresh_token","appIdHash":"sha256(api_id:secret)","refresh_token":"...","pin":"{trading_pin}"}
Response: {"access_token":"...","refresh_token":"..."}
```

---

## Hypotheses (Phase 3 Preview)

Based on the surface analysis, the following attack vectors show high potential:

### 1. IDOR on Order/Position Endpoints
**Reasoning**: Order operations use `id` parameter; if validation is client-side only, cross-account access possible
**Impact**: Read/modify/cancel other users' orders
**Test**: Enumerate order IDs, attempt access with different session tokens

### 2. Refresh Token Reuse/Rotation Bypass
**Reasoning**: Previous finding showed same refresh token usable multiple times; rotation may not be enforced
**Impact**: Persistent access even after "rotation"
**Test**: Use same refresh token multiple times in succession

### 3. appIdHash Bypass on Token Operations
**Reasoning**: Previous finding showed SHA256 secret not validated; may still apply to certain flows
**Impact**: Generate tokens without knowing app secret
**Test**: Send requests with invalid/empty appIdHash

### 4. Rate Limit Bypass via IP Rotation
**Reasoning**: Rate limits appear IP-based; rotating IPs may bypass daily blocks
**Impact**: Continue trading after being blocked
**Test**: Make requests from different source IPs

### 5. WebSocket Authentication Bypass
**Reasoning**: WebSocket endpoints may have weaker auth validation than REST
**Impact**: Subscribe to other users' data streams
**Test**: Connect with stolen/invalid tokens

### 6. Cross-Site WebSocket Hijacking
**Reasoning**: If WebSocket doesn't validate Origin header properly
**Impact**: Execute trades from malicious page
**Test**: Craft page that connects to WebSocket endpoint

---

## CVSS Candidates

| Hypothesis | CVSS Estimate | Rationale |
|------------|---------------|-----------|
| IDOR on Orders | 8.1 (High) | AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:N |
| Refresh Token Bypass | 7.5 (High) | AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N |
| appIdHash Bypass | 9.1 (Critical) | AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N |
| Rate Limit Bypass | 5.3 (Medium) | AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:L/A:L |
| WebSocket Hijack | 6.5 (Medium) | AV:N/AC:L/PR:N/UI:R/S:U/C:H/I:N/A:N |

---

## STATE UPDATE FOR NEXT RUN

STATUS_TARGET: fyers.in
STATUS_PHASE: HYPOTHESIS
STATUS_STATE: HIGH_POTENTIAL

NEXT_STEP_1: Design detailed PoC for IDOR on order endpoints
NEXT_STEP_2: Test appIdHash validation on refresh flow
NEXT_STEP_3: Enumerate WebSocket authentication mechanism
NEXT_STEP_4: Check for token leakage in browser storage/localStorage
