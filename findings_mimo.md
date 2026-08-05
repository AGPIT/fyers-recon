
# 6 items on 2026-08-04 21:14:36 UTC
- **Bug Bounty Program**: Active program at https://fyers.in/bug-bounty-program/
- **Security Contact**: security@fyers.in
- **Submission**: Via Zoho Form only
- **In-Scope**: FYERS Trading Platform (Web & Mobile), FYERS APIs part of trading platform
- **Out-of-Scope**: Third-party systems, deprecated systems, internal corporate systems
- **Bounty Range**: Critical (₹1,00,000) | High (₹50,000) | Medium (₹20,000) | Low (₹5,000)

# 5 Hypotheses Generated on 2026-08-04 23:45:12 UTC
1. **IDOR on Order Endpoints** (CVSS 8.1) - Sequential order IDs enable cross-account access
2. **Refresh Token Race Condition** (CVSS 7.5) - Concurrent refresh requests fork token families
3. **WebSocket CSWSH** (CVSS 6.5) - Missing Origin validation enables cross-site hijacking
4. **appIdHash Bypass** (CVSS 9.1) - SHA256 validation bypass allows unauthorized token generation
5. **Rate Limit Bypass** (CVSS 5.3) - Header manipulation circumvents IP-based restrictions

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
+1. **IDOR on Order Endpoints** (CVSS 8.1) - Sequential order IDs enable cross-account access
+2. **Refresh Token Race Condition** (CVSS 7.5) - Concurrent refresh requests fork token families
+3. **WebSocket CSWSH** (CVSS 6.5) - Missing Origin validation enables cross-site hijacking

# 5 Additional Hypotheses Generated on 2026-08-05 01:45:00 UTC (New Surface)
6. **Webhook Spoofing (Missing HMAC)** (CVSS 8.1) - No signature validation enables forged order events
7. **API Connect SDK XSS** (CVSS 6.1) - Dynamic parameters allow token theft via XSS
8. **Pre-prod Environment Bypass** (CVSS 6.5) - Weaker security controls in sandbox environment
9. **Webhook Secret Leakage** (CVSS 7.5) - Secret embedded in client-side JavaScript code
10. **Status Page Disclosure** (CVSS 5.3) - Internal infrastructure details exposed

NEW ATTACK SURFACE IDENTIFIED (model: mimo)
Review research_mimo.md for details
+6. **Webhook Spoofing (Missing HMAC)** (CVSS 8.1) - No signature validation enables forged order events
+9. **Webhook Secret Leakage** (CVSS 7.5) - Secret embedded in client-side JavaScript code

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
**CVSS**: 7.5 (High) - AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N
**CVSS**: 6.5 (Medium) - AV:N/AC:L/PR:N/UI:R/S:U/C:H/I:N/A:N
**CVSS**: 6.1 (Medium) - AV:N/AC:L/PR:R/UI:R/S:C/C:L/I:L/A:N

# 4 MCP Hypotheses Generated on 2026-08-05 10:04:38 UTC (MCP Integration)
11. **MCP Session Hijacking via SSE** (CVSS 7.5) - Session ID not bound to authenticated principal
12. **MCP Token Passthrough** (CVSS 6.5) - User token forwarded without audience validation
13. **MCP Tool Description Injection** (CVSS 6.1) - XSS via malicious tool descriptions
14. **MCP OAuth Token Theft** (CVSS 9.1) - Redirect URI manipulation steals auth codes

NEW ATTACK SURFACE IDENTIFIED (model: mimo)
Review research_mimo.md for details
+11. **MCP Session Hijacking via SSE** (CVSS 7.5) - Session ID not bound to authenticated principal
+14. **MCP OAuth Token Theft** (CVSS 9.1) - Redirect URI manipulation steals auth codes

# 4 EDIS Hypotheses Generated on 2026-08-05 12:30:00 UTC (EDIS/TPIN System)
15. **EDIS API Authorization Bypass** (CVSS 9.1) - Missing auth on EDIS endpoints
16. **CDSL Redirect URL Manipulation** (CVSS 7.5) - Phishing via manipulated redirect
17. **ISIN Enumeration** (CVSS 5.3) - Information disclosure via EDIS inquiry
18. **WebSocket EDIS Leakage** (CVSS 5.3) - EDIS data exposed via WebSocket

# 5 Signup/Registration Hypotheses Generated on 2026-08-05 12:30:00 UTC (Vagator OTP API)
19. **OTP Brute Force** (CVSS 7.5) - No rate limiting on verify_otp endpoint
20. **PIN Brute Force** (CVSS 8.1) - No lockout on verify_pin endpoint
21. **fy_id Enumeration** (CVSS 5.3) - User enumeration via send_login_otp response
22. **Request Key Replay** (CVSS 6.5) - Reuse of expired request_keys
23. **ReCAPTCHA Bypass** (CVSS 6.5) - Empty recaptcha_token accepted

TOTAL HYPOTHESIES: 23 across 8 attack surfaces

# 1 items on 2026-08-05 14:42:22 UTC
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
+| # | Hypothesis | CVSS | Priority |
+| Priority | Hypothesis | CVSS | Surface |
 **CVSS**: 7.5 (High) - AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
| `/verify_otp` | Very Low (prevent brute force) | Unknown | CRITICAL |
| `/verify_pin_v2` | Low (prevent PIN brute force) | Unknown | CRITICAL |
| # | Original Hypothesis | Refined Assessment | CVSS |

# 1 Hypothesis Generated on 2026-08-05 16:45:00 UTC (Vagator API - Refined)
24. **Multi-Factor Authentication Bypass** (CVSS 8.1) - Combined OTP+PIN brute force chain with no rate limiting

# 4 Fund Transfer Hypotheses Generated on 2026-08-05 17:00:00 UTC (Fund Transfer System)
25. **CSRF on Withdrawal Endpoint** (CVSS 8.1) - Missing CSRF token on POST withdrawal endpoint
26. **IDOR on Bank Details** (CVSS 7.5) - Sequential bank detail IDs enable cross-account access
27. **Race Condition on Instant Withdrawal** (CVSS 7.5) - Concurrent requests bypass withdrawal limits
28. **Session Fixation on Fund Transfer** (CVSS 6.5) - Session not regenerated after authentication

TOTAL HYPOTHESIES: 28 across 9 attack surfaces

# 4 Verified P&L/Account Hypotheses Generated on 2026-08-05 20:00:00 UTC (Verified P&L System)
29. **Verified P&L IDOR via UUID Enumeration** (CVSS 5.3) - Sequential/predictable UUIDs expose P&L data
30. **Verified P&L API Endpoint IDOR** (CVSS 7.5) - API accepts user IDs bypassing UUID protection
31. **Account Management CSRF** (CVSS 8.1) - Missing CSRF on profile modification endpoints
32. **Tax P&L API IDOR** (CVSS 7.5) - Tax P&L endpoint lacks proper authorization

TOTAL HYPOTHESIES: 32 across 10 attack surfaces

# 4 API Connect/Account Mgmt Hypotheses Generated on 2026-08-05 22:00:00 UTC (API Connect & Partners)
33. **API Connect postMessage Injection** (CVSS 8.1) - Missing origin validation on postMessage handler
34. **API Connect SDK Key Theft via XSS** (CVSS 7.5) - API key exposed in SDK initialization
35. **Partners Widget Notification Spoofing** (CVSS 6.5) - Unauthenticated notification data fetch
36. **Staging OAuth Client ID Disclosure** (CVSS 3.1) - Commented staging client ID in HTML source

TOTAL HYPOTHESIES: 36 across 11 attack surfaces

# HYPOTHESIS Refinement Completed on 2026-08-05 22:30:00 UTC (API Connect & Partners)
- **H33**: API Connect postMessage Injection - Evidence confirmed in SDK source (CVSS 8.1)
- **H34**: SDK Key Theft via XSS - API key exposed in demo page (CVSS 7.5)
- **H35**: Partners Widget Notification Spoofing - Public endpoint confirmed (CVSS 6.5)
- **H36**: Staging OAuth Client ID Disclosure - Visible in HTML comments (CVSS 3.1)
- **H14**: MCP OAuth Token Theft - Refined with MCP documentation (CVSS 9.1)

# HYPOTHESIS Refinement Completed on 2026-08-05 21:00:00 UTC (Verified P&L System)
- **H29**: UUID Leakage via public sharing and search engine indexing (CVSS 5.3)
- **H30**: Verified P&L API IDOR via user_id parameter (CVSS 7.5)
- **H31**: Account Management CSRF on profile modification endpoints (CVSS 8.1)
- **H32**: Tax P&L API IDOR via fyers_id parameter (CVSS 7.5)

# POC Design Completed on 2026-08-05 21:30:00 UTC (Verified P&L System)
- **POC_VERIFIEDPNL.md**: Created with read-only testing methodology for H29-H32
- **Coverage**: UUID Leakage, API IDOR, CSRF, Tax P&L IDOR
- **Status**: POC design complete, ready for authorized testing

# POC Design Completed on 2026-08-05 18:30:00 UTC (Fund Transfer System)
- **POC_FUNDTRANSFER.md**: Created with read-only testing methodology for H25-H28
- **Coverage**: CSRF, IDOR, Race Condition, Session Fixation
- **Status**: POC design complete, ready for authorized testing

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
| # | Hypothesis | CVSS | Likelihood | Evidence |
### CVSS 3.1 Calculation
| Priority | Hypothesis | CVSS | Surface |

# 4 items on 2026-08-05 19:07:50 UTC
- **Next Phase**: SURFACE analysis on Fund Transfer endpoints
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md

## High-Value Hypotheses
- IDOR Testing (H26)
- IDOR Testing (H26)

# 13 items on 2026-08-05 19:58:03 UTC
- /home/runner/work/fyers-recon/fyers-recon/research_mimo.md
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md
- **POC_FUNDTRANSFER.md**: Created with read-only testing methodology for H25-H28
- **Coverage**: CSRF, IDOR, Race Condition, Session Fixation
- **Status**: POC design complete, ready for authorized testing
- /home/runner/work/fyers-recon/fyers-recon/research_mimo.md
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md
- **H30**: Verified P&L API IDOR via user_id parameter (CVSS 7.5)
- **H31**: Account Management CSRF on profile modification endpoints (CVSS 8.1)
- **H32**: Tax P&L API IDOR via fyers_id parameter (CVSS 7.5)
- **POC_FUNDTRANSFER.md**: Created with read-only testing methodology for H25-H28
- **Coverage**: CSRF, IDOR, Race Condition, Session Fixation
- **Status**: POC design complete, ready for authorized testing

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
 28. **Session Fixation on Fund Transfer** (CVSS 6.5) - Session not regenerated after authentication
+29. **Verified P&L IDOR via UUID Enumeration** (CVSS 5.3) - Sequential/predictable UUIDs expose P&L data
+30. **Verified P&L API Endpoint IDOR** (CVSS 7.5) - API accepts user IDs bypassing UUID protection

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
 32. **Tax P&L API IDOR** (CVSS 7.5) - Tax P&L endpoint lacks proper authorization
+33. **API Connect postMessage Injection** (CVSS 8.1) - Missing origin validation on postMessage handler
+34. **API Connect SDK Key Theft via XSS** (CVSS 7.5) - API key exposed in SDK initialization

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
 36. **Staging OAuth Client ID Disclosure** (CVSS 3.1) - Commented staging client ID in HTML source
+- **H33**: API Connect postMessage Injection - Evidence confirmed in SDK source (CVSS 8.1)
+- **H34**: SDK Key Theft via XSS - API key exposed in demo page (CVSS 7.5)
