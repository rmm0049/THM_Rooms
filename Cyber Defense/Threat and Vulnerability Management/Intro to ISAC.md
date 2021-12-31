# Intro to ISAC
Information Sharing and Analysis Centers (ISACs) are used to share and exchange varius Indicators of Compromise (IOCs) to obtain threat intelligence. IOCs can include MD5s, IPs, YARA rules, and more. There are many ISACs that can be used to gather threat intelligence including AlienValut OTX, Threat Connect, and MISP.

### Task 2 Basic Terminology
**APT** is an acronym for **Advanced Persistant Threat**. This can be considered a team/group or even a county (nation-state group), that engages in long-term attacks against organizations and/or countries.

**TTP** is an acronym for **Tactics, Techniques, and Procedures**
- The **Tactic** is the adversary's goal or objective
- The **Technique** is how the adversary achieves the goal or objective
- The **Procedure** is how the technique is executed.

**IOCs** are the indicators for malware and adversary groups. Indicators can include file hashes, IPs, names, etc.

### Task 3 What is Threat Intelligence
Threat Intelligence, also known as TI and Cyber Threat Intelligence (CTI), is used to provide info about the threat landscape specifically adversaries and their TTPs.

Data must be analyzed to be considered threat intelligence. Once analyzed and actionable, then it becomes threat intelligence. The data needs context around to become intel.

Threat intelligence is also broken up into three different types
- Strategic: Assist senior management make informed decisions specifically about the security budget and strategies
- Tactical: Interacts with TTPs and attack models to identify adversary attack patterns.
- Operational: Interact with IOCs and how the adversaries operationalize.

### Task 4 What are ISACs
According to the National Council of ISACs, "Information Sharing and Analysis Centers (ISACs) are member-driven organizations, delivering all-hazards threat and mitigation information to asset owners and operators". ISACs can be community-centered or vendor-specific. ISACs include CTI from threat actors as well as mitigation information in the form of IOCs, YARA rules, etc. ISACs maintain situational awareness by sharing and collaborating to maintain CTI, through a [National Council of ISACs](https://www.nationalisacs.org/).

List of ISACs that can help a blue team:
- US-CERT
- AlienVault OTX
- ThreatConnect
- MISP

### Task 5 Using ThreatConnect to create a Threat Intel Dashboard

Threat Connect focuses more on the info and new developments within cybersecurity and the threat landscape and connecting the landscape with indicators.

### Task 6 INtro to AlienVault OTX
AlienVault OTX from AT&T Cybersecurity is one of the main ISACs that is used as an exchange for cumminity maintained threat intelligence.

AlienVault uses 'Pulses' to create trackers for various categories. Pulses can be categorized by Malware type, APT or group, and targeted industry. All pulses are community-created excluding official pulses from AlienVault.

Pulses can include a wide variety of IOCs such as File Hashes (MD5, SHA1), IPv4, IPv6, Domain, URL, YARA, CVE and more.

There are also six different tabs that you can navigate to on the navigation bar, they are outlined below.

-   Dashboard - This is shown above in the screenshot above. It's the main page of OTX and will provide a brief overview of important intel.
-   Browse - This will allow you to see all new pulses and sort by various categories to find the newest intel.
-   Scan Endpoints - This is an automated service called OTX Endpoint Security that will scan endpoints for indicators.
-   Create Pulse - This will allow you to create your own pulses and contribute to the threat exchange.
-   Submit Sample - This allows you to submit a malware sample or URL sample which OTX will analyze and generate a report based on the provided sample.
-   API Integration - Allows synchronization of the threat exchange with other tools for monitoring your environment.

### Task 7 Using OTX to gather Threat Intelligence
###### Pulse Overview
Can consist of a description, tags, indicator types (file hash, Yara, IP, domain, etc.), and threat infrastructure (country of origin). OTX uses pulses as their indicators. A majority of pulses are community-created and maintained. Always analyze the indicators used before using them for CTI.

##### Breaking Down a Pulse
Pulses are made up of three main categories: Pulse decription, indicator overview, indicators.

###### Pulse Description
Consists of the description itself, references, tags, malware families, and ATT&CK IDs.

###### Indicator Overview
Gives you a brief statistical representation of the indicators within the pulse as well as threat infrastucture. Can be useful if looking for a very specific IOC like a file hash or YARA rule, etc.

###### Indicators
The most important section the entire pulse. It contains all of the indicators and information about them.

- Type - the type of indicator (File hash, URL, IP, Domain, etc.)
- Indicator - the indicator itself
- Added - Date added
- Active - shows whether the indicator is still seen in the wild and active
- Related Pulses - shows other pulses that share the same indicator
- Extra info (advanced) - these are the advanced options including dynanic analysis, network activity, and YARA rules

##### Finding Pulses based on Malware
If you want to find specific pulses only for a specific malware strain you can look for pulses based on malware. This allows you to very quickly find IOCs and rules for a specific strain of malware.

##### Finding Pulses based on Adversaries
You can identify pulses based on the adversary group. The adversary menu will give you a short description of the group as well as pulses related to that group.

##### Finding pulses based on Industry
A common vector for adversaries to focus on is the industry of their target, for example finance, education, aerospace, etc. They will also sometimes target corporations in one specific industry. An example of this is the Ryuk malware being distributed by "one" group targeting financial corporations.

### Task 8 Creating IOCs
##### Creating IOCs Manually
A large portion of ISACs are community contributions where contributors take the latest malware samples and create IOCs for them. There are a few tools that can help with the creation of IOCs including strings, winmd5free, and Mandiant IOC editor.

##### Creating Pulses from IOCs
Navigate to the create pulse tab of AlienVault OTX to begin creating a pulse.

### Task 9 Investigation Scenarios
##### Scenario 1
Your incident response team has quarantined a suspicious bin file. The team thinks it is a ransomware variation. Investigate and create indicators for the file.

You can find the shellcode under `C:\Users\Jon\Documents\Scenarios\Scenario 1`

##### Scenario 2

You have been assigned to analyze this week's quarantined files. The file is thought to be an unknown trojan or a new strain of the emotet malware. Investigate and create indicators for the file.

You can find the shellcode under `C:\Users\Jon\Documents\Scenarios\Scenario 2`



