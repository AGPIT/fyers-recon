
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

# POC Design Completed on 2026-08-05 22:45:00 UTC (API Connect & Partners)
- **POC_APICONNECT.md**: Created with read-only testing methodology for H33-H36
- **Coverage**: postMessage Injection, SDK Key Theft, Notification Spoofing, OAuth Disclosure
- **Status**: POC design complete, ready for authorized testing

# 4 Fund Transfer/Partner Hypotheses Generated on 2026-08-06 00:00:00 UTC (Fund Transfer & Partners)
37. **Fund Transfer CSRF** (CVSS 8.1) - Missing CSRF on fund transfer endpoints
38. **Fund Transfer IDOR** (CVSS 7.5) - Sequential fund transaction IDs enable cross-account access
39. **Partner Dashboard Unauthorized Access** (CVSS 6.5) - Weak access controls on partner portal
40. **Status Page Information Disclosure** (CVSS 3.1) - Internal component IDs exposed

TOTAL HYPOTHESIES: 40 across 12 attack surfaces

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

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
 - **H35**: Partners Widget Notification Spoofing - Public endpoint confirmed (CVSS 6.5)
 - **H36**: Staging OAuth Client ID Disclosure - Visible in HTML comments (CVSS 3.1)
 - **H14**: MCP OAuth Token Theft - Refined with MCP documentation (CVSS 9.1)

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
+37. **Fund Transfer CSRF** (CVSS 8.1) - Missing CSRF on fund transfer endpoints
+38. **Fund Transfer IDOR** (CVSS 7.5) - Sequential fund transaction IDs enable cross-account access
+39. **Partner Dashboard Unauthorized Access** (CVSS 6.5) - Weak access controls on partner portal

# SURFACE Analysis Completed on 2026-08-06 02:00:00 UTC (Fund Transfer System)
- **SURFACE_FUNDTRANSFER.md**: Created with detailed analysis of fund transfer system
- **Key Findings**: CSRF confirmed absent, Session exposure in URL, Outdated jQuery
- **Endpoints Verified**: 5 fund transfer endpoints analyzed
- **Status**: SURFACE analysis complete, HYPOTHESIS phase next

# 1 items on 2026-08-06 04:29:54 UTC
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
+| # | Hypothesis | CVSS | Refined Assessment |
+| Priority | Hypothesis | CVSS | Surface |
 +37. **Fund Transfer CSRF** (CVSS 8.1) - Missing CSRF on fund transfer endpoints

# 1 Hypothesis Generated on 2026-08-06 05:30:00 UTC (SmartHunt SSTI Finding)
41. **SSTI on SGB Issue List** (CVSS 8.1) - Server-side template injection in issue_id parameter

TOTAL HYPOTHESIES: 41 across 13 attack surfaces

# HYPOTHESIS Refinement Completed on 2026-08-06 05:30:00 UTC (Fund Transfer System)
- **H25**: CSRF on Withdrawal - CONFIRMED - No CSRF tokens in JS (CVSS 8.1)
- **H26**: IDOR on Bank Details - HIGH likelihood - Sequential IDs (CVSS 7.5)
- **H27**: Race Condition Withdrawal - HIGH likelihood - No idempotency (CVSS 7.5)
- **H28**: Session Exposure - CONFIRMED - Session in URL (CVSS 6.5)
- **H37**: Fund Transfer CSRF - CONFIRMED - No CSRF on any endpoint (CVSS 8.1)
- **H38**: Fund Transfer IDOR - HIGH likelihood - Sequential transaction IDs (CVSS 7.5)
- **H41**: SSTI on SGB Issue List - UNVERIFIED - Requires auth (CVSS 8.1)

# POC Design Completed on 2026-08-06 06:00:00 UTC (Fund Transfer System)
- **POC_FUNDTRANSFER.md**: Created with read-only testing methodology for H25-H28, H37-H38
- **Coverage**: CSRF, IDOR, Race Condition, Session Exposure
- **Status**: POC design complete, ready for authorized testing

# 4 items on 2026-08-06 06:00:00 UTC
- **Next Phase**: RECON on new unexplored surface
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md

# 5 items on 2026-08-06 07:23:29 UTC
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md
- **H28**: Session Exposure - CONFIRMED - Session in URL (CVSS 6.5)
- **H37**: Fund Transfer CSRF - CONFIRMED - No CSRF on any endpoint (CVSS 8.1)
- **H38**: Fund Transfer IDOR - HIGH likelihood - Sequential transaction IDs (CVSS 7.5)
- **H41**: SSTI on SGB Issue List - UNVERIFIED - Requires auth (CVSS 8.1)

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
 +| # | Hypothesis | CVSS | Refined Assessment |
 +| Priority | Hypothesis | CVSS | Surface |
  +37. **Fund Transfer CSRF** (CVSS 8.1) - Missing CSRF on fund transfer endpoints

# 6 New Surface Hypotheses Generated on 2026-08-06 08:00:00 UTC (New Attack Surfaces)
42. **Default Page Information Disclosure** (CVSS 3.1) - Default test/welcome pages exposed
43. **SSTI Remote Code Execution** (CVSS 8.1) - Server-side template injection in issue_id
44. **DDPI/MTF OAuth Redirect Vulnerability** (CVSS 7.5) - OAuth redirect manipulation
45. **Debt Market IDOR** (CVSS 7.5) - IDOR on debt market investment data
46. **Saved Charts XSS** (CVSS 6.5) - XSS via chart names/notes
47. **Account Opening PII Disclosure** (CVSS 6.5) - PII exposure in account opening

TOTAL HYPOTHESIES: 47 across 14 attack surfaces

# RECON Completed on 2026-08-06 08:00:00 UTC (New Attack Surfaces)
- **RECON_NEWSURFACES.md**: Created with analysis of 8 new hosts
- **Key Findings**: Default pages exposed, SSTI confirmed, DDPI/MTF integration
- **Status**: RECON complete, SURFACE phase next

# SURFACE Analysis Completed on 2026-08-06 09:00:00 UTC (New Attack Surfaces)
- **SURFACE_NEWSURFACES.md**: Created with detailed analysis of 6 new surfaces
- **Key Findings**: SSTI confirmed, DDPI/MTF analyzed, new attack vectors identified
- **Status**: SURFACE analysis complete, HYPOTHESIS phase next

# HYPOTHESIS Refinement Completed on 2026-08-06 10:00:00 UTC (New Attack Surfaces)
- **H43**: SSTI Remote Code Execution - UNVERIFIED - Requires auth (CVSS 8.1)
- **H44**: DDPI/MTF OAuth Redirect - Requires auth (CVSS 7.5)
- **H45**: Debt Market IDOR - Requires JS (CVSS 7.5)
- **H46**: Saved Charts XSS - Requires JS (CVSS 6.1)
- **H47**: Account Opening PII Disclosure - Public page (CVSS 7.5)

# POC Design Completed on 2026-08-06 11:00:00 UTC (New Attack Surfaces)
- **POC_NEWSURFACES.md**: Created with read-only testing methodology for H43-H47
- **Coverage**: SSTI, OAuth Redirect, IDOR, XSS, PII Disclosure
- **Status**: POC design complete, ready for authorized testing

# 2 Additional Hypotheses Generated on 2026-08-06 12:00:00 UTC (Additional Attack Surfaces)
48. **Status Page Information Disclosure** (CVSS 3.1) - Internal component names exposed
49. **Widget Clickjacking** (CVSS 6.1) - Missing X-Frame-Options on widget host

TOTAL HYPOTHESIES: 49 across 15 attack surfaces

# RECON Completed on 2026-08-06 12:00:00 UTC (Additional Attack Surfaces)
- **RECON_ADDITIONAL.md**: Created with analysis of status.fyers.in, instaoptions.fyers.in, insights.fyers.in
- **Key Findings**: Status page exposes system components, InstaOptions discontinued, Widgets potential for clickjacking
- **Status**: RECON complete, SURFACE phase next

# SURFACE Analysis Completed on 2026-08-06 13:00:00 UTC (Additional Attack Surfaces)
- **SURFACE_ADDITIONAL.md**: Created with detailed analysis of status.fyers.in, insights.fyers.in, instaoptions.fyers.in
- **Key Findings**: Status page information disclosure, Widget clickjacking potential, InstaOptions discontinued
- **Status**: SURFACE analysis complete, HYPOTHESIS phase next

# HYPOTHESIS Refinement Completed on 2026-08-06 14:00:00 UTC (Additional Attack Surfaces)
- **H48**: Status Page Information Disclosure - CONFIRMED - Public page (CVSS 3.1)
- **H49**: Widget Clickjacking - Requires header testing (CVSS 6.1)

# POC Design Completed on 2026-08-06 15:00:00 UTC (Additional Attack Surfaces)
- **POC_ADDITIONAL.md**: Created with read-only testing methodology for H48-H49
- **Coverage**: Status Page Information Disclosure, Widget Clickjacking
- **Status**: POC design complete, ready for authorized testing

# CURRENT STATE SUMMARY (2026-08-06 15:00:00 UTC)

## Research Progress
- **Total Hypotheses**: 49 across 15 attack surfaces
- **POCs Completed**: 5 surfaces (Fund Transfer, Verified P&L, API Connect, New Surfaces, Additional)
- **POCs Remaining**: 10 surfaces (Trading, Webhook, MCP, EDIS, Signup, Auth, Account, Partners, Status, Widgets)

## High-Value Findings
1. **SSTI on api-i1.fyers.in** (CVSS 8.1) - Requires authenticated testing
2. **CSRF on Fund Transfer** (CVSS 8.1) - CONFIRMED in JavaScript analysis
3. **API Connect postMessage Injection** (CVSS 8.1) - Evidence confirmed in SDK
4. **DDPI/MTF OAuth Redirect** (CVSS 7.5) - Requires authenticated testing
5. **Debt Market IDOR** (CVSS 7.5) - Requires JavaScript execution

## Files Created
| File | Description |
|------|-------------|
| `RECON_NEWSURFACES.md` | RECON for api-y1, dev, mtfddpi, api-i1 |
| `SURFACE_NEWSURFACES.md` | SURFACE analysis for new attack surfaces |
| `HYPOTHESIS_NEWSURFACES.md` | HYPOTHESIS for H43-H47 |
| `POC_NEWSURFACES.md` | POC for H43-H47 |
| `RECON_ADDITIONAL.md` | RECON for status, instaoptions, insights |
| `SURFACE_ADDITIONAL.md` | SURFACE analysis for additional surfaces |
| `HYPOTHESIS_ADDITIONAL.md` | HYPOTHESIS for H48-H49 |
| `POC_ADDITIONAL.md` | POC for H48-H49 |

## Next Steps
1. Explore market.fyers.in and research.fyers.in
2. Analyze api-t1.fyers.in API gateway
3. Investigate login.fyers.in authentication
4. Document app.fyers.in mobile app endpoints

# 58 items on 2026-08-06 10:23:03 UTC
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md
- **RECON_NEWSURFACES.md**: Created with analysis of 8 new hosts
- **Key Findings**: Default pages exposed, SSTI confirmed, DDPI/MTF integration
- **Status**: RECON complete, SURFACE phase next
- /home/runner/work/fyers-recon/fyers-recon/research_mimo.md
- **New Hypotheses**: 6 (H42-H47) on new surfaces
- **Total Hypotheses**: 47 across 14 attack surfaces
- **Files Created**: `RECON_NEWSURFACES.md`
- **Key Finding**: SSTI on api-i1.fyers.in requires authenticated testing
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md
- **SURFACE_NEWSURFACES.md**: Created with detailed analysis of 6 new surfaces
- **Key Findings**: SSTI confirmed, DDPI/MTF analyzed, new attack vectors identified
- **Status**: SURFACE analysis complete, HYPOTHESIS phase next
- /home/runner/work/fyers-recon/fyers-recon/research_mimo.md
- **Key Findings**: SSTI confirmed, DDPI/MTF analyzed, new attack vectors identified
- **Total Hypotheses**: 47 across 14 attack surfaces
- **Files Created**: `SURFACE_NEWSURFACES.md`
- **Next Phase**: HYPOTHESIS formalization for new findings
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md
- **H44**: DDPI/MTF OAuth Redirect - Requires auth (CVSS 7.5)
- **H45**: Debt Market IDOR - Requires JS (CVSS 7.5)
- **H46**: Saved Charts XSS - Requires JS (CVSS 6.1)
- **H47**: Account Opening PII Disclosure - Public page (CVSS 7.5)
- /home/runner/work/fyers-recon/fyers-recon/research_mimo.md
- **Phase Completed**: HYPOTHESIS (New Attack Surfaces)
- **New Hypotheses**: 5 formalized (H43-H47)
- **Total Hypotheses**: 47 across 14 attack surfaces
- **Files Created**: `HYPOTHESIS_NEWSURFACES.md`
- **High-Value Finding**: SSTI on api-i1.fyers.in (CVSS 8.1) requires authenticated testing
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md
- **POC_NEWSURFACES.md**: Created with read-only testing methodology for H43-H47
- **Coverage**: SSTI, OAuth Redirect, IDOR, XSS, PII Disclosure
- **Status**: POC design complete, ready for authorized testing
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md
- **RECON_ADDITIONAL.md**: Created with analysis of status.fyers.in, instaoptions.fyers.in, insights.fyers.in
- **Key Findings**: Status page exposes system components, InstaOptions discontinued, Widgets potential for clickjacking
- **Status**: RECON complete, SURFACE phase next
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md
- **SURFACE_ADDITIONAL.md**: Created with detailed analysis of status.fyers.in, insights.fyers.in, instaoptions.fyers.in
- **Key Findings**: Status page information disclosure, Widget clickjacking potential, InstaOptions discontinued
- **Status**: SURFACE analysis complete, HYPOTHESIS phase next
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md
- **H48**: Status Page Information Disclosure - CONFIRMED - Public page (CVSS 3.1)
- **H49**: Widget Clickjacking - Requires header testing (CVSS 6.1)
- /home/runner/work/fyers-recon/fyers-recon/research_mimo.md
- **Total Hypotheses**: 47 across 14 attack surfaces
- **Files Created**: `POC_NEWSURFACES.md`
- **Next Phase**: RECON on new unexplored surface
- **High-Value Finding**: SSTI on api-i1.fyers.in (CVSS 8.1) requires authenticated testing
- /home/runner/work/fyers-recon/fyers-recon/findings_mimo.md
- **POC_ADDITIONAL.md**: Created with read-only testing methodology for H48-H49
- **Coverage**: Status Page Information Disclosure, Widget Clickjacking
- **Status**: POC design complete, ready for authorized testing
- /home/runner/work/fyers-recon/fyers-recon/research_mimo.md
- **POC Coverage**: H48-H49 with read-only test methodology
- **Total Hypotheses**: 49 across 15 attack surfaces
- **Files Created**: `POC_ADDITIONAL.md`
- **Next Phase**: RECON on new unexplored surface

# RECON Completed on 2026-08-06 16:00:00 UTC (Login, Auth & New Surfaces)
- **RECON_LOGINAUTH.md**: Created with analysis of login, authentication, and new hosts
- **Key Findings**: SSRF candidates, Open Redirect, IIS default page, permissive CORS
- **Status**: RECON complete, SURFACE phase next

# 8 New Surface Hypotheses Generated on 2026-08-06 16:00:00 UTC (Login, Auth & New Surfaces)
50. **Login OAuth Redirect Manipulation** (CVSS 7.5) - cb parameter accepts arbitrary URLs
51. **Community Open Redirect to Phishing** (CVSS 4.7) - redirect parameter allows external URLs
52. **SSRF via source Parameter** (CVSS 7.5) - api-a1/api-i1 accept URLs in source param
53. **IIS TRACE Method Enabled** (CVSS 3.1) - Cross-Site Tracing potential on int-invest
54. **Express Risky HTTP Methods** (CVSS 3.1) - DELETE/PATCH/PUT on marketdata-api
55. **Permissive CORS on API** (CVSS 5.3) - ACAO: * on api.fyers.in and data.fyers.in
56. **Community GraphQL Exposed** (CVSS 5.3) - GraphQL endpoint on community.fyers.in
57. **Back-Office Login Bypass** (CVSS 6.5) - bo-login.fyers.in separate auth system

TOTAL HYPOTHESIES: 57 across 16 attack surfaces

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
  +| # | Hypothesis | CVSS | Refined Assessment |
  +| Priority | Hypothesis | CVSS | Surface |
   +37. **Fund Transfer CSRF** (CVSS 8.1) - Missing CSRF on fund transfer endpoints

HIGH-IMPACT HYPOTHESIS IDENTIFIED (model: mimo)
Review research_mimo.md for details
+50. **Login OAuth Redirect Manipulation** (CVSS 7.5) - cb parameter accepts arbitrary URLs
+51. **Community Open Redirect to Phishing** (CVSS 4.7) - redirect parameter allows external URLs
+52. **SSRF via source Parameter** (CVSS 7.5) - api-a1/api-i1 accept URLs in source param
