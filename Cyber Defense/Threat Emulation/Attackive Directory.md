# Attacktive Directory
### Task 2 Setup
**Installing Impacket**
Impacket can be a pain to install correctly. Here's some instructions

`git clone https://github.com/SecureAuthCorp/impacket.git /opt/impacket`

After the repo is cloned, you will notice several install related files, requirements.txt, and setup.py. A commonly skipped file during installation is setup.py, this actually installs impacket onto your system so you can use it and not have to worry about any dependencies.

`pip3 install -r /opt/impacket/requirements.txt`

then

`cd /opt/impacket/ && python3 ./setup.py install`

After that, Impacket should be correctly installed and ready to use!

**Installing Bloodhound and Neo4j**
Bloodhound is another tool that we'll utilize while attacking Active Directory. can be installed using `apt` package manager.

### Task 3 Welcome to Attacktive Directory
**Enumeration**
Basic enumeration starts out with a **nmap** scan.

Found ports 135, 445 (SMB), 88 (kerberos-sec), 3389 (RDP)

### Task 4 Enumerating Users in Kerberos
Kerberos is a key authentication service within Active Directory. With this port open, we can use a tool called `Kerbrute` to brute force discovery of users, passwords, and even password spray.

It is not recommended to use brute force credentials because of account lockout policies that we cannot enumerate on the domain controller.

### Task 5 Abusing Kerberos
After the enumeration of the users, we can attempt to abuse a feature within kerberos with an attack method called `ASREPRoasting`. Occurs when a user account has the privilege "Does not require Pre-Authentication" set. This means that the account does not need to provide valid identification before requesting a Kerberos Ticket on the specified user account.

##### Retrieving Kerberos Tickets
Impacket has a tool called `GetNPUsers.py` (in impacket/examples/GetNPUsers.py) that will allow us to query ASReproastable accounts from the Key Distribution Center. The only thing that's necessary to query accounts is a valid set of usernames which we enumerated previously via Kerbrute.

### Task 6 Back to the Basics
With a user's account credentials we now have significantly more access to the domain. We can now attempt to enumerate any shares that the domain controller may be giving out.

### Task 7 Elevating Privileges within the Domain
