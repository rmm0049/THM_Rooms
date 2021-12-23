# Zero Logon 
### Task 1 The Zero Day Angle
The ZeroLogon vulnerability is approached from a "Proof of Concept" emphasis, providing a breakdown of the vulnerable method within this issue.

##### Zero Logon - The Zero Day Angle
On September 14th, Secura released a whitepaper for CVE-2020-1472, that allowed an attacker to go from Zero to Domain Admin in approximately one minute.

Zero Logon is purely statistics based attack that abuses a feature within MS-NRPC (Microsoft NetLogon Remote Protocol), MS-NRPC is a critical authentication component of Active Directory that handles authentication for User and Machine accounts. In short - the attack mainly focuses on a poor implementation of Cryptography. To be specific Microsoft chose to use AES-CFB8 for a function called ComputeNetlogonCredential, except they hard coded the Initialization vector to use all zeroes instead of a random string. When an attacker sends a message only containing zeroes with the IV of zero, there is a 1-i-256 chance that the Ciphertext will be zero.

##### About Machine Accounts
Normally, if we tried a statistics based attack on any user account, we would get locked out. This is not the case if we apply this principle to machine accounts. Machine accounts behave in a much different way than standard user accounts. They have no predefined account lockout attempts because a 64+ character alphanumeric password is normally used to secure them, making them very difficult to break into.

##### Abusing the Vulnerability
Machine accounts often hold system level privileges which we can use for a variety of things. If you're not familiar with Active Directory, we can take a Domain Controller's Machine Account and attempt to use the granted authentication in conjunction with Secretsdump.py (SecretsDump is a password dumping utility like Mimikatz, except it lives on the Network instead of the host)

Use Zero Logon to bypass authentication on the Domain Controller's Machine Account -> Run SecretsDump.py to dump credentials -> Crack/Pass Domain Admin Hahshes -> ??? -> Profit

##### Analyzing the MS-NRPC Logon Process
We know there's vulnerability in the way that Microsoft handles Authenitcation within ComputeNetlogonCredential function of MS-NPRC.

![](https://www.zdnet.com/a/hub/i/2020/09/11/91ce3485-5a9b-4fd7-9bdb-908084954c58/zerologon-attack.png)

**Step 1** The client creates a NetrServerReqChallenge and sends it off. This contains the following values:
1. The DC
2. The Target Device (Also the DC in this case)
3. A Nonce (in our case 16 bytes of 0's)

**Step2** The server receives te NetrServerReqChallenge, the server will then generate it's own Nonce (This is called the Server Challenge), the server will send the Server Challenge back.

**Step 3** The client will compute its NetLogon Credentials with the Server Challenge provided. It uses the NetrServerAuthenticate3 method which requires the following paramters
1. A Custom Binding Handle (Impacket handles this for us, it's negotiated prior)
2. An Account Name (The DC's machine account name. ex: DC01$)
3. A Secure Channel Type (Impacket sort of handles this, but we still need to specify it: [nrpc.NETLOGON_SECURE_CHANNEL_TYPE:ServerSecureChannel]. 
4. The Computer Name (The Domain Controller ex: DC01)
5. The Client Credential String (8 hextets of \x00 [16 bytes of Zero])
6. Negotiation Flags (The following value observed from Win10 client with Sign/Seal flags disabled: 0x212fffff)

**Step 4** The server will receive the NetrServerAuthenticate request and will compute the same request itself using its known, good values. If the results are good, the server send the required info back to the client.

At this point the Zero Logon vulnerability is on its way. The above steps above will be looped through a certain number of times to attempt to exploit the Zero Logon vulnerability. The actual exploit occurs at Step 3 and 4, this is where we're hoping for the Server to have the same computations as the client.

**Step 5** If the server computes the same value, the client will re-verify and once mutual agreement is confirmed, they will agree on a session key. The session key will be used to encrypt communications between the client and the server, which means authentication is successful.

From there, normal RPC communications can occur.

### Task 2 Impacket Installation
Git Clone Impacket

```scheme
python3 -m pip install virtualenv
python3 -m virtualenv impacketEnv
source impacketEnv/bin/activate
pip install git+https://github.com/SecureAuthCorp/impacket
```

### Task 3 The Proof of Concept
