
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
