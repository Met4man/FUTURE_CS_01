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

*Report prepared by: David Msekena*  
Repository: FUTURE_CS_01*
*Email: msekena.d@gmail.com

