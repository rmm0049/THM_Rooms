# Yara Room
### Task 1 Introduction
Install Yara and follow the steps along

### Task 2 What is Yara?
Yara can identify information based on both binary and textual patterns, such as hexadecimal and strings contained within a file.
Rules are used to label these patterns. Yara rules are frequently written to determine if a file is malicious or not, based upon the features or patterns it presents.

###### Why Does Malware use strings?
Malware, just like a simple "Hello World" application, use strings to store textual data.

Ransomware ->[12t9YDPgwueZ9NyMgw519p7AA8isjr6SMw](https://www.blockchain.com/btc/address/12t9YDPgwueZ9NyMgw519p7AA8isjr6SMw) -> Bitcoin Wallet for ransom payments

### Task 3 Installing Yara (Ubuntu/Debian & Windows)
###### Installing on Kali Linux (or other Debian)
**3.2.1.1.** Updating package manager `sudo apt update -y && sudo apt upgrade -y`

3.2.1.1.**2.** Installing Yara `sudo apt install yara`

OR Installing from Source

###### Installing on Windows
Downlaod latest binaries (zip files) from their GitHub page
Run the executable/binary

### Task 4 Deploy
Deploy the Virtual Machine

### Task 5 Intro to Yara Rules
5.1 First Yara Rule

Every `yara` command requres two arguments to be valid:
- The rule file you create
- Name of file, directory, or process ID to use the rule for

For example, if you wanted to use "myrule.yar" on directory "some directory" we would use the following command:
`yara myrule.yar somedirectory`




