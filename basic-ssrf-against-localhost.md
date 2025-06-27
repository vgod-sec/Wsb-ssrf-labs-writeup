 Lab: Basic SSRF Against the Local Server

This lab demonstrates a simple Server-Side Request Forgery (SSRF) vulnerability by exploiting a stock check feature that fetches data from an internal system.


🎯 Goal

Exploit the SSRF vulnerability to access the internal admin interface on `localhost` and delete the user `carlos`.


 Steps to Reproduce

1. Try to access the admin panel directly by visiting: /admin
→ It is not accessible directly from the browser.

2. Find a product, click "Check stock", and intercept the request using Burp Suite.

3. Send the intercepted request to Burp Repeater.

4. Modify the `stockApi` parameter to: http://localhost/admin
 → This causes the server to fetch the admin interface internally.

5. Read the HTML response and find the delete URL: http://localhost/admin/delete?username=carlos

6. Now send a new request with the `stockApi` parameter set to the above delete URL:
7. The server processes the request, deletes the user, and the lab is marked as solved.

---

🛡️ Mitigation

- Block requests to internal IPs and `localhost`.
- Use allow-lists for external URLs.
- Sanitize and validate all user-supplied URLs.

