# IDOR Insecure Direct Object Reference
### Task 1 What is an IDOR?
This type of vulnerability can occur when a web server receives user-supplied input to retrieve objects (files, data, documents), too much trust has been placed on the input data, and is not validated on the server-side to confirm the requested objects belong to the user requesting it.

### Task 2 An IDOR Example
Imagine you've signed up for an online service, and you want to change your profile information. The link you click on goes to http://online-service.thm/profile?user_id=1305, and you can see you info.

Now imagine you change the user_id value to something else.

### Task 3 Finding IDORs in Encoded IDs
When passing data from page to page either by post data, query strings, or cookies, web developers will often take the raw data and encode it. Encoding ensures that the receiving web server will be able to understand the contents. Encoding changes binary data into an ASCII string commonly using the `a-z, A-Z, 0-9 and =` character for padding. The most common encoding is base64 encoding and is fairly easy to spot.

### Task 4 Finding IDORs in Hashed IDs
Hashed IDs are a little bit more complicated to deal with than encoded ones, but they may follow a predictable pattern.

### Task 5 Finding IDORs in Unpredictable IDs
If the ID cannot be detected using the above methods, an excellent IDOR detection is to create two accounts and swap the ID numbers between them. If you can view the other users' content using their ID number while still being logged in with a different account, you've found an IDOR vulnerability

### Task 6 Where are IDORs located?
The vulnerable endpoint you're targeting may not always be something you see in the address bar. It could be content your browser loads in via an AJAX request or something that you find referenced in a JavaScript file.

Sometimes endpoints could have an unreferenced parameter that may have been of some use during development and got pushed to production. For example you may see a call to **/user/details** displaying your information. But through an attack known as parameter mining, you discover a parameter **user_id** that you can use to display other users' information such as **/user/details?user_id=123**

### Task 7 A Practical IDOR Example



