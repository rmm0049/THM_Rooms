### Intro
Defining the difference between passive and active reconnaissance, involving the tools:
- `whois` to query WHOIS servers
- `nslookup` to query DNS servers
- `dig` to query DNS servers

We use `whois` to query WHOIS records, while we use `nslookup` and `dig` to query DNS database records. These are all publicly available records and hence do not alert the target.

We can also use two online services:
- DNSDumpster
- Shodan.io

### Passive Versus Active Recon
Recon can be defined as a preliminary survey to gather information about a target. It is the first step in **The Unified Kill Chain** to gain an initial foothold on a system. We divide recon into:
- Passive
- Active

Passive recon relies on publicly available knowledge. It is the knowledge that you can access from publicly available resources without directly engaging with the target.

May involve:
- Looking up DNS records of a domain from a public DNS server
- Checking job ads related to the target website
- Reading news articles about the target company.

Active, cannot be achieved so discreetly. It requires direct engagement with the target. Think of it like you check the locks on the doors and windows, among other potential entry points.

Examples of active recon:
- Connecting to one of the company servers such as HTTP, FTP, and SMTP.
- Calling the company in an attempt to get info (Social Engineering)
- Entering company premises pretending to be a repairman

### whois
A request and response protocol that follows the RFC 3912 specification. A WHOIS server listens on TCP port 43 for incoming requests. The domain registrar is responsible for maintaining the WHOIS records for the domain names it is leasing. The WHOIS server replies with various information related to the domain requested. Of particular interest, we can learn:

- Registrar: which registrar was the domain name registered?
- Contact info of registrant
- Creation, update, and expiration dates
- Name Server

To get this info, we need to use a `whois` client or an online service. Many online services provide `whois` info; however, it is generally faster and more convenient to use your local whois client.

ex) `whois tryhackme.com`

We get back plenty of info; after inspecting them in the order, we see that we were redirected to `whois.namecheap.com` to get our info. In this case and at the time being, `namecheap.com` is maintaining the WHOIS record for this domain name.

### nslookup and dig
Find the IP addres of a domain name using `nslookup`, which stands for name server look up. You need to issue the command `nslookup DOMAIN_NAME`, or more generally `nslookup <options> <domain> <server>`

DNS queries:

- A -> IPv4 addresses
- AAAA -> IPv6 addresses
- CNAME -> Canonical Name (Alias)
- MX -> Mail Servers
- SOA -> Start of Authority
- TXT -> TXT Records

ex) `nslookup -type=A tryhackme.com 1.1.1.1`

### DNSDumpster
DNS lookup tools like *dig* and *nslookup*, cannot find subdomains on their own. The domain you're inspecting might include a different subdomain that can reveal much information about the target. For instance, if tryhackme.com the subdomains wiki.tryhackme.com and webmail.tryhackme.com, you want to learn more about these two as they can hold a trove of info about the target.

DNSDumpster can be used to find a lot more information about a domain name.

### Shodan.io
A service shodan.io can be helpful to learn various pieces of information about the client's network, without actively connecting to it. Furthermore, on the defensive side, you can use different services from Shodan.io to learn about connected and exposed devices belonging to your organization.

