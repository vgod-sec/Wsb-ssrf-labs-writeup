This lab demonstrates a Server-Side Request Forgery (SSRF) vulnerability in an application that implements a whitelist-based input filter. The stock-checking feature of the application retrieves data from a backend server using a user-supplied URL. To prevent SSRF, the application validates whether the hostname in the URL belongs to a predefined list of trusted domains. However, the input filter is not sufficiently strict and can be bypassed using URL parsing tricks.

The objective is to access the internal admin interface hosted at http://localhost/admin and delete the user carlos, even though the application is designed to block direct access to internal addresses like localhost or 127.0.0.1.

To begin the attack, intercept a stock-checking request using a tool like Burp Suite and send it to Repeater for testing. Initially, changing the `stockApi` parameter to `http://127.0.0.1/` results in rejection, confirming that the whitelist filter is in place.

Next, exploit the fact that URLs can contain embedded credentials in the format `http://username@hostname/`. Change the URL to `http://username@stock.weliketoshop.net/`. This URL appears to the filter as pointing to a whitelisted domain (`stock.weliketoshop.net`), but the username part can be manipulated to redirect the actual request.

The next step is to test the behavior when a `#` character is added to the username, like `http://username#@stock.weliketoshop.net/`. This results in rejection, indicating the filter is parsing the full string, including fragments.

To bypass this, double URL-encode the `#` character into `%2523`. This encoded value will be decoded once by the URL parser, resulting in a single `%23`, which is interpreted as the start of a fragment. However, because the whitelist filter checks the unprocessed string, it still sees a valid whitelisted domain.

Now, construct the final payload URL as follows:  
`http://localhost:8080%2523@stock.weliketoshop.net/admin/delete?username=carlos`

In this case, the whitelist sees `stock.weliketoshop.net`, which is allowed. But after decoding, the backend resolves the actual hostname to `localhost:8080`, which points to the internal admin panel. The appended path `/admin/delete?username=carlos` reaches the admin interface, triggering the deletion of the target user.

This exploit shows how attackers can manipulate URL encoding and structure to bypass SSRF protections. Proper URL parsing and validation after full decoding are critical to preventing such vulnerabilities.
