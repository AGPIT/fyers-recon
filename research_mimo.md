
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
