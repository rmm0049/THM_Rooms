# Nmap Live Host Discovery
Helps handle the questions:
1. Which systems are up?
2. What services are running on these systems?

Different approahces that Nmap uses to discover live hosts:
1. ARP scan: uses ARP requests to discover live hosts
2. ICMP scan: uses ICMP requests to identify live hosts
3. TCP/UDP ping scan: sends packets to TCP ports and UDP ports to determine live hosts

ARP Requests are tied to their subnet.

### Enumerating Targets
There's different techniques we can use for scanning.

- `IP scanme.nmap.org example.com` -> will scan 3 IPs
- range: `10.11.12.15-20` will scan 6 IPs
- subnet: `10.11.12.0/24` -> will scan 255 IPs

Can also provide a file as input

`nmap -iL <listOfHosts.txt>`

`nmap -sL TARGETS` => will show what hosts nmap will scan, attempting a reverse-DNS resolution on all targets. If you want to disable, `-n`

### Discovering Live Hosts
For live hosts we can use:

- ARP from Link Layer
- ICMP from network layer
- TCP from transport layer
- UDP from transport layer

### nmap Host Discovery Using ARP
How would you know which hosts are up and running? It is essential to avoid wasting time on port-scanning an offline host or IP address not in ue.

1. When a privileged user tries to scan targets on a local network (Ethernet), Nmap uses ARP requests. A privleged user is root who belongs to sudoers and can run sudo.
2. When a privileged user tries to scan targets outside the local network, Nmap uses ICMP echo requests, TCP ACK to port 80, TCP SYN to port 443 and ICMP timestamp request.
3. When an unprivileged user tries to scan targets outside of the network range, Nmap resorts to a TCP 3-way handshake by sending SYN packets to port 80 and 443


Nmap, be default uses a ping scan to find live hosts, then proceeds to scan live hosts only. if you want to use Nmap to discover online host without port-scanning the live systems, use `nmap -sn TARGETS`.

ARP scan is only possible if you are on the same subnet as the target systems. on an Ethernet (802.3) and WiFi (802.11), you need to know the MAC address of any system before you can communicate with it.

If you want Nmap to perform an ARP scan without port-scanning you can use `nmap -PR -sn TARGETS` where `-PR` indicates that you only want an ARP scan. The following example shows Nmap using ARP for host discovery without port scanning `nmap -PR -sn <IP>/24`.

Talking about ARP scans, you can use a built in ARP scanner `arp-scan`; it provides many options customize your scan. A popular choice is `arp-scan --localnet` or simply `arp-scan -l`. This will send ARP requests to all valid IP addresses on your local network. You could also specify the specific interface you want `sudo arp-scan -I eth0 -l` will send ARP requests on the localnet using interface `eth0`.


### Nmap Host discovery using ICMP
We can ping every IP address on a target network and see who would respond to our `ping` (ICMP Type 8/Echo) requests with a ping reply (ICMP Type 0). This would be good to use; however, modern Windows OS machines have ICMP traffic blocked by the firewall by default so the host would appear to be down.

Echo requests
`nmap -PR -sn TARGET`

Because ICMP echo requests tend to be blocked, you might also consider ICMP Timestamp or ICMP Address Mask requests to tell if a system is online. Nmap uses timestamp request (ICMP Type 13) and checks whether it will get a Timestamp reply (ICMP type 14). Adding the `-PP` option tells Nmap to use ICMP timestamp requests. 

`nmap -PP -sn MACHINE_IP/24`

Similarly, Nmap uses address mask queries (ICMP Type 17) and checks whether it gets an address mask reply (ICMP Type 18). This scan can be enabled with the `-PM`.

`nmap -PM -sn TARGET`


### Nmap Host Discovery Using TCP and UDP

**TCP SYN Ping**
We can send a packet with the SYN (Synchronize) flag set to a TCP port, 80 by default, and wait for a response. An open port should reply with a SYN/ACK (Acknowledge); a closed port would respond with a RST packet (reset).

If you want Nmap to use TCP SYN ping, you can do so via option `-PS` followed by the port number range, list, or a combination of them. For example, `-PS21` will target port 21, while `-PS21-25` will target ports 21,22,23,24, and 25.

Privileged users can send TCP SYN packets and don't need to complete the TCP 3-way handshake even if the port is open

`nmap -PS -sn TARGET`

**TCP ACK ping**
As you have guessed, this sends a packet with an ACK flag set. You must be running Nmap as a privileged user to be able to acomplish this.

By default, port 80 is used. The syntax is `-PA` followed by a port range if you want.

**UDP Ping**
You can use UDP to discover if a host is online. Contrary to TCP SYN ping, sending a UDP packet to an open port is not expected to lead to any reply. However, if we send a UDP packet to a closed UDP port, we expect to get an ICMP port unreachable packet; indicating the target system is up and available.

A UDP packet sent to an open UDP port and not triggering any response. However, sending a UDP packet to any closed UDP port can trigger a response indirectly indicating that the target is online.

`nmap -PU -sn TARGET`

**Masscan**
On a side note, Masscan uses a similar approach to discover the available systems. However, to finish its network scan quickly, Masscan is quite aggresive with the rate of packets it generates. The syntax quite similar: `-p` can be followed by a port number, list, or range. Consider:
- `masscan IP/24 -p443`
- `masscan IP/24 -p80,443`
- `masscan IP/24 -p22-25`
- `masscan IP/24 --top-ports 100`

### Using Reverse-DNS Lookup
Nmap's default behaviour is to use reverse-DNS online hosts. Because the hostnames can reveal a lot, this can be a helpful step. However, if you don't want to send such DNS queries, you can use `-n` to skip this step.

By default, Nmap will look up online hosts; however, you can use the option `-R` to query the DNS server even for offline hosts. If you want to use a specific DNS server, you can add the `--dns-servers DNS_SERVER` option.

### Summary
- ARP Scan => `sudo nmap -PR -sn TARGET`
- ICMP Echo Scan => `sudo nmap -PE -sn TARGET`
- ICMP Timestamp Scan => `sudo nmap -PP -sn TARGET`
- ICMP Address Mask Scan => `sudo nmap -PM -sn TARGET`
- TCP SYN ping => `sudo nmap -PS22,80 -sn TARGET`
- TCP ACK ping => `sudo nmap -PA -sn TARGET`
- UDP ping => `sudo nmap -PU -sn TARGET`

