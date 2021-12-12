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

Example rule
```scheme
rule examplerule {
	condition: true
}
```

For example, if you wanted to use "myrule.yar" on directory "some directory" we would use the following command:
`yara myrule.yar somedirectory`

### Task 6 Expanding on Yara Rules
Some more yara conditions:
- Keyword
- Desc
- Meta
- Strings
- Conditions
- Weight

###### Meta
Is for decriptive information about the rule such as the author and does not impact the actual rule, similar to commenting in code.

##### Strings
Checks for specific text or hexadecimal in files or programs. For example, if you'd want to search a directory for all files containing "Hello World!"

```scheme
rule hello_world_checker{
	strings:
		$hello_world = "Hello World!"
}
```

The keyword `strings` and then the actual text we want to search "Hello World!" is stored in the variable `$hello_world`.

In order to make the rule valid, we need to add the condition
```scheme
rule hello_world_checker{
	strings:
		$hello_world = "Hello World!"
	condition:
		$hello_world
}
```

This only checks for exactly "Hello World!" though and no other variation. You can define multiple strings and then give the `any of them` condition.

```scheme
rule hello_world_checker{
	strings:
		$hello_world = "Hello World!"
		$hello_world_upper = "HELLO WORLD!"
		$hello_world_lower = "hello world"
	condition:
		any of them
}
```

###### Conditions
We have already used `true` and `any of them` condition. You can also used normal programming operators like `<=`, `>=` and `!=`.

```scheme
rule hello_world_checker{
	strings:
		$hello_world = "Hello World!"
	condition
		$hello_world <= 10
}
```

Now this rule will only match is there is 10 or less occurrences of "Hello World!"

###### Combining keywords
If you wanted to have a rule that match if any `.txt` file has the string "Hello World!" in it you can combine them.

```scheme
rule hello_world_checker{
	strings:
		$hello_world = "Hello World!"
		$text_file = ".txt"
	condition:
		$hello_world and $text_file
}
```

![](https://miro.medium.com/max/875/1*gThGNPenpT-AS-gjr8JCtA.png)

### Task 7 Yara Modules
Frameworks such as Cuckoo Sandbox or Python's PE Module allows from improvement in the technicality of yara rules

###### Cuckoo
Is an automated malware analysis environment and can generate yara rules based upon the behaviours discovered from Cuckoo Sandbox

###### Python PE
Allows you to create Yara rules from various sections and elements of the Windows Portable Executable (PE) structure.

### Task 8 Other tools and Yara
LOKI is a free open source IOC (Indicator of Compromise) scanner
Detection is based on 4 methods:
- File Name IOC check
- Yara Rule Check
- Hash Check
- C2 Back Connect Check

THOR Lite is Florian's newest multi-platform IOC AND YARA scanner.

FENRIR is a bash script that will run on any system capable of running bash. Simple IOC Checker

YAYA (Yet Another Yara Automation)
Helps manage multiple YARA rule repositories and starts by importing a set of high-quality YARA rules and then lets researchers add their own rules. Will only run on Linux systems.

### Task 9 Using LOKI and its Yara rule set
To Run -> `python loki.py -h` for options

If running Loki on your own system, the first command should be `--update`. This will add the `signature-base` directory, which Loki uses to scan for known evil.

To run -> `python loki.py -p <path>`

### Task 10 Creating Yara rules with yarGen
Because file2 came back as benign but it is clearly a PHP web shell, we can create a new rule using yarGen for it. To create a new Yara rule for file2, use the command:

`python3 yarGen.py -m /home/cmnatic/suspicious-files/file2 --excludegood -o /home/cmnatic/suspicious-files/file2.yar`
- `-m` is the path to the files you want to generate rules for
- `--excludegood` force to exclude all goodware strings
- `-o` output for the Yara rule

### Task 11 Valhalla
Valhalla is an online Yara feed created and hosted by Nextron-Systems. Per the website, "VALHALLA boosts your detection capabilities with the power of thousands of hand-crafted high-quality Yara rules"




