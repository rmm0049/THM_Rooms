### Intro
Covers:
- HTTP
- FTP
- POP3
- SMTP
- IMAP

### Telnet
The Telnet protocol is an application layer protocol used to connect to a virtual terminal of another computer.

Telnet protocol is relatively simple. When a user connects, they will ask for a username and password. Upon correct authentication, the user will access the remote system's terminal. Unfortunately, all this communication between the client and server is in cleartext.

A Telnet server listens on port 23.  Example, `telnetd`, a telnet server

1. First, you're asked to provide a username.
2. Then, you're asked for a password
3. Once the system checks the loginc credentials, you're greeted with a welcome message
4. The remote server grants you a command prompt

### Hypertext Transfer Protocol (HTTP)
This protocol is used to transfer web pages. Anytime you browse the World Wide Web (WWW), HTTP is used

HTTP sends and receives data as cleartext (not encrypted); therefore, you can use a simple tool such as Telnet or Netcat to communicate with a web server and act as a "web browser".

1. First, connect to port 80 using `telnet <ip> 80`
2. Next, need to type `GET /index.html HTTP/1.1` to retrieve the page `index.html` or `GET / HTTP/1.1` to retrieve the default page
3. Finally, need to provide some value for the host such as `host: telnet`, press Enter/Return twice

Popular HTTP servers include:
- Apache
- Internet Information Services (IIS)
- nginx

Apahce and nginx are both open source software; however, IIS is closed source software from Microsoft

### File Transfer Protocol (FTP)
FTP was developed to make the transfer of files between different computers with different systems efficient.

FTP also sends and receives data as cleartext; therefore, we can use telnet or Netcat to communicate with a FTP server and act as a FTP client.

Ex)
1. Try to `telnet <ip> 21`
2. Provide a username with `USER frank`
3. Provide a password with `PASS D2xc0CgD`

A command like `STAT` can provide some added information. The `SYST` command shows the System Type of the target. `PASV` switches the mode to passive. It is worth noting that there are two modes of FTP

- Active: data is sent over a separate channel originating from the FTP server's port 20.
- Passive: Data is sent over a separate channel originting from a FTP client's port above port number 1023

The command `TYPE A` swithces the file transfer mode to ASCII, while `TYPE I` switches the file transfer mode to binary. However, we cannot transfer a file using a simple client such as Telnet because FTP creates a separate connection for file transfer.

Ex. of FTP server software:
- vsftpd
- ProFTPD
- uFTP

### Simple Mail Transfer Protocol (SMTP)
Email is one of the most used services on the Internet. There are various configurations for email servers; for instance, you may set up an email system to allow local users to exchange emails with each other with no access to the Internet.

Email delivery over the Internet requires the following components:
1. Mail Submission Agent (MSA)
2. Mail Transfer Agent (MTA)
3. Mail Delivery Agent (MDA)
4. Mail User Agent (MUA)

The above four items may look cryptic, but they are move straightforward than thought

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/822a449fd569c16c875a13ca2487b714.png)

1. A Mail User Agent (MUA) or simply an email client, has an email message to be sent. The MUA connects to a Mail Submission Agent (MSA) to sent its message
2. The MSA receives the message, checks for any errors before tranferring it to the Mail Transfer Agent (MTA) server, commonly hosted on the same server
3. The MTA will send the email message to the MTA of the recipient. The MTA can also function as a Mail Submission Agent (MSA
4. A typical setup would have the MTA server also functioning as a Mail Delivery Agent (MDA)
5. The recipient will collect is email from the MDA using their email client.

In the same way, we need to follow a protocol to communicate with an HTTP server, and we need to rely on email protocols to talk with a MTA and an MDA. The protocols are:
1. Simple Mail Transfer Protocol (SMTP)
2. Post Office Protocol Version 3 (POP3) or Internet Message Access Protocol (IMAP)

Once connected, you issue `helo hostname` and then start typing out the email

After the `helo`, you issue `mail from:`, `rcpt to:` to indicate sender and the recipient. Issue `data` to type the content of the email. Then `<CR><LF>.<CR><LF>` or `Enter . Enter` to queue the message

### Post Office Protocol 3 (POP3) port 110
POP3 is used to download the email messages from a Mail Delivery Agent server. The mail client connects to the POP3 server, authenticates, downloads the new email messages before (optionally) deleting them.

After connecting on port 110, Authentication is required to access the email messages; the user authenticates by providing his username `USER frank` and password `PASS D2xc9CgD`. Using the command `STAT`, we get the reply `+OK 1 179`; based on RFC 1939, a positive response to `STAT` has the format `+OK nn mm`, where *nn* is the number of email messages in the inbox, and *mm* is the size of the inbox in octets (byte). THe command `LIST` provided a list of new messages on the server, and `RETR 1` retrieved the first message in the list.

In order to keep track and make sure all inboxes are synchronized, we need to consider other protocols, such as IMAP

### Internet Message Access Protocol (IMAP) port 143
IMAP is more sophisticated than POP3. IMAP makes it possible to keep your email synchronized across multiple devices (and mail clients). In other words, if you mark an email message as read when checking your email on your smartphone, the change will be saved on the IMAP server (MDA) and replicated on your laptop when you synchronize your imbox.

Simple IMAP commands
- `LOGIN username password`
- IMAP requires each command to be preceded by a random string to be able to track the reply so we added `c1`, `c2` and so on
- Then listing mail folder `LIST "" "*"`
- New messages using `EXAMINE INBOX`

