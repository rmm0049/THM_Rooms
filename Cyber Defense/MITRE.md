# MITRE Room
### Task 1 Introduction to MITRE
Most might associate MITRE with CVEs (Common Vulnerabilities and Exposures) list, but MITRE researches in many areas.

###### Room focuses
- ATT&CK (Adversarial Tactics, Techniques, and Common Knowledge) Framework
- CAR (Cyber Analytics Repository) Knowledge Base
- SHIELD Active Defense
- AEP (ATT&CK Emulation Plans)

### Task 2 Basic Terminology
**APT** stands for *Advanced Persistant Threat*. This can be considered a team/group or even country (nation-state group) that engage in long-term attacks against organizations and/or countries. *FireEye's* contains a list of APT groups.

**TTP** stands for Tactice, Techniques, and Procedures
- Tactic is the adversary's goal or objective
- Technique is how the adversary achieves the goal or objective
- Procedure is how the technique is executed.

### Task 3 ATT&CK Framework
From the website, "MITRE ATT&CK is globally-accessible knowledge base of adversary tactics and techniques based on real-world observations". Tool is useful for both red team or blue team. Thhe ATT&CK Matrix for Entreprise has 14 categories. Each category contains the techniques an adversary could use to perform the tactic.

**MITRE ATT&CK® Navigator**: "_The ATT&CK® Navigator is designed to provide basic navigation and annotation of ATT&CK® matrices, something that people are already doing today in tools like Excel. We've designed it to be simple and generic - you can use the Navigator to visualize your defensive coverage, your red/blue team planning, the frequency of detected techniques, or anything else you want to do_."

### Task 4 CAR Knowledge Base
**CAR** Cyber Analytis Repository is a knowledge base of analytics developed by MITRE based on the MITRE ATT&CK adversary model.

Reviewing **[CAR-2020-09-001: Scheduled Task - File Access](https://car.mitre.org/analytics/CAR-2020-09-001/)**.

Another analytic to, [**CAR-2014-11-004: Remote PowerShell Sessions**](https://car.mitre.org/analytics/CAR-2014-11-004/)

Under Implementations, pseudocode is provided and an EQL version of the pseudocode. EQL (pronounced as 'equal'), and it's an acronym for Event Query Language. EQL can be utilized to query, parse, and organize Sysmon event data. You can read more about this [**here**](https://eql.readthedocs.io/en/latest/).

### Task 5 Shield Active Defense
MITRE ENGAGE has replaced MITRE SHIELD
SHIELD is still on GitHub

Navigate to this directory on machine
`~/Desktop/mitrecnd.github.io-master`

Run the command:
`bundle exec jekyll serve`

Then navigate to browser at:
`http://127.0.0.1:4000`

This allows us to browse a local copy of **MITRE SHIELD**

Per the website, "_Shield is an active defense knowledge base MITRE is developing to capture and organize what we are learning about active defense and adversary engagement. Derived from over 10 years of adversary engagement experience, it spans the range from high level, CISO ready considerations of opportunities and objectives, to practitioner friendly discussions of the TTPs available to defenders._"

Shield Active Defense is similar to the ATT&CK Matrix but the tactics and techniques provied to us enable us to trap and/or engage with an adversary active within the network. Can be used for **threat intelligence**.

### Task 6 ATT&CK Emulation Plans
If these tools provided to us by MITRE are not enough, under [**MITRE ENGENUITY**](https://mitre-engenuity.org/), we have **CTID**, the **Adversary Emulation Library**, and ATT&CK® Emulation Plans.

**CITD**
Center of Threat-Informed Defense. Consists of various companies and vendors from around the globe. Some of the companies and vendors who participate:
- AttackIQ
- Verizon
- Microsoft
- Red Canary
- Splunk

The [**Adversary Emulation Library**](https://medium.com/mitre-engenuity/introducing-the-all-new-adversary-emulation-plan-library-234b1d543f6b) is a public library making adversary emulation plans a free resource for blue/red teamers. The library and the emulations are a contribution from CTID. There are 3 [**ATT&CK® Emulation Plans**](https://mitre-engenuity.org/attackevaluations/) currently available: [**APT3**](https://attack.mitre.org/resources/adversary-emulation-plans/), [**APT29**](https://github.com/center-for-threat-informed-defense/adversary_emulation_library/tree/master/apt29), and **[FIN6](https://github.com/center-for-threat-informed-defense/adversary_emulation_library/tree/master/fin6)**.

### Task 7 ATT&CK and Threat Intelligence
**Threat Intelligence (TI)** or **Cyber Threat Intelligence (CTI)** is the information, or TTPs, attributed to the adversary. By using threat intelligence, as defenders, we can make better decisions regarding the defensive strategy.

**Scenario**: You are a security analyst who works in the aviation sector. Your organization is moving their infrastructure to the cloud. Your goal is to use the ATT&CK® Matrix to gather threat intelligence on APT groups who might target this particular sector and use techniques targeting your areas of concern. You are checking to see if there are any gaps in coverage. After selecting a group, look over the selected group's information and their tactics, techniques, etc.

###### Question 1
APT33
###### Question 2
Use stuxnet? Nay
###### Question 3
cloud accounts
###### Question 4
Ruler tool
###### Question 5
abnormal or malicious behavior
###### Question 6
Affects Azure AD, Google Workspace, IaaS, Office 365, SaaS





