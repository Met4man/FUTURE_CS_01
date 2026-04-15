# Content Map
---
- [1.1 IDOR - Using BurpSuite](#11-IDOR-using-BurpSuite)
- [1.2 IDOR - Via Web Browser] 
- [2. Cross-Site Scripting (XSS)]
-XSS Finding 1: Reflected XSS in name Parameter
     -XSS Finding 2: Reflected XSS via img Tag
     -XSS Finding 3: Reflected XSS via svg Tag
     -XSS Finding 4: Reflected XSS via iframe Tag
     -XSS Finding 5: Reflected XSS via input Tag with type="image"
     -XSS Finding 6: Stored XSS via script Injection in Message Box
XSS Finding 7: Stored XSS via img Tag Injection in Message Box
XSS Finding 8: Stored XSS via svg Tag Injection in Message Box
XSS Finding 9: Stored XSS via iframe Tag with javascript: Source in Message Box
XSS Finding 10: Stored XSS via marquee Tag with onstart Event in Message Box
3. Authentication Flaws – Brute Force Attack via Burp Suite

# 1.1-IDOR-using-BurpSuite

# Summary 

While testing the target web app for security issues, I found critical SQL Injection (SQLi) vulnerabilities with the help of automated tools. The main concern lies in the GET parameter id, which is exposed to several SQLi attack patterns. If exploited, this could lead to unauthorized access to the database, data leaks, or even affect the overall integrity of the application.

Target Details
Component	Details
Web Server OS	Linux (Debian)
Web Server Technology	Apache 2.4.63
Database	MySQL (MariaDB 5.0.12+)
Testing Tool	SQLMap
Request file used	login_request.txt
Vulnerability Info
1. Vulnerable Parameter
Parameter: id (GET request)
Location: Observed in login_request.txt( It contain the raw HTTP request from the sqli page in DVWA ) during automated scanning
2. Injection Types Confirmed
Method	Description	Example Payload
Time-based Blind	Server response delayed if injection is successful.	id=admin' AND (SELECT SLEEP(5)) AND 'ZIVL'='ZIVL&Submit=Submit
UNION-based Injection	Allows extraction of data by combining SELECT results.	id=admin' UNION ALL SELECT CONCAT(...hex...),NULL-- -&Submit=Submit
3. Technical Evidence
SQLMap confirmed the backend is MySQL (MariaDB fork).
The vulnerable endpoint responds positively to both time-based and UNION query payloads.
Affected parameter is verified to be injectable using multiple techniques.
SQLMap Output Excerpt
sqlmap identified the following injection point(s) with a total of 120 HTTP(s) requests:
---
Parameter: id (GET)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=admin' AND (SELECT 2138 FROM (SELECT(SLEEP(5)))IxYj) AND 'ZIVL'='ZIVL&Submit=Submit

    Type: UNION query
    Title: Generic UNION query (NULL) - 2 columns
    Payload: id=admin' UNION ALL SELECT CONCAT(0x716b787871,0x4e617a516e5462456a65504151506b4b67637744755153716b5041736f6b4d65596c777176754b62,0x716a717171),NULL-- -&Submit=Submit
---
web server operating system: Linux Debian
web application technology: Apache 2.4.63
back-end DBMS: MySQL >= 5.0.12 (MariaDB fork)
Potential Impact
Attackers can pull out sensitive stuff like user details, passwords, and more.
There’s a chance the database could get tampered with; data might be changed, wiped out, or messed up.
If login credentials are leaked, it could lead to deeper system breaches.
These kinds of issues can hit hard, breaking compliance rules and damaging the brand’s image.
Recommended Mitigation
Always use prepared statements that securely handle query parameters.
Strictly validate and sanitize all user inputs, especially those coming through GET/POST parameters.
Configure database accounts with only the minimum permissions needed for the application to run.
Avoid showing raw database errors to users, use generic and user-friendly error messages instead.
Supporting Evidence
Logs of SQLMap output are in the sqlmaplogs folder.
raw request file used (login_request.txt).
Automated tool response logs confirming vulnerability type and affected parameter.
1.2 SQL Injection - Manual
Description
SQL Injection allows attackers to execute arbitrary SQL code by injecting malicious input into SQL queries.

Payloads Used
' OR '1'='1  
This payload uses a classic SQL injection trick. It always makes the condition true, so the website shows all user records by bypassing normal checks and displaying a list of users.
Screenshot From 2025-07-23 12-31-37
" OR "1"="1  
Tried the same trick as above, but with double quotes. If the website doesn’t use double quotes in its SQL, this will not work so no results or nothing changes.
admin' --  
This tries to comment out part of the SQL query to bypass login, but the website might show a blank (white) page or an error message instead.
' OR 'a'='a  
Another version of the always-true condition. It works like the first payload and makes the site show all user records again.
' UNION SELECT NULL, username, password FROM users --  
This tries to pull out usernames and passwords from the database with a UNION query. I guess because the columns don’t match, it gave blank page.
' UNION SELECT 1,2,3 --  
This payload checks if you can run a UNION SELECT with three columns. because it failed , it gave a blank screen.
' AND 1=CONVERT(int, (SELECT @@version)) --  
This payload tries to create an error by making the database do something it can't like converting text to a number). If it works, the site might show an error message. but the site showed and blank white screen.
' OR IF(1=1, SLEEP(5), 0) --  
This is a “time-based” injection. If it works, the website reply slows down (waits 5 seconds), but you won’t see any new data on the page. but i my case it waits more than 10 sec and gave nothing means white screen.
'; WAITFOR DELAY '00:00:05' --
This is also a timing test, but for Microsoft SQL Server. The page will pause for 5 seconds if the injection works, but won’t show any database info. Same thing happened....
Mitigation
Use parameterized queries (prepared statements)
Validate and sanitize all inputs
Disable detailed SQL error messages
Conclusion
The web application is critically vulnerable to SQL Injection via the id parameter. Prompt remediation following the recommendations above is strongly advised to mitigate this risk and prevent exploitation.

2. Cross-Site Scripting (XSS)
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

