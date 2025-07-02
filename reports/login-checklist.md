This checklist summarizes manual testing performed on the login and registration forms of the test website. The focus was on validating input fields, testing for SQL injections, and verifying basic security controls without formal documentation.

Checklist
 Login form accepts only valid email formats (must include '@' and domain)
Verified: special characters and invalid formats are blocked by frontend validation popup.

 Registration form enforces email pattern %@%.com
Verified: emails not matching pattern are rejected; password field accepts any input.

 Password field tested with 5 different SQL injection payloads for an existing user
Verified: all injection attempts were rejected; login did not bypass authentication.

 SQL injection attempts in email field (including attempts with valid email patterns)
Verified: injections failed due to frontend validation and backend protections.

 Weak passwords and random emails tested for login
Verified: login correctly rejects invalid credentials without bypass.

Conclusion
Frontend validation enforces strict email formatting, preventing malformed input at the client side.

Backend protections (likely prepared statements or parameterized queries) prevent SQL injection attacks on password field.

No security flaws related to SQL injection were found during this testing phase.

Further testing is recommended for other attack vectors such as XSS and authentication bypasses in registration or other forms.


