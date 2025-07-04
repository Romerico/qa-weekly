Manual Injection Testing — Search 

Scope:
Search suggestion API (/api/search-suggestions/)
Search query string (/search/?q=...)

Techniques: XSS, SQL Injection, malformed input
Goal: Detect errors, crashes, input problems

Section: XSS

TC-XSS-01 — Normal script tag
Payload: <script>alert(1)</script> 
Endpoint: /api/search-suggestions/?q=%3Cscript%3Ealert(1)%3C%2Fscript%3E
Expected: Treated as text
Actual: All the goods showed up with a 'Free' pricetag first and increasing the price


TC-XSS-02 — Broken script tag
Payload: <script>alert!</script>
Endpoint: /api/search-suggestions/?q=%3Cscript%3Ealert!<script>
Expected: Ignored or filtered
Actual: All the goods showed up with a 'Free' pricetag first and increasing the price

TC-XSS-03 — Script with trailing space
Payload: <script>
Endpoint: /api/search-suggestions/?q=%3Cscript%3E%20
Expected: Ignored
Actual: Gave 500 Internal Server Error — backend crashed


Section: SQL Injection

TC-SQLI-01 — Simple injection
Payload: username = 'admin' OR '1'='1'
Endpoint: /search/?q=username+%3D+%27admin%27+OR+%271%27%3D%271%27
Expected: Input cleaned/ignored
Actual: Few results showed up 

TC-SQLI-02 — Simple injection 1
Payload: ' OR 1=1 --
Endpoint: /search/?q=' OR 1=1 --
Expected: Input cleaned/ignored
Actual: Treated like 'search all'

TC-SQLI-03 — Subquery
Payload: ' OR (SELECT COUNT() FROM users) > 0 --
Endpoint: /search/?q=%27+OR+(SELECT+COUNT()+FROM+users)+%3E+0+--
Expected: Input cleaned/ignored
Actual: Few results showed up


Summary

Basic script: treated as text
Broken tag: caused UI glitch
Script with space: backend crash (500 error)
SQL in search: sometimes shows wrong results
Subquery: handled okay
Recovery form: only accepts proper emails

Recommendations

Sanitize everything on server too
Fix crash from malformed input (500 error)
Check why UI reacts weird to broken tags
Keep checklist for all new input forms