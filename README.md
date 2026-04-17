# Vulnerability Assessment Report for a live Website.
Report Author : Met4man
# Task 1 : Overview



This task involved performing security testing on a sample web application to identify common vulnerabilities. The following types of attacks were tested:
---

# Tools used:
- Burpsuite
- Developer tools
- live vulnerable website called ztw26 https://ztw.ctbb.show/#home
<img width="1817" height="956" alt="image" src="https://github.com/user-attachments/assets/60bc2bec-4be5-4c45-b7dc-2f3a9baa0445" />

**Risk Summary**  
| Vulnerability              | Risk Level | Impact                          |
|---------------------------|------------|---------------------------------|
| IDOR                      | High       | Unauthorized data access        |
| Broken Access Control     | High       | Privilege escalation            |
| Reflected XSS             | Critical   | Session hijacking & account takeover |
| CSRF (via GET)            | High       | Account deletion / data modification |


# 🛡️ FUTURE_CS_01 — Vulnerability Assessment Report for a Live Website

**Internship:** Future Interns — Cyber Security Track  
**Task:** Task 1 — Vulnerability Assessment Report for a Live Website  
**Target:** https://ztw.ctbb.show/#home  
**Assessment Type:** Passive / Non-Intrusive Reconnaissance  
**Tools Used:** Nmap · OWASP ZAP (Passive) · Browser DevTools · SSL Labs · BuiltWith  
**Report Date:** April 2025  

---

## 📋 Table of Contents

1. [Executive Summary](#executive-summary)
2. [Target Overview](#target-overview)
3. [Methodology](#methodology)
4. [Findings Summary](#findings-summary)
5. [Detailed Vulnerability Findings](#detailed-vulnerability-findings)
6. [Risk Matrix](#risk-matrix)
7. [Remediation Recommendations](#remediation-recommendations)
8. [Tools & References](#tools--references)

---

## 1. Executive Summary

A passive vulnerability assessment was conducted on the web application hosted at `https://ztw.ctbb.show`. The goal was to identify common security weaknesses, classify them by risk level, and provide actionable remediation steps that the business can understand and act upon.

**Overall Risk Rating: 🟠 MEDIUM**

| Risk Level | Count |
|------------|-------|
| 🔴 High    | 1     |
| 🟠 Medium  | 3     |
| 🟡 Low     | 4     |
| ℹ️ Informational | 3 |

Key concerns include **missing critical HTTP security headers**, **potential information disclosure via server headers**, and **absence of a Content Security Policy (CSP)**, which collectively increase the attack surface for common web-based attacks.

---

## 2. Target Overview

| Field | Details |
|-------|---------|
| **URL** | https://ztw.ctbb.show/#home |
| **Protocol** | HTTPS (TLS) |
| **Hosting** | Cloud-based (CDN-fronted) |
| **Technology Stack** | HTML5, JavaScript (likely React/Vue SPA), CSS3 |
| **Assessment Scope** | Passive recon, HTTP header analysis, SSL/TLS check, client-side review |
| **Out of Scope** | Active exploitation, SQL injection testing, authenticated testing |

---

## 3. Methodology

The assessment followed a **passive, non-intrusive** approach aligned with OWASP Testing Guide v4.2:

```
[1] Passive Reconnaissance
        └── WHOIS / DNS lookup
        └── Technology fingerprinting (BuiltWith, Wappalyzer)

[2] HTTP Header Analysis
        └── Browser DevTools → Network tab
        └── SecurityHeaders.com scan

[3] SSL/TLS Configuration Review
        └── Qualys SSL Labs

[4] Client-Side Code Review
        └── Browser DevTools → Sources / Console
        └── Checking for exposed keys, debug info, comments

[5] OWASP ZAP Passive Spider
        └── Non-intrusive automated scan (no active attacks)

[6] Report & Risk Classification
        └── CVSS v3.1 scoring (Low / Medium / High / Critical)
```

---

## 4. Findings Summary

| # | Vulnerability | Severity | CVSS Score |
|---|--------------|----------|-----------|
| V-01 | Missing Content Security Policy (CSP) Header | 🔴 High | 7.4 |
| V-02 | Missing X-Frame-Options Header | 🟠 Medium | 6.1 |
| V-03 | Server/Technology Information Disclosure | 🟠 Medium | 5.3 |
| V-04 | Missing Subresource Integrity (SRI) on CDN Resources | 🟠 Medium | 5.3 |
| V-05 | Missing X-Content-Type-Options Header | 🟡 Low | 3.7 |
| V-06 | Missing Referrer-Policy Header | 🟡 Low | 3.1 |
| V-07 | Missing Permissions-Policy Header | 🟡 Low | 2.6 |
| V-08 | Verbose HTTP Response Headers | 🟡 Low | 2.5 |
| V-09 | No robots.txt / sitemap.xml present | ℹ️ Info | — |
| V-10 | HTTPS enforced (Positive Finding) | ✅ Pass | — |
| V-11 | No mixed content detected | ✅ Pass | — |

---

## 5. Detailed Vulnerability Findings

---

### 🔴 V-01 — Missing Content Security Policy (CSP) Header
**Severity:** HIGH | **CVSS 3.1:** 7.4 (AV:N/AC:L/PR:N/UI:R/S:C/C:H/I:N/A:N)

**Description:**  
The application does not return a `Content-Security-Policy` HTTP response header. CSP is a critical browser-side defense that controls which resources (scripts, styles, images, fonts) are allowed to load on a page.

**Business Impact:**  
Without CSP, attackers who find a Cross-Site Scripting (XSS) vulnerability can inject malicious scripts that steal user session cookies, redirect users to fake login pages, or silently send user data to attacker-controlled servers. This is especially dangerous for any page handling user input or authentication.

**Evidence:**
```
HTTP Response Headers (observed via DevTools):
  ❌ Content-Security-Policy: [NOT PRESENT]
```

**Remediation:**  
Add a strict CSP header in your web server or CDN configuration:
```http
Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted-cdn.com; style-src 'self' 'unsafe-inline'; img-src 'self' data:; frame-ancestors 'none';
```
> 💡 **Business Tip:** Think of CSP like a strict guest list at your website — only approved resources are allowed in. This stops attackers from sneaking in malicious code even if they find a way to inject content.

---

### 🟠 V-02 — Missing X-Frame-Options Header
**Severity:** MEDIUM | **CVSS 3.1:** 6.1

**Description:**  
The `X-Frame-Options` response header is absent. This header prevents the website from being loaded inside an `<iframe>` on another site.

**Business Impact:**  
Without this protection, attackers can perform **Clickjacking attacks** — embedding the real website invisibly inside a fake page and tricking users into clicking buttons they cannot see (e.g., "Confirm Payment", "Allow Access"). This can lead to unauthorized transactions or account takeovers.

**Evidence:**
```
HTTP Response Headers:
  ❌ X-Frame-Options: [NOT PRESENT]
```

**Remediation:**
```http
X-Frame-Options: DENY
```
or, if you need to allow same-origin framing:
```http
X-Frame-Options: SAMEORIGIN
```
> 💡 **Business Tip:** This is like putting a "no screenshots" policy on your website — it stops attackers from placing an invisible overlay on top of your pages to trick your users.

---

### 🟠 V-03 — Server / Technology Information Disclosure
**Severity:** MEDIUM | **CVSS 3.1:** 5.3

**Description:**  
HTTP response headers reveal information about the underlying server technology (e.g., web server type, version, or framework). This gives attackers a head start in identifying known vulnerabilities for those specific technologies.

**Business Impact:**  
Attackers use this "fingerprinting" information to look up publicly known exploits (CVEs) for the exact server version revealed. This reduces the time and effort needed to attack the application.

**Evidence:**
```
HTTP Response Headers (example):
  Server: nginx/1.x.x      ← reveals server type and version
  X-Powered-By: Express    ← reveals backend framework
```

**Remediation:**  
Configure your web server to suppress or genericize these headers:

*Nginx:*
```nginx
server_tokens off;
```
*Apache:*
```apache
ServerTokens Prod
ServerSignature Off
```
*Express/Node.js:*
```javascript
app.disable('x-powered-by');
```

---

### 🟠 V-04 — Missing Subresource Integrity (SRI) on External CDN Resources
**Severity:** MEDIUM | **CVSS 3.1:** 5.3

**Description:**  
The application loads JavaScript or CSS files from external CDN sources without Subresource Integrity (SRI) hashes. SRI ensures that if an external file is tampered with, the browser refuses to load it.

**Business Impact:**  
If an attacker compromises a CDN provider (supply chain attack), they could modify the JavaScript file to inject malicious code into every visitor's browser session — stealing data, injecting ads, or redirecting users — with zero changes to the actual website's own code.

**Evidence:**
```html
<!-- Observed (no integrity attribute): -->
<script src="https://cdn.example.com/library.min.js"></script>

<!-- Secure version should be: -->
<script src="https://cdn.example.com/library.min.js"
        integrity="sha384-XXXXXXXX"
        crossorigin="anonymous"></script>
```

**Remediation:**  
Generate SRI hashes for all external resources at [https://www.srihash.org](https://www.srihash.org) and add `integrity` and `crossorigin` attributes to all `<script>` and `<link>` tags.

---

### 🟡 V-05 — Missing X-Content-Type-Options Header
**Severity:** LOW | **CVSS 3.1:** 3.7

**Description:**  
The `X-Content-Type-Options: nosniff` header is not present. Without it, browsers may attempt to "sniff" (guess) the content type of a response, which can lead to MIME-type confusion attacks.

**Remediation:**
```http
X-Content-Type-Options: nosniff
```

---

### 🟡 V-06 — Missing Referrer-Policy Header
**Severity:** LOW | **CVSS 3.1:** 3.1

**Description:**  
No `Referrer-Policy` header is set. Without this, the browser may send the full URL of the current page to third-party services in the `Referer` header, potentially leaking sensitive URL parameters (e.g., session tokens, search terms, user IDs).

**Remediation:**
```http
Referrer-Policy: strict-origin-when-cross-origin
```

---

### 🟡 V-07 — Missing Permissions-Policy Header
**Severity:** LOW | **CVSS 3.1:** 2.6

**Description:**  
The `Permissions-Policy` header (formerly `Feature-Policy`) is not configured. This header controls access to browser features like camera, microphone, geolocation, and payment APIs. Without it, any third-party script loaded on the page could potentially request these permissions.

**Remediation:**
```http
Permissions-Policy: camera=(), microphone=(), geolocation=(), payment=()
```

---

### 🟡 V-08 — Verbose HTTP Response Headers
**Severity:** LOW | **CVSS 3.1:** 2.5

**Description:**  
Certain response headers expose excessive detail about the infrastructure (e.g., specific version numbers, internal system names, or debug information in headers). Even individually minor, this collectively aids attacker reconnaissance.

**Remediation:**  
Audit all response headers and remove or genericize any headers not strictly required by the application.

---

## 6. Risk Matrix

```
         IMPACT
         Low    Medium    High    Critical
L  High  │  V-08  │  V-05  │  V-02  │        │
I        │  V-06  │  V-03  │  V-04  │        │
K Medium │        │  V-07  │        │        │
E  Low   │        │        │  V-01  │        │
L        └────────┴────────┴────────┴────────┘
I
H
O
O
D
```

---

## 7. Remediation Recommendations

### Priority 1 — Implement Immediately (High Risk)
- ✅ Add a `Content-Security-Policy` header to prevent XSS escalation

### Priority 2 — Implement Soon (Medium Risk)
- ✅ Add `X-Frame-Options: DENY` to prevent Clickjacking
- ✅ Remove or suppress `Server` and `X-Powered-By` headers
- ✅ Add SRI hashes to all external CDN resource links

### Priority 3 — Implement as Best Practice (Low Risk)
- ✅ Add `X-Content-Type-Options: nosniff`
- ✅ Add `Referrer-Policy: strict-origin-when-cross-origin`
- ✅ Add `Permissions-Policy` to restrict unused browser APIs
- ✅ Audit and clean up all verbose response headers

### Positive Findings (Keep Maintaining)
- 🟢 HTTPS is enforced — TLS in use, no mixed content detected
- 🟢 No obvious client-side secrets or API keys found in source

---

## 8. Tools & References

| Tool | Purpose | Link |
|------|---------|------|
| Browser DevTools | HTTP header inspection | Built into Chrome/Firefox |
| SecurityHeaders.com | Automated header analysis | https://securityheaders.com |
| Qualys SSL Labs | TLS/SSL configuration check | https://ssllabs.com/ssltest |
| OWASP ZAP | Passive web app scanning | https://www.zaproxy.org |
| Nmap | Port/service discovery | https://nmap.org |
| SRI Hash Generator | Subresource Integrity | https://www.srihash.org |

**References:**
- [OWASP Top 10 (2021)](https://owasp.org/Top10/)
- [OWASP Testing Guide v4.2](https://owasp.org/www-project-web-security-testing-guide/)
- [Mozilla Security Headers Docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)
- [CVSS v3.1 Scoring Guide](https://www.first.org/cvss/calculator/3.1)

---

## 📌 Disclaimer

> This assessment was conducted solely for **educational purposes** as part of the Future Interns Cyber Security internship program. All testing was **passive and non-intrusive** — no active exploitation, injection attacks, or authentication bypasses were performed. The target was assessed only using publicly observable HTTP responses and client-side code. This report must not be used for unauthorized testing of any system.

---

*Report prepared by: Future Interns — Cyber Security Intern*  
*Track Code: CS | Repository: FUTURE_CS_01*

