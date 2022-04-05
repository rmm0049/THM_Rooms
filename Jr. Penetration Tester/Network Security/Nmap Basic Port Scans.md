# Nmap Basic Port Scans

### Intro
The next step, would be checking which ports are open and listening and which ports are closed. Therefore, this covers port scanning and the different types of port scans used by `nmap`.

- TCP connect port scan
- TCP SYN port scan
- UDP port scan

### TCP and UDP ports
 A TCP port or UDP port is used to identify a network service running on a host. At risk of oversimplification, we can classify ports in two states:
1. Open port indicates that there is some service listening on that port
2. Closed port indicates that there is no service listening on that port

However, in practical situations, we need to consider the impact of firewalls. For instance, a port might be open, but a firewall might be blocking packets, so in reality, there's 6 states:

1. **Open**: indicates that a service is listening on the specified port
2. **Closed**: indicates that no service is listening on the specified port, although the port is accessible.
3. **Filtered**: means that Nmap cannot determine if the port is open or closed because is not accessible. There is no response
4. **Unfiltered**: means that Nmap cannot determine if the port is open or closed, although the port is accessible. This state is encountered when using an ACK scan `-sA`
5. **Open|Filtered**: cannot determine whether port is open or filtered
6. **Closed|Filtered**: cannot decide whether port is closed or filtered.

### TCP flags
The TCP header is the first 24 bytes of a TCP segment. The following figure shows the TCP header as defined in RFC 793.

![[Pasted image 20220327170021.png]]

In particular we need to focus on the flags that Nmap can set or unset.

1. **URG**: Urgent flag indicates that the urgent pointer filed is significant. The urgent pointer indicates that the incoming data is urgent, and that a TCP segment with the URG flag set is processed immediately without consideration of having to wait on previously sent TCP segments.
2. **ACK**: Acknowledgement flag indicates that the acknowledgement number is significant. It is used to acknowledge the receipt of a TCP segment.
3. **PSH**: Push flag asking TCP to pass the data to the application promptly
4. **RST**: Reset flag is used to reset the connection. ANother device, such as a firewall, might send it to tear a TCP connection. This flag is also used when data is sent to a host and there is no service on the receiving end to answer (closed port)
5. **SYN**: Synchronize flag is used to initiate TCP 3-way handshake and synchronize sequence numbers with the other host. The sequence number should be set randomly during TCP connection establishment
6. **FIN**: The sender has no more data to send

### TCP Connect Scan
TCP connect scan works by completing the TCP 3-way handshake.

`nmap -sT TARGET`

It is important to note that if you are not privileged user, a TCP connect scan is the only possible option to discover open TCP ports.

A `RST/ACK` packet is sent back to indicate a port is closed

`-r` option can be used to scan the ports in consecutive order instead of random order.

### TCP SYN Scan
Unprivileged users cannot run this scan. SYN scan does not need to complete the 3-way handshake; instead, it tears down the connection once it receives a response from the server.

`nmap -sS TARGET`

The difference here is that if the client gets back a SYN/ACK packet, the client does not send an ACK back but rather a RST packet.

### UDP Scan
UDP is a connectionless protocol and hence it does not require any handshake for connection. We cannot guarantee that a service listening on a UDP port would respond to our packets. Howeer, if a UDP packet is sent to a closed port, an ICMP port unreachable (type 3, code 3) is returned. You can select UDP scan using the `-sU` option; moreover, you can combine it with another TCP scan

`nmap -sU TARGET

### Fine-Tuning Scope and Performance
You can specify the ports you want to scan instead of the default 1000 ports. Specifying the ports is intuitive by now. Let's see some examples

- port list: `-p22,80,443`
- port range: `-p1-1023`

To scan all ports `-p-`, to scan top 100 ports `-F`, or any top ports amount `--top-ports <num>`

Scan timing and aggresiveness `-T<0-5>`

- paranoid (0)
- sneaky (1)
- polite (2)
- normal (3)
- aggressive (4)
- insane (5)

To avoid IDS alerts, you might consider `T0` or `T1`

Alternatively you can choose to control the packet rate using `--min-rate <number>` and `--max-rate <number>`.

Moreover, you can control probing parallelization using `--min-parallelism <numprobes>` or max version





