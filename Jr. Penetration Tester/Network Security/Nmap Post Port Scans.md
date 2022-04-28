### Service Detection
Once Nmap discovers open ports, you can probe the available port to detect the running service. Further investigation of open ports is an essential piece of information as the pentester can use it to learn if there are any known vulnerabilities of the service.

Adding `-sV` to Nmap will collect service and version info. You can control the intensity with `--version-intensity LEVEL` where the level ranges between 0, the lightest, and 9, the most complete. `-sV --version-light` has intensity of 1 where `-sV --version-all` has intensity 9

Nmap does a TCP 3-way handshake because it needs it to determine service and version info, so a `-sS` scan is not possible

### OS Detection and Traceroute

**OS Detection**
Nmap can detect the OS based on its behavior and any telltale signs in its responses. OS detection can be enabled using the `-O` option. Ex) `nmap -sS -O TARGET`.

Many factors will determine the accuracy of this. The guest OS fingerprints might get distorted due to the rising use of virtualization and similar technologies. Therefore, always take the OS version with a grain of salt.

**Traceroute**
If you want Nmap to find the routers between you and the target, just add `--traceroute`. 

### Nmap Scripting Engine (NSE)

A part of Nmap is the NSE, a Lua interpreter that allows Nmap to execute Nmap scripts written in the Lua language.

The default Nmap installation contains close to 600 scripts. These are locate in `/usr/share/nmap/scripts`

You can specify to use any or group of these install scripts; moreover, you can install other user's scripts and use them for scans. Default scripts is done by `--script=default` or with `-sC` flag. In addition to this, there are script categories you can specify.

**Script Categories**
- `auth`: Authenticated related scripts
- `broadcast`: Discover hosts by sending broadcast messages
- `brute`: performs brute-force password auditing against logins
- `default`: Default scripts, same as `-sC`
- `discovery`: Retrieve accessible information, such as database tables and DNS names
- `dos`: detect servers vulnerable to Denial of Service (DoS)
- `exploit`: Attempts to exploit various vulnerable services
- `external`: Checks using a third-party service, such as Geoplugin and VirusTotal
- `fuzzer`: Launch fuzzing attacks
- `intrusive`: Intrusive scripts such as brute-force attacks and exploitation
- `malware`: Scans for backdoors
- `safe`: Safe scripts that won't crash the target
- `version`: Retrieve service versions
- `vuln`: checks for vulnerabilities or exploit vulnerable services

We use Nmap to run a SYN scan against `10.10.208.51` and execute the default scripts in the console shown below

`nmap -sS -sC TARGET`

You can also specify the script by name using `--script "Script-Name"` or a pattern such as `--script "ftp*"`, which would include `ftp-brute`.

### Saving the Output

Three output formats
1. Normal
2. Grepable (`grep`able)
3. XML

There is a fourth one that we cannot recommend:
- Script Kiddie

**Normal**
As the name implies, the normal output is similar to the output you get on the screen when scanning a target. `-oN FILENAME`

**Grepable**
grep, Global Regular Expression Printer. `-oG FILENAME`. The grepable output is only 4 lines from previous example vs 21 in the normal format.

**XML**
The third format is XML. You can save the scan results in XML format using `-oX FILENAME`. The XML format would be most convenient to process to output in other programs. Conveniently enough, you can scan output in all three formats using `-oA FILENAME`




