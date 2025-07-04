Manual Injection Testing — Login, Registration and Recovery

Scope:
Login, registration and password recovery forms
Techniques: SQL Injection, malformed inputs, format validation
Goal: Check if bypass is possible, detect bad validation, injection vulnerabilities

Section: Login Form

TC-LOGIN-01 — Email format enforcement
Input: admin, <>, !@#, and similar
Field: Email
Expected: Only emails like something@something.com are accepted
Actual: All invalid formats blocked, frontend showed popup

TC-LOGIN-02 — Weak password check
Input: qwe, 123456, password, one-letter strings
Field: Password
Expected: Should be flagged or rejected
Actual: Form accepts any password, backend does the real check

TC-LOGIN-03 — SQL injection in password
Payloads: ' OR '1'='1, '--, UNION SELECT NULL, admin' --
Field: Password, with real email
Expected: Should block login
Actual: No bypass happened, looks like input is sanitized or prepared statements are used

TC-LOGIN-04 — SQL injection in email
Payloads: ' OR '1'='1@x.com, admin' --@test.com
Field: Email
Expected: Rejected by frontend
Actual: All failed — frontend enforces strict pattern for emails

Section: Registration Form

TC-REG-01 — Email format
Input: admin, admin@, @example.com, abc@x
Field: Email
Expected: Only emails like name@domain.com accepted
Actual: All invalid emails blocked on frontend

TC-REG-02 — XSS in password field
Input: <script>alert(1)</script>
Field: Password
Expected: Rejected or cleaned
Actual: Just treated as plain text, no execution

TC-REG-03 — Fake data with valid pattern
Input: huiduha@huiduha.com and 123456
Expected: Accept if no backend email validation
Actual: Form lets user register, no ownership check

Section: Recovery Form

TC-REC-01 — XSS payloads
Payloads: <script>, "><script>, onerror=alert(1)
Field: Email
Expected: All blocked
Actual: Validation worked — special characters not accepted

TC-REC-02 — SQL injection payloads
Payloads: ' OR '1'='1, UNION SELECT *
Field: Email
Expected: Blocked
Actual: Everything blocked by frontend validation

Summary

Email format check: works fine, strict on client side
Password: weak passwords go through, backend checks real match
SQL injection in email: blocked by frontend
SQL injection in password: failed, looks protected
Script in password: harmless, just text
Registration with fake data: works, no email verification
Recovery form: filters all bad input, only valid emails allowed

Recommendations

Backend should repeat email format check, not rely only on frontend
Better password rules: length, symbols, banned words
Email verification needed during registration
Log brute-force or repeated payload attempts