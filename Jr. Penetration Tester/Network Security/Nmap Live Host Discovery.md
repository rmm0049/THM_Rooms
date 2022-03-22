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

