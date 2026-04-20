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

---


## 📋 Project Overview

This project documents a **Grey-box Vulnerability Assessment** performed against the live web application hosted at [`https://ztw.ctbb.show/#home`](https://ztw.ctbb.show/#home), as part of the **Future Interns Cyber Security Internship Programme — Track: CS**.

The assessment was conducted to:
- Identify and document exploitable security weaknesses within the application
- Evaluate their potential impact using the **CVSS v3.1** risk rating framework
- Recommend practical, actionable remediation measures

A total of **4 vulnerabilities were identified** — **1 rated Critical** and **3 rated High** — covering areas such as access control, client-side scripting, session management, and insecure HTTP methods.

> [!WARNING]
> All testing was performed exclusively within an authorized, educational scope as part of the Future Interns internship programme. No malicious, destructive, or unauthorized techniques were employed at any point.

---

## 🚨 Vulnerabilities Found

| # | Vulnerability | CVSS Score | Risk Rating |
|:-:|--------------|:----------:|:-----------:|
| 1 | Insecure Direct Object Reference (IDOR) | `8.1 / 10` | 🔴 **High** |
| 2 | Broken Access Control | `8.8 / 10` | 🔴 **High** |
| 3 | Reflected Cross-Site Scripting (XSS) | `9.3 / 10` | 🔴 **Critical** |
| 4 | Cross-Site Request Forgery (CSRF) via GET | `8.0 / 10` | 🔴 **High** |

<br>

---

## 📁 Repository Structure

```
📦 FUTURE_CS_01/
│
├──  images/                                     # Evidence & proof-of-concept screenshots
│   ├── idor_api_response.png                      # Unauthorized API response — IDOR confirmed
│   ├── idor_sequential_ids.png                    # Sequential ID enumeration via DevTools
│   ├── broken_access_admin_route.png              # Admin route accessible by regular user
│   ├── broken_access_jwt_payload.png              # JWT role field — client-side modifiable
│   ├── xss_reflected_alert.png                    # Reflected XSS alert(document.cookie) fired
│   ├── xss_missing_csp_header.png                 # Missing Content-Security-Policy header
│   ├── xss_session_exfiltration.png               # Session cookie exfiltration POC
│   ├── csrf_get_endpoint.png                      # State-changing GET endpoint intercepted
│   ├── csrf_no_token.png                          # No CSRF token in request — Burp Suite
│   ├── csrf_account_deletion.png                  # Account deletion triggered via crafted URL
│   └── security_headers_scan.png                  # SecurityHeaders.com scan result
│
├── 📄 Vulnerability_Assessment_Report.pdf    # Full 15-page assessment report
├── 📄 Vulnerability_Assessment_FUTURE_CS_01.pptx          # 15-slide presentation deck
└── 📄 README.md                                           # This file
```

## 👤 Author

**Cyber Security Intern**

📍 Dar es Salaam, Tanzania

    Institute of Finance Management

🏢 **Internship:** [Future Interns](https://www.linkedin.com/company/future-interns/)

📞 +255 749 604 110 &nbsp;|&nbsp; 📧 msekena.d@gmail.com &nbsp;|&nbsp; 

