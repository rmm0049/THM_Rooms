### Scans
- Null Scan
- FIN Scan
- Xmas Scan
- Maimon Scan
- ACK Scan
- Window Scan
- Custom Scan

Also you can do:
- Spoofing IP
- Spoofing MAC
- Decoy Scan
- Fragmented Packets
- Idle/Zombie Scan

### TCP Null Scan, FIN Scan and XMAS Scan

**Null Scan**
The null scan does not set any flag; all six flag bits are set to zero. This can be specified using the `-sN` option. A TCP packet with no flags set will not trigger any response when it reaches an open port, as shown in the figure below. Therefore, from Nmap's perspective, a lack of reply in a null scan indicates that either the port is open or a firewall is blocking the packet

However, we expect the target server to respond with an RST packet if the port is closed. Consequently, we can use the lack of RST response to figure out the ports that are not closed: open or filtered

`nmap -sN TARGET`

**FIN Scan**
The FIN scan sends a TCP packet with the FIN flag set. You can choose this scan type using `-sF` option. Similarly, no response will be sent if the TCP port is open. Again, Nmap cannot be sure if the port is open or if a firewall is blocking the traffic related to this TCP port

However, if the target system should respond with an RST if the port is closed. Consequently, we will be able to know which ports are closed and use this knowledge to infer the ports that are open or filtered. It's worth noting some firewalls will 'silently' drop the traffic without sending an RST.

**Xmas Scan**
The Xmas scan gets its name after Christmas tree lights. An Xmas scan sets the FIN, PSH, and URG flags simultaneously.

Can be used with `-sX` option.

Like the null scan and FIN scan, if an RST packet is received, it means that the port is closed. Otherwise, it will be reported as open|filtered.

### TCP Maimon Scan
Uriel Maimon first described this scan in 1996, the FIN and ACK bits are set. The target should send an RST packet as a response. However, certain BSD-derived systems drop the packet if it is an open port exposing the open ports. This scan won't work on most targets encountered in modern networks; however, it's included to better understand port scanning mechanism.

Can be used with `-sM` option

### TCP ACK , Window and Custom Scan

**TCP ACK Scan**
Will send a TCP packet with the ACK flag set. Uses the `-sA` option. It's expected to receive a RST regardless of the state of the port. This behavior happens because a TCP packet with the ACK flag set should be sent only in response to a received TCP packet to acknowledge receipt of some data.

Is helpful if there is a firewall in front of the target. Consequently, based on which ACK packets resulted in responses, you will learn which ports were not blocked by the firewall.

**Windows Scan**
The TCP window scan is almost the same as the ACK scan; however, it examines the TCP window field of the RST packets returned. On specific systems, this can reveal that the port is open.

Uses the `-sW` option. We expect to get an RST packet in reply to our "uninvited" ACK packets, regardless of whether the port is open or closed.

Can be useful when firewalls are in place

**Custom Scan**
If you want to experiment with a new TCP flag combination beyond the built-in TCP scan types, you can do so using `--scanflags`. Example is `--scanflags RSTSYNFIN`

`nmap --scanflags CUSTOM_FLAGS TARGET`

### Spoofing and Decoys
In some network setups, you will be able to scan a target system using a spoofed IP address and even a spoofed MAC address. Such a scan is only beneficial in a situation where you can guarantee to capture the response.

Ex) `nmap -S SPOOFED_IP TARGET`

1. Attacker sends a packet with a spoofed source IP address to target machine
2. Target machine replies to the spoofed IP address as the destination
3. Attacker captures the replies to figure out open ports

In general, you expect to specify the network interface using `-e` and to explicity disable ping scan `-Pn`. Therefore, instad of `nmap -S SPOOFED TARGET` you will need to issue `nmap -e NET_IFACE -Pn -S SPOOFED_IP TARGET`

If you're on the same subnet as the target machine, you would be able to spoof your MAC address as well using `--spoof-mac SPOOFED_MAC`.

Spoofing only works in a minimal number of cases where certain conditions are met.

You can launch a decoy scan by specifying a specific or random IP address after the `-D` flag.

Ex) `nmap -D 10.10.0.1,10.10.0.2,ME 10.10.255.96`, the `ME` indicates that your IP address should appear in the 3rd order. You can also add `RND` to do a random IP

### Fragmented Packets

**Firewall**
Software or Hardware that permits packets to pass through or blocks them. It functions based on rules, blocking all traffic with exceptions or allowing all traffic with exceptions.

**IDS**
An intrusion detection system (IDS) inspects network packets for select behavioral patterns or specific content signatures. It raises an alert whenever a malicious rule is met.

**Fragmented Packets**
Nmap provides the option `-f` to fragment packets. Once chosen, the IP data will be divided into 8 bytes or less. Adding another `-f` (`-f` or `-ff`) will split the data into 16 byte-fragments instead of 8. You can change the default value by using `--mtu`

![[Pasted image 20220405185410.png]]

### Idle/Zombie Scan
Spoofing the source IP address can be a great approach to scanning stealthily. However, spoofing will only work in specific network setups. It requires you to be in a position where you can monitor the traffic. Considering these limitations, spoofing your IP address can have little use; however, we can give it an upgrade with the idle scan.

The idle scan, or zombie scan, requires an idle system connected to the network that you can communicate with. Nmap will make each probe appear as if coming from the idle (zombie) host, then it will check for indicators whether the idle host received any response to the spoofed probe. This is accomplished by checking the IP identification (IP ID) value in the IP header.

`nmap -sI ZOMBIE_IP TARGET`

To discover whether or not a port is open:
1. Trigger the idle host to respond so that you can record the current IP ID on the idle host.
2. Send a SYN packet to a TCP port on the target. The packet should be spoofed to appear as if it was coming from the idle host (zombie) IP address.
3. Trigger the idle machine again to respond so that you can compare the new IP ID with the on received earlier.

### Getting More Details
You might consider adding the `--reason` option to provide details on why Nmap provided the port detail

### Summary
Port Scan Type

Example Command

TCP Null Scan

`sudo nmap -sN 10.10.64.175`

TCP FIN Scan

`sudo nmap -sF 10.10.64.175`

TCP Xmas Scan

`sudo nmap -sX 10.10.64.175`

TCP Maimon Scan

`sudo nmap -sM 10.10.64.175`

TCP ACK Scan

`sudo nmap -sA 10.10.64.175`

TCP Window Scan

`sudo nmap -sW 10.10.64.175`

Custom TCP Scan

`sudo nmap --scanflags URGACKPSHRSTSYNFIN 10.10.64.175`

Spoofed Source IP

`sudo nmap -S SPOOFED_IP 10.10.64.175`

Spoofed MAC Address

`--spoof-mac SPOOFED_MAC`

Decoy Scan

`nmap -D DECOY_IP,ME 10.10.64.175`

Idle (Zombie) Scan

`sudo nmap -sI ZOMBIE_IP 10.10.64.175`

Fragment IP data into 8 bytes

`-f`

Fragment IP data into 16 bytes

`-ff`

Option

Purpose

`--source-port PORT_NUM`

specify source port number

`--data-length NUM`

append random data to reach given length

These scan types rely on setting TCP flags in unexpected ways to prompt ports for a reply. Null, FIN, and Xmas scan provoke a response from closed ports, while Maimon, ACK, and Window scans provoke a response from open and closed ports.

Option

Purpose

`--reason`

explains how Nmap made its conclusion

`-v`

verbose

`-vv`

very verbose

`-d`

debugging

`-dd`

more details for debugging