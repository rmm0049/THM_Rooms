# File Inclusion
### Task 1 Intro
File Inclusion Vulnerabilities include LFI (Local File Inclusion), RFI (Remote File Inclusion), and directory traversal. Web applications are written sometimes to request access to files on a given system, including images, static text, and so on via parameters. Parameters are query strings attached to the URL that could be used to retrieve data or perform actions based on user input.

![[Pasted image 20220131120750.png]]

The scenario where a user requests to access files from a webserver. First, the user sends an HTTP request to the webserver that includes a file to display. Ex) `http://webapp.thm/get.php?file=userCV.pdf`, where the `file` is the parameter and the `userCV.pdf` is the required file to access.

![[Pasted image 20220131120913.png]]

##### Why do File inclusion vulnerabilities happen?
Commonly found and exploited in various programming languages for web applications, such as PHP that are poorly written and implemented. The main culprit is input validation, in which the user inputs are not validated or sanitized.

### Task 3 Path Traversal
Also known as `Directory traversal`, a web security vulnerability that allows an attacker to read operating system resources, such as local files on the server running an applications.

![[Pasted image 20220131121350.png]]

Common OS files to look for:
- `/etc/issue` -> contains a message or system ID to be printed before the login prompt
- `/etc/profile` -> controls system-wide default variables, such as Export variables
- `/proc/version` -> specifies the version of the Linux kernel
- `/etc/passwd` -> has all registered user that has access to a system
- `/etc/shadow` -> contains info about the system's users' passwords
- `/root/.bash_history` -> root user's bash history
- `/var/log/dmessage` -> contains global system messages, including messages that are logged during system startup
- `/var/mail/root` -> all emails for root user
- `/root/.ssh/id_rsa` -> root user's ssh private key
- `/var/log/apache2` -> accessed requests for Apache webserver
- `C:\boot.ini` -> contains the boot options for computers with BIOS firmware

### Task 4 Local File Inclusion - LFI
LFI attacks against web applications are often due to a developer's lack of security awareness. With PHP, using functions such as `include`, `require`, `include_once`, and `require_once` often contribute to vulnerable web applications.

1. Suppose the web application provides two languages, and the user can select between the `EN` and `AR`
```scheme
	<?php
		include($_GET["lang"]);
	?>
```

The PHP code above uses a `GET` request via the URL parameter `lang` to include the file of the page. The call can be done by sending the following HTTP request as follows: `http://webapp.thm/index.php?lang=EN.php` to load the English page or `http://webapp.thm/index.php?lang=AR.php` to load the Arabic page.

Theoretically we could read the `/etc/passwd` file by using `http://webapp.thm/get.php?file=/etc/passwd`

### Task 5 Local File Inclusion - LFI #2
1. The first two cases, we checked the code for the web app, however in this case we are testing a black box, in which we do not have the source doe.

The entry point is `http://webapp.thm/index.php?lang=EN`

We still get an error after `http://webapp.thm/index.php?lang=../../../../etc/passwd`

This is because the `include` function reads the input with `.php` at the end. This tells us that the developer specifies the file type to pass to the include function. To bypass this scenario, we can use the NULL BYTE, which is `%00`.

**Note**: The `%00` trick does not work with PHP 5.3.4 and above

2. Now the developer decided to filter keywords to avoid disclosing sensitive information. The `/etc/passwd` file is being filtered. There are two possible methods to bypass the filter. First, by using the NullByte (%00) or the current directory trick at the end of the filtered keyword `/.`.
3. Next, the developer starts to use input validation by filtering some keywords.

error message after: `http://webapp.thm/index.php?lang=../../../etc/passwd`

The application replaces the `../` with the empty string. We can bypass this by doubling up: `....//....//....//....//etc/passwd`

The filtering only does a first pass, and only gets rid of the first `../`

### Task 6 Remote File Inclusion - RFI
Remote File Inclusion is a technique to include remote files and into a vulnerable application. Like LFI, the RFI occurs when improperly sanitizing user input, allowing an attack to inject an external URL into `include` function. One requirement for RFI is that the `allow_url_fopen` option needs to be `on`.

The risk of RFI is higher than LFI sinch RFI vulnerabilities allow an attacker to gain Remote Command Execution (RCE) on the server. Other consequences:
- Sensitive Information Disclosure
- Cross-site scripting
- Denial of Service

##### RFI steps
Let's say that the attacker hosts a PHP file on their own server `http://attacker.thm/cmd.txt` where `cmd.txt` contains a printing message `Hello THM`.

```scheme 
<?php echo "Hello THM"; ?>
```

### Task 7 Remediation
1. keep systems and services, including web applications frameworks, updated with latest version
2. Turn off PHP errors to avoid leaking the path of the application and other potentially revealing information.
3. A Web Application Firewall (WAP) is a good option to help mitigate web app attacks
4. Disable some PHP features that cause file inclusion vulnerabilities, if you web app doesn't need them, such as `allow_url_fopen` and `allow_url_include`
5. Carefully analyze the web app and allow only protocols and PHP wrappers are in need
6. Never trust user input, sanitize and validate
7. Implement white listing for file names and locations as well as blacklisting



