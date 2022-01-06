# Sysinternals
The Sysinternals tool is compilation of over 70+ Windows-based tools. Each of the tools falls into one of the following categories:
- File and Disk Utilities
- Networking Utilities
- Process Utilities
- Security Utilities
- System Information
- Miscellaneous

The Sysinternals tools are extremely popular among IT professionals who manage Windows systems. These tools are so popular that even red teamers and adversaries alike use them

Microsoft acquired the Sysinternals tools -> 2006

### Task 2 Install Sysinternals suite
Can be downloaded to run on your local system or run from the web. You can also just get specific categories within the suite as a download.

https://live.sysinternals.com/ Is the link to run these from the web.

You can add an entry to your Path ENV variable so that these can be run from anywhere.

### Task 3 Using Sysinternals Live
Allos you to execute Sysinternals tools directly from the Web without hunting for and manually downloading them. Simply enter a tool's Sysinternals Live path into Windows Explorer or a command promprt as **live.sysinternals.com/[toolname]** or **\\live.sysinternals,com\tools\toolname**

Based on the instructions to launch Process Monitor,
`\\live.sysinternals.com\tools\procmon.exe`

and it fails...

Need to install the and run the WebDAV client. Go to powershell and get the status with `get-service webclient`. If it's stopped then do `start-service webclient` to start it.

Also, Network Discovery needs to be enabled as well, which can be done through the Network and Sharing Center.

### Task 4 File and Disk Utilities
##### Sigcheck
Command-line utility that shows file version number, timestamp information,  and digital signature details, including certificate chains. It also includes an option to check a file's status on VirusTotal.

ex) `sigcheck -u -e C:\Windows\System32 -accepteula`

parameter usage:
- `-u` If VirusTotal is enabled, show files that are unknown by VirusTotal or have non-zero detection, otherwise show only unsigned files
- `-e` scan executable images only (regardless of their extension)

##### Streams
**Alternate Data Streams** (ADS) is a file attribute specific to Windows **NTFS** (New Technology File System). Every file has at least one data stream ($DATA) and ADS allows files to contain more than one stream of data. Natively Window Explorer doesn't display ADS to the user. There are 3rd party executables that can be used to view this data, but Powershell gives you the ability to view ADS for files.

`streams <file/directory> -accepteula`

##### SDelete
Command-line utility that takes a number of options, in any given use, allows you to delete one or more files and/or directories, or to cleanse the free space on a logical disk.

SDelete (Secure Delete) implemented the **DOD 5220.22-M** (Department of Defense Clearing and sanitizing protocol).

DoD 5220.22-M Wipe Method
- Pass 1: Writes a zero and verifies the write.
- Pass 2: Writes a one and verifies the write.
- Pass 3: Writes a random character an verifies the write.

SDelete has been used by adversaries and is associated with MITRE techniques T1485 (Data Destruction) and T1070.004 (indicator Removal on Host: File Deletion). Its MITRE ID S0195

### Task 5 Networking Utilities
##### TCPView
A Windows program that will show you detailed listings of all TCP and UDP endpoints on your system, including the local and remote addresses and state of TCP connections. Also includes Tcpvcon, a command-line function that has the same functionality

There's a built-in Windows tool called **Resource Monitor**, `resmon`.

Expand **TCP Connections** to view the **Remote Address** for each **Process** with an outbound connection.

### Task 6 Process Utilities
##### Autoruns
Takes the knowledge of auto-starting locations of any startup monitor, shows you what programs are configured to run during system bootup or login. It reports Explorer shell extensions, toolbars, browser helper objects, Winlogon notifications, auto-start services, and much more.

**Note**: This is a good tool to search for any malicious entries created in the local machine to establish **Persistence**.

##### ProcDump
Command-line utility whose primary purpose is monitoring an application for CPU spikes and generating crash dumps during a spike that an administrator or developer can use to determine the cause of the spike.

Alternatively, you can use **Process Explorer** to do the same. Right-click on the process to create a **Minidump** or **Full Dump** of the Process.

##### Process Explorer
Consists of two sub-windows. The top window always shows a list of the currently active processes, including the names of their owning accounts, whereas the information displayed in the bottom window depends on the mode that Process Explorer is in: if it is in handle mode you'll see the handles that the process selected in the top window has opened; if the Process Explorer is in DLL mode you'll see the DLLs and memory-mapped files that the process has loaded.

There is also an option to **Verify Signatures**, Once enabled it shows up as a column within the Process view.

Other options to note include **Run at Logon** and **Replace Task Manager**.

Colors of the processes and meaning:
- Purple -> files may be packed
- Red -> processes is exiting (being stopped)
- green -> Freshly spawned (just loaded)
- light blue -> run by same account that started Process Explorer
- dark blue -> process is selected (by clicking or otherwise)
- pink -> indicates the process is a service (like svchost.exe)
- dark grey -> process is suspended

##### Process Monitor
Shows real-time file system, Registry and process/thread activity. It is a combination of two legacy Sysinternals features, Filemon and Regmon and adds an extensive list of enhancements including rich and non-destructive filtering.

Capturing events can be toggled on or off

To use ProcMon effectively you **must** use the filter and **must** configure it properly as it captures thousands of events.

##### PsExec
Is a lightweight telnet-replacement that lets you execute processes on other systems, complete with full interactivity for console applications, without having to manually install client software. PsExec's most powerful uses include launching interactive command-prompts on remote systems and remote-enabling tools like IpConfig that otherwise do not have the ability to show information about remote systems.

### Task 7 Security Utilities
##### Sysmon
System Monitor is a Windows system service and device driver that, once installed on a system, remains resident across system reboot to monitor and log system activity to the Windows event log. It provides detailed information about process creations, network connections and changes to file creation time.

### Task 8 System Information
##### WinObj
A 32-bit Windows NT program that uses the native Windows NT API to access and display information on the NT object manager's name space

Looking into the concept of **Session 0** and **Session 1**.

Session 0 is the OS session and Session 1 is the User session. Also there will be at least 2 csrss.exe processes running, one for each session.

### Task 9 Miscellaneous
##### BgInfo
Automatically displays relevant information about a Windows computer on the desktop's background, such as the computer name, IP address, service pack version, and more.

Handle utility if you handle multiple machines Typically utilized on servers. When a user RDPs into a server, the system informatio is displayed on the wallpaper to provide quick information about the server, such as the server's name.

##### RegJump
Command-line applet that takes a registry path and makes Regedit open to that path. It accepts root keys in standard (e.g. HKEY_LOCAL_MACHINE) and abbreviated form (HKLM).

There are multiple ways to query the Windows Registry without using the registry editor, such as the command line (`req query`) and PowerShell (`Get-Item`/`Get-ItemProperty`)

##### Strings
Strings just scans the file you pass it for UNICODE (or ASCII) strings of a default length of 3 or more UNICODE (or ASCII) characters. Note that it works under Windows 95 as well.

