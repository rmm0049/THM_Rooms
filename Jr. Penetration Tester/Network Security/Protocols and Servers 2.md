# Protocols and servers 2

### Intro
Servers implemeneting protocols are subject to different kinds of attacks.
1. Sniffing Attack (Network Packet Capture)
2. Man-in-the-Middle (MITM) attack
3. Password Attack (Authentication Attack)
4. Vulnerabilities

From a security perspective, we always need to think about wht we aim to protect; consider the security triad: CIA (Confidentiality, Integrity, Availability).

The inverse is the DAD (Disclosue, Alteration, Destruction) which are the opposites of the CIA components

### Sniffing Attack
Sniffing attack refers to using a network packet capture tool to collect info about the target. When a protocol communicates in cleartext, the data exchanged can be captured by a third party to analyze.

Can be conducted using an Ethernet (802.3) network card, provided that the user has proper permissions.

1. **tcpdump**
2. **Wireshark**
3. **tshark**

Consider checking email using POP3. First, we are going to use `tcpdump` to attempt to capture the username and password. In terminal, using `sudo tcpdump port 110 -A` will listen for traffic on port 110 with ascii formating.

### Man In The Middle Attack (MITM)
Taking traffic and intercepting and forwarding the traffic onwards as well as in the reverse

Ettercap and Bettercap are tools for this type of attack.

MITM can also affect other cleartext protocols such as FTP, SMTP, and POP3. Mitigation against this attack requires the use of cryptography. The solution lies in proper authentication along with encryption or signing of the exchanged messages. With the help of Public Key Infrastructure (PKI) and trusted root certificates, Transport Layer Security (TLS) protects from MITM attacks.

### Transport Layer Security (TLS)
SSL (Secure Sockets Layer) started when the world wide web stated to see new applications, such as online shopping and sending payment information. Netscape introduced SSL in 1994, with SSL 3.0 being released in 1996. TLS was introduced in 1999

Protocol -> Default Port -> Secured Protocol -> Default Port with TLS
- HTTP -> 80 -> HTTPS -> 443
- FTP -> 21 -> FTPS -> 990
- SMTP -> 25 -> SMTPS -> 465
- POP3 -> 110 -> POP3S -> 995
- IMAP -> 143 -> IMAPS -> 993

![[Pasted image 20220418184913.png]]


After establishing a TCP connection with the server, the client establishes an SSL/TLS connection, as shown in the figure above.
1. Client sends a ClientHello to the server to indiciate its capabilities, such as supported algorithms
2. The server responds with a ServerHello, indicating the selected connection parameters. The server provides its certificate if server authentication is required. The certificate is a digital file to identify itself; it is usually digitally signed by a third party. Moreover, it might send additional information necessary to generate the master key, in its ServerKeyExchange messge, before sending the ServerHelloDone message to indicate that it is done with the negotiation.
3. Client responds with a ClientKeyExchange, which contains additional information required to generate the master key. Furthermore, it switches to use encryption and informs the server using the ChangeCipherSec message
4. The server switches to use encryption as well and informs the client in the ChangeCipherSec message

### Secure Shell (SSH)

Secure Shell (SSH) was created to provide a secure way for remote sysadmins. In other words, you can securely connect to another system over the network and execute commands on that system.

To use SSH, you need a SSH server and a SSH client. The SSH server typically listens on port 22 by default. The SSH client can authenticate using:
- A username and a password
- A private and public key

Note that if it's the first time you're connecting to a machine, you will need to confirm the fingerprint of the SSH server's public key to avoid a man-in-the-middle (MITM) attack.

![[Pasted image 20220428090407.png]]

We can use SSH to transfer files using SCP (Secure Copy Protocol) based on the SSH protocol. An example syntax is `scp mark@10.10.36.82:/home/mark/archive.tar.gz ~`

As a closing note, FTP could be secured using SSL/TLS by using the FTPS protocol that uses port 990. FTP can also be secured using the SSH protocol which is the SFTP protocol, by default this service listens on port 22, just like SSH

### Password Attack

...


### Summary
| Protocol | TCP Port | Application(s) | Data Security |
|------------|------------|--------------------|-------------------|
| FTP                      | 21                | File Transfer     | Cleartext
| FTPS                     | 990               | File Transfer     | Encrypted
| HTTP                     | 80                | Web               | Cleartext





