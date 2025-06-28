Basic SSRF Against Back-End System

Extended description: Exploit an SSRF vulnerability to access a protected internal admin interface and delete a specific user.

Objective:
Access the back-end admin interface via SSRF and delete the user carlos to solve the lab.

Vulnerability Overview:
The application includes a "Check stock" feature that makes a server-side HTTP request to a URL defined by the stockApi parameter. Since the input is insufficiently validated, it's possible to manipulate the URL and redirect the server to access internal resources.

Exploitation Steps:

1. Intercept the Request  
   - Navigate to any product page and click the Check stock button.  
   - Intercept the HTTP request in Burp Suite.

2. Modify the stockApi Parameter  
   Replace the value of stockApi with:  
   http://192.168.0.1:8080/admin

3. Send the Request to Intruder  
   - Highlight the last octet (1) in the IP address and click Add $.  
   - Set the payload type to Numbers.  
   - Configure the payload range:  
     From: 1  
     To: 255  
     Step: 1

4. Launch the Attack  
   - Click Start attack.  
   - Sort the responses by Status code or Length.  
   - Look for a request that returns a 200 OK response, indicating successful access to the internal admin interface.

5. Access Admin Interface  
   - Send the successful request to Repeater.  
   - Modify the path to:  
     /admin/delete?username=carlos  
   - Send the request.

6. Success  
   - A successful response confirms the user carlos has been deleted.  
   - Lab is now solved.

Mitigation Recommendations:
- Implement strict allowlists for URLs or domains.
- Reject requests to internal IP ranges (e.g., 127.0.0.1, 169.254.0.0/16, 192.168.0.0/16).
- Avoid using user-supplied input directly in server-side requests.
- Perform DNS resolution checks and compare resolved IPs against a safe list.
- Isolate internal services with strict network segmentation and access controls.

References:
- https://portswigger.net/web-security/ssrf
- https://portswigger.net/web-security/ssrf/filters

Author:
vgod  
Cybersecurity Learner | Bug Bounty Enthusiast  
GitHub: https://github.com/vgod
