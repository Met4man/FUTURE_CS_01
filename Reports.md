# Content Map
---
- [1. IDOR - Using BurpSuite](#1-IDOR-using-BurpSuite)
- [2.Cross-Site Scripting (XSS)](#2.-Cross-Site-Scripting-(XSS))

# 1. IDOR using BurpSuite

# Summary 

While testing the target web app for security issues, I found critical Insecure Direct Object Reference (IDOR) vulnerabilities with the help of automated tools. this could lead to unauthorized access to the database, data leaks, or even affect the overall integrity of the application.

Target Details

| Component | Details |
| :-- | :-- |
| Host Domain | ztw.ctbb.show |
| Web Server Technology | nginx/1.24.0 |
| Testing Tool | Burpsuite & Developer Tools |
| Request file used | login_request.txt |

# So What is IDOR?

Insecure Direct Object Reference (IDOR) is a type of access control vulnerability where an application exposes internal object identifiers (like user IDs, file names, or database keys) and fails to properly verify whether the user is authorized to access them.

### 1. Vulnerable Parameter
Parameter: id (GET request)
Location: Observed in the GET Request URL( If a user can change a parameter and access someone else’s data → IDOR exists).

What was done
  - Generated credentials and logged in into the lab.

Screenshot:
<img width="1272" height="662" alt="image" src="https://github.com/user-attachments/assets/d368d84f-e2e2-4f40-ae8e-3022616f144a" />

---
  - Identified user/object id parameter that was initially 4 and changed to 6.

 Screenshot:


<img width="1523" height="791" alt="image" src="https://github.com/user-attachments/assets/4e2f8794-b29b-472f-b018-c5fe6bee21c7" />
<img width="1271" height="663" alt="image" src="https://github.com/user-attachments/assets/aa435314-83f2-4ca5-8a8a-dc5994b91d51" />


# Output Observed:

After modifying the user id to 6 we can view a different user's senitive data.

# Exposed Sensitive Data

| Field      | Value                         |
|------------|-------------------------------|
| ID         | 6                             |
| First Name | Frank                         |
| Last Name  | Smith                         |
| Email      | frank.johnson@testmail.org    |
| Role       | user                          |
| Phone      | 555-6966                      |
| Password   | e2a9aa7346cdc4ff              |
| Join Date  | 2025-08-07                    |

Screenshot

<img width="1258" height="746" alt="image" src="https://github.com/user-attachments/assets/c8250577-6b7d-40e3-beab-aea8f9cd1d15" />

---

# 2. Cross-Site Scripting (XSS)

Description
XSS vulnerabilities allow attackers to inject and execute malicious scripts in a user’s browser, leading to session hijacking or data theft.

Payloads Used
XSS Finding 1: Reflected XSS in name Parameter
Page: /dvwa/vulnerabilities/xss_r/
Payload Used: <script>alert('XSS')</script>
Parameter: name
Test Performed: Entered payload in the "name" input field and submitted the form.
Result: A browser alert popped up with the message "XSS". After closing the popup, the page displayed the name as "hello", which confirms that user input was reflected without handling or sanitization.
Screenshot:

