
# Lab: SSRF with Filter Bypass via Open Redirection Vulnerability

##  Description
This lab contains a stock-checking feature that fetches data from an internal system using a `stockApi` parameter. Direct access to internal services like `http://192.168.0.12:8080/admin` is blocked by the server. However, the application has an open redirect vulnerability in the `path` parameter of the `/product/nextProduct` endpoint, which we exploit to bypass the SSRF filter and access the restricted admin interface.


##  Objective
Delete the user `carlos` by exploiting the SSRF vulnerability using an open redirection as a filter bypass.


##  Steps to Reproduce

### 1. Intercept the Stock Check Request
- Visit any product page and click **Check stock**.
- Capture the request using **Burp Suite**.
- Note the `stockApi` parameter in the request.

### 2. Test for SSRF Restrictions
- Modify the `stockApi` parameter to point directly to:
  ```
  http://192.168.0.12:8080/admin
  ```
- Observe that the request is blocked due to SSRF filter.

### 3. Identify the Open Redirect
- Click **Next product** on any product page.
- Intercept and analyze the request:
  ```
  GET /product/nextProduct?path=/some/path
  ```
- The value of `path` is reflected in a redirection response, confirming an open redirect vulnerability.

### 4. Create SSRF Payload with Redirection
- Use the open redirect endpoint to redirect the request to the internal admin page.
- Modify the `stockApi` value like this:
  ```
  /product/nextProduct?path=http://192.168.0.12:8080/admin
  ```

### 5. Confirm Admin Page Access
- The stock checker follows the redirection and loads the admin interface.

### 6. Perform Malicious Action
- Now, modify the path to delete the user `carlos`:
  ```
  /product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos
  ```
- Submit the request and confirm that the user is deleted.


## ✅ Lab Solved
Once the deletion request is successful, the lab will be marked as completed.

 Mitigation
- Avoid making server-side HTTP requests using unvalidated user input.
- Implement strict URL allow-listing for redirects.
- Validate and sanitize all redirection URLs.
- Segment internal systems from public-facing applications.
- Monitor logs for unusual SSRF-related activity.


**Tags**: `SSRF`, `Open Redirect`, `Filter Bypass`, `Burp Suite`, `Web Security`, `PortSwigger Labs`
