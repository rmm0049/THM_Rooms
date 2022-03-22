# Active Reconnaissance
### Web Browser
There are several ways where you can use a web browser to gather information about a target.

On the transport level, the browser connects to:
- TCP port 80 by default when the website is accessed over HTTP
- TCP port 443 by default when the website is accessed over HTTPS

It is possible for web traffic to be served on non-standard ports. For instance, https://127.0.0.1:8834/ will connect to the ip address at port 8834.

While browsing a web page, you can press `Ctrl+Shift+I` on PC or `Option+Command+I` on a Mac to open the developer tools on Firefox.

There are also plenty of add-ons for Firefox and Chrome that can help in penetration testing. Here are a few examples:
- **FoxyProxy** lets you quickly change the proxy server you are using to access the target website. Often used with Burp Suite
- **User-Agent Switcher and Manager** gives you the ability to pretend to be accessing the webpage from a different operating system or different web browser.
- **Wappalyzer** provides insights about the technologies used on the visited sites.

### Ping
The ping command sends an ICMP Echo packet to a remote system. If the remote system responds to ICMP traffic, and the packet was correctly routed, the remote system should send back an ICMP echo reply packet.

`ping <ip-addr>` or `ping [-c|-n <num>] <ip-addr>`

### Traceroute
The `traceroute` or `tracert` on Windows *traces* the route taken by the packets from your system to another system. The purpose is to find the IP addreses of the routers or hops that a packet traverses as it goes from your system to a target host.

### Telnet
The TELNET (Teletype Network) protocol was developed in 1969 to communicate with a remote system via a command line interface. `telnet` sends all the data, including usernames and passwords in cleartext.

`telnet <ip> PORT`

### Netcat
Netcat or simply `nc` has different applications that can be of great value to a pentester. Netcat supports both TCP and UDP protocols. It can function as a client that connects to a listening port; alternatively it can act as a server that listens on a port of your choice. Hence, it is a convenient tool that you can use a simple client or server over TCP or UDP.

`nc <ip> PORT`

**Options**:
- -l -> listen mode
- -p -> specify port number
- -n -> numeric only; no resolution of hostnames via DNS
- -v -> verbose output
- -vv ->  very verbose
- -k -> keeps listening after client disconnects
