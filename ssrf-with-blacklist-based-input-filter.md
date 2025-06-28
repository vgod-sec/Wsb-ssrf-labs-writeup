SSRF with Blacklist-Based Input Filter

Objective:
Access the internal admin interface at http://localhost/admin and delete the user carlos by bypassing weak SSRF blacklist filters.

Vulnerability Overview:
The application includes a stock check feature that fetches server-side data based on a user-supplied URL. The developer has implemented basic blacklist filters to block common hostnames and paths like localhost and /admin. These filters can be bypassed using alternative representations and encoding tricks.

Exploitation Steps:

1. Intercept the Request  
   - Visit any product page and click the Check stock button.  
   - Intercept the request in Burp Suite and send it to Burp Repeater.

2. Test Direct Access  
   - Change the stockApi parameter to:  
     http://127.0.0.1/  
   - Observe that the request is blocked by the SSRF filter.

3. Bypass Localhost Filter  
   - Change the URL to use a shortened IP:  
     http://127.1/  
   - This bypasses the blacklist and allows access to the internal server.

4. Test Admin Path  
   - Change the URL to:  
     http://127.1/admin  
   - Notice this is also blocked due to the presence of the word admin.

5. Bypass Admin Filter  
   - Use double URL encoding to bypass the filter:  
     http://127.1/%2561dmin  
   - This will be decoded to /admin by the server, successfully bypassing the filter.

6. Final Exploit  
   - Use the bypassed path to send the final payload:  
     http://127.1/%2561dmin/delete?username=carlos  
   - This deletes the user and solves the lab.

Mitigation:
- Avoid relying solely on blacklists; they are easily bypassed.
- Use allowlists of valid domains or IPs.
- Normalize and validate all URLs server-side before processing.
- Restrict internal metadata and admin endpoints using firewall rules.
- Implement network segmentation for sensitive services.

References:
- https://portswigger.net/web-security/ssrf
- https://portswigger.net/web-security/ssrf/filters

Author:
vgod  
Cybersecurity Learner | Bug Bounty Enthusiast  
GitHub: https://github.com/vgod-sec
