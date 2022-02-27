# Command Injection
### Task 1 Intro (What is command injection?)
- How to discover the command injection vulnerability
- How to test and exploit this vulnerability using payloads designed for different OSs
- How to prevent this vulnerability in an application
- Lastly, apply theory into practice larning in a practical

Command injection is the abuse of an application's behavior to execute commands on the OS, using the same privileges that the application on a device is running with.

A common injection vulnerability is also known as a "Remote Code Execution" (RCE) because an attacker can trick the applicatio into executing a series of payloads that they provide, without direct access to the machine itself.

Command injection is one of the top ten vulnerabilities, also is on the OWASP top ten

### Task 2 Discovering Command Injection
This vulnerability exists because applications often use functions in programming languages such as PHP, python, and NodeJS to pass data and to make system calls on the machine's operating system.

In the code snippet, the application takes data that a user enters in an input field named `$title` to search a directory for a song.

![[Pasted image 20220210155625.png]]

1. The songs variable is set and contained in the "/var/www/html/songs" directory
2. The user input is the song title they searched for. The application stores this in the `$title` variable.
3. The data within the `$title` variable is passed to the command `grep` to search a text file named *songtitle.txt* for the entry of whatever the user searched for.
4. The output of this search of *songtitle.txt* will determine whether the application informs the user that the song exists or not.

Example in Python
![[Pasted image 20220210155928.png]]

### Task 3 Exploiting Command Injection
You can often determine whether or not command injection may occur by the behaviors of an application.

Applications that use user input to populate system commands with data can often be combined in unintended behavior. For example, the shell operators `;`, `&`, and `&&` will combine two (or more) system commands and execute them both.

Can be detected in mostly one of two ways:
- Blind command injection
- Verbose command injection

**Detecting blind command injection**
There is no visible output, so it is not immediately noticeable.

We will need to use a payload that will cause some delay. For example, the `ping` and `sleep` commands are significant payloads to test with. Using `ping` as an example, the application will hang for *x* seconds in relation to how many *pings* you have specified.

The `curl` command is a great way to test for command injection because you're able to deliver data to and from an application in your payload.

ex) `curl http://vulnerable.app/process.php%3Fsearch%3DThe%20Beatles%3B%20whoami`

**Detecting Verbose Command Injection**
The easiest method of the two. Gives you feedback as to what is happening or being executed.

#### **Useful payloads**
**Linux**
- whoami
- ls
- ping
- sleep
- nc

**Windows**
- whoami
- dir
- ping
- timeout

### Task 4 Remediating Command Injection
Can be prevented in a variety of ways. The examples are from the PHP progamming language; however, the same principles can be extended to many other languages.

**Vulnerable Functions**
In PHP, many functions interact with the OS to execute commands via shell:
- exec()
- passthru()
- system()

**Input sanitization**
Sanitizing any input from a user that an application uses is a great way to prevent command injection. This is a process of specifying the formats or types of dta that a user can submit. For example, an input field that only accepts numerical data or removes any special characters such as `>`, `&` and `/`.

The PHP function `filter_input()` can be used to check whether or not data submitted via an input form is of a certain type.

**Bypassing Filter**
Applications will employ numerous techniques in filtering and sanitizing data that is taken from a user's input. These filters will restrict you to sepcific payloads; however we can abuse the logic behind an application to bypass these filters. For example, an application might filter out quotation marks, we can instead use the hexadecimal value to achieve the same result.




