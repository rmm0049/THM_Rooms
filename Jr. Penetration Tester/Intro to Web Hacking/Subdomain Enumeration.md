# Subdomain enumeration
### Task 1 Brief
Methods to find subdomains: Brute Force, OSINT, and Virtual Host

### Task 2 OSINT - SSL/TLS Certificates
##### SSL/TLS Certificates
When these certificates are created for a domain by a CA (Certificate Authority), CA's take part in what's called "Certificate Transparency (CT) logs". These are publicly available for every SSL/TLS certificate created for a domain name. The purpose for this transparency is to stop malicious and accidently made certificates from being used. Sites like https://crt.sh and https://transparencyreport.google.com/https/certificates offer a searchable database of certificates.

What domain was logged on crt.sh at 2020-12-26?

### Task 3 OSINT - Search Engines
Search engines contain trillions of links to more than a billion websites, which can be an excellent resource for finding new subdomains. You can you a search technique like `-site:www.domain.com site:*.domain.com` would only contain results leading to the domain name domain.com but to exclude any links to www.domain.com.

### Task 4 DNS Bruteforce
This method includes trying tens, hundreds or thousands of different possible subdomains from a pre-defined list of commonly used subdomains.

### Task 5 OSINT - Sublist3r
This tool will help doing some automated discovery

### Task 6 Virtual Hosts
You can use the tool `ffuf` to enumerate virtual hosts, because the subdomain may not be pubicly available through DNS, so you will never find it. You can modify the Host header with ffuf and FUZZ the virtual hosts

`ffuf -w /usr/share/wordlist -H "Host: FUZZ.domain.com" -u http://<ip>`

You can then filter the size using the `-fs` option to find the ones it actually finds.

