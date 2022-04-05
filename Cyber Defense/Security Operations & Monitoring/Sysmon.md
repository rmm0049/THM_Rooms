# Sysmon
A tool used to monitor and log events on Windows. It is commonly used in conjunction with a SIEM (Security Information and Event Mangagement) system or other log parsing solutions that aggregate, filter and visualize events.

Events within Sysmon are stored in `Applications and Services Logs/Microsoft/Windows/Sysmon/Operational`

### Task 2 Sysmon Overview

##### Sysmon Config
It requires a config file in order to tell the binary how to analyze the events that it is receiving. You can create your own Sysmon config file or download one. Sysmon contains 24 unique Event IDs, all of which can be used within the config to specify how the events should be handled and analyzed.

##### Event ID 1: Process Creation
This event will look for any processes that hvae been created. You can use this to look for known suspicious processes or processes with typos that would be considered an anomaly.. This event will use the CommandLine and Image XML tags.

ex)
```xml
<RuleGroup name="" groupRelation="or">  
<ProcessCreate onmatch="exclude">  
  <CommandLine condition="is">C:\Windows\system32\svchost.exe -k appmodel -p -s camsvc</CommandLine>  
</ProcessCreate>  
</RuleGroup>
```
This rule excludes the svchost.exe process from the logs

##### Event ID 3: Network Connection
The network connection event will look for events that occur remotely. This will include files and sources of suspicious binaries as well as opened ports. This event will use the Image and DestinationPort XML tags.

ex)
```xml
`<RuleGroup name="" groupRelation="or">  
<NetworkConnect onmatch="include">  
  <Image condition="image">nmap.exe</Image>  
  <DestinationPort name="Alert,Metasploit" condition="is">4444</DestinationPort>  
</NetworkConnect>  
</RuleGroup>`
```
This will identify files transmitted over open ports, in this case it's specifically looking for *nmap.exe* and specifically port 4444 which is commonly used in metasploit framework.

##### Event ID 7: Image Loaded
This event will look for DLLs loaded by processes, which is useful when hunting for DLL injection and DLL Hijacking attacks. It is recommended to exercise caution when using this Event ID as it causes a high system load. It will use the Image, Signed, ImageLoaded, and Signature XML tags.

ex)
```xml
`<RuleGroup name="" groupRelation="or">  
<ImageLoad onmatch="include">  
  <ImageLoaded condition="contains">\Temp\</ImageLoaded>  
</ImageLoad>  
</RuleGroup>`
```
This will look for DLLs that have been loaded within the \Temp\ directory.

##### Event ID 8: CreateRemoteThread
Will monitor for processes injecting code into other processes. The CreateRemoteThread function is used for legimate tasks and applications. However, it could be used by malware to hide malicious activity. This event will use the SourceImage, TargetImage, StartAddress, and StartFuction XML tags.

```xml
`<RuleGroup name="" groupRelation="or">  
<CreateRemoteThread onmatch="include">  
  <StartAddress name="Alert,Cobalt Strike" condition="end with">0B80</StartAddress>  
  <SourceImage condition="contains">\</SourceImage>  
</CreateRemoteThread>  
</RuleGroup>`
```
This will look at the memory address for a specific ending condition "0B80" which could be an indicator of a Cobalt Strike beacon. The second method look sfor injected processes that do not have a parent process.

##### Event ID 11: File Created
Will log when files are created or overwritten the endpoint. This could be used to identify file names and signatures of files that are written to disk. This event uses TargetFilename XML tags.

```xml
`<RuleGroup name="" groupRelation="or">  
<FileCreate onmatch="include">  
  <TargetFilename name="Alert,Ransomware" condition="contains">HELP_TO_SAVE_FILES</TargetFilename>  
</FileCreate>  
</RuleGroup>`
```

The above is an exmple of a ransomware event monitor. This is just one example of a variety of different ways can utilize Event ID 11.

**Event ID 12 / 13/ 14: Registry Event**
This event looks for changes or modifications to the registry. Malicious activity from the registry can include persistence and credential abuse. This event uses the TargetObject XML tags.

```xml
`<RuleGroup name="" groupRelation="or">  
<RegistryEvent onmatch="include">  
  <TargetObject name="T1484" condition="contains">Windows\System\Scripts</TargetObject>  
</RegistryEvent>  
</RuleGroup>`
```
This will look for registry objects that are in "Windows\System\Scripts" directory as this is a common directory for adversaries to place scripts to establish persistence.

##### Event ID 15: FileCreateStreamHash
Will look for any files created in an alternate data stream. This is common technique used by adversaries to hide malware. This event uses TargetFilename XML tags.

```xml
`<RuleGroup name="" groupRelation="or">  
<FileCreateStreamHash onmatch="include">  
  <TargetFilename condition="end with">.hta</TargetFilename  
</FileCreateStreamHash>  
</RuleGroup>`
```
The above code snippet will look for files with the .hta extension that have been placed within an alternate data stream.

##### Event ID 22: DNS Event
WIll log all DNS queries and events for analysis. The most common way to deal with these events is to exclude all trusted domains that you know will be very common "noise" in your environment. This event uses the QueryName XML tags.

```xml
`<RuleGroup name="" groupRelation="or">  
<DnsQuery onmatch="exclude">  
  <QueryName condition="end with">.microsoft.com</QueryName>  
</DnsQuery>  
</RuleGroup>`
```
This will exclude any DNS queries that end with ".microsoft.com"

### Task 3 Installing and Preparing Sysmon
Start sysmon.exe service

### Task 4 Cutting out the Noise
Since most activity is normal it is just "noise" seen on a network that makes it hard to find the actual malicious activity. So we need to exclude or filter out most of this "noise". For this we will look at suspicious logs with both Sysmon configs and how to optimize your hunt only using Sysmon. Look at how to detect ransomware, persistence, Mimikatz, Metasploit, and Command and Control (C2) beacons.

##### Sysmon "Best Practices"
Offers a fairly open and configurable platform for you to use. A few common best practices are outlined and explained below.

- Exclude -> Include
	- When creating rules for the configuration file, it is typically best to prioritize excluding events rather than including events. This prevents you from missing crucial events and only seeing the events that matter the most.
- CLI gives you further control
	- You can use either the `Get-WinEvent` or `wevutil.exe` to access and filter logs.
- Know your environment before implementation


### Task 5 Hunting Metasploit
Metasploit is a commonly used exploit framework for penetration testing and red team operations. Metasploit can be used to easily run exploits on a machine and connect back to a meterpreter shell. To begin hunting we will look for network connections that originate from suspicious ports such as `4444` and `5555`, by default, Metasploit uses port 4444. If there is a connection to any IP known or unkown it should be investigated. 

First looking at a modified Ion-Security configuration to detect the creation of new network connections. The code snippet below will use event ID 3, along with the destination port to identify active connections specifically on port `4444` and `5555`.

```xml
`<RuleGroup name="" groupRelation="or">  
<NetworkConnect onmatch="include">  
<DestinationPort condition="is">4444</DestinationPort>  
<DestinationPort condition="is">5555</DestinationPort>  
</NetworkConnect>  
</RuleGroup>`
```

### Task 6 Detecting Mimikatz
Mimikatz is used to dump credentials from memory along with other Windows post-exploitation activity. Mimikatz is mainly known for dumping LSASS. We can hunt for the file created, execution of the file from an elevated process, creation of a remote thread,  and proceses that Mimikatz creates. Anti-Virus will typically pick up Mimikatz as the signature is very well known but it is still possible for threat actors to obfuscate or use droppers to get the file onto the device.  

##### Detecting File Creation
First method hunting for Mimikatz is just looking for files created with the name Mimikatz. This is easy to get around though...

```xml
`<RuleGroup name="" groupRelation="or">  
<FileCreate onmatch="include">  
<TargetFileName condition="contains">mimikatz</TargetFileName>  
</FileCreate>  
</RuleGroup>`
```

### Task 7 Hunting Malware
Malware has many forms and variations with different end goals. The two types of malware that we will be focusing on are RATs and backdoors. Examples of RATs are `Xeexe` and `Quasar`.

##### Hunting RATs and C2 servers
The first method is similar to hunting Metasploit and that's creating a configuration file to hunt and detect suspicious ports open on the endpoint. By using known suspicious ports to include in our logs we can add to our hunting methodology in which we can use logs to identify adversaries on our network.

### Task 8 Hunting Persistence
Used by attackers to maintain access to a machine once it is compromised. We can hunt persistence by looking for File Creation events as well as Registry Modification events. You can also filter by the Rule Names in order to get past the network noise and focus on anomalies within the event logs.

##### Hunting Startup Persistence
Look for files that are placed in `\Startup\` or `\Start Menu` directories.

##### Hunting Registry Key Persistence
Detecting registry modifiation that adjusts and places a script inside `CurrentVersion\Windows\Run` and other registry locations.

### Task 9 Detecting Evasion Techniques
There are a number of evasion techniques used by malware authors to both evade anti-virus and evade detections. Some of these techniques are Alternate Data Streams, Injections, Masquerading, Packing/Compression, Recompiling, Obfuscation, Anti-Reversing Techniques. Alternate Data Streams are used by malware to hide its files from normal inspection by saving the files in a different stream apart from `$DATA`.

##### Hunting Alternate Data Streams
Sysmon detects this using Event ID 15. It will hash and log any NTFS Streams that are included within the Sysmon configuration file. The below snippet will hunt for files in the `Temp` and `Startup` folder as well as `.hta` and `.bat` extensions.

```xml
`<RuleGroup name="" groupRelation="or">  
<FileCreateStreamHash onmatch="include">  
<TargetFilename condition="contains">Downloads</TargetFilename>  
<TargetFilename condition="contains">Temp\7z</TargetFilename>  
<TargetFilename condition="ends with">.hta</TargetFilename>  
<TargetFilename condition="ends with">.bat</TargetFilename>  
</FileCreateStreamHash>  
</RuleGroup>`
```

