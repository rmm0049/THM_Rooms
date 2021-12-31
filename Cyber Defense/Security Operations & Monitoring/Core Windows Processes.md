# Core Windows Processes
### Task 1 Intro
Exploring the core processes on a Windows System. This foundational knowledge will help identify malicious processes running on an endpoint.

### Task 2 Task Manager
TM is the built-in GUI-based Windows uitility that allows users to see what is running on the Windows system. It also providse info on resource usage, such as how much CPU and memory are utilized by each process. When a program is not responding, TM is used to kill the proces.

Can be opened by right-clicked on the Taskbar -> Task Manger, or by pressing `Ctrl + Shift + Esc`

By default the current tab is `Proccesses`. The processes are categorized: `Apps` and `Background Processes`. Another category is `Windows Processes` The columns are very minimal, **Name, Status, CPU, and Memor**. To view more, right-click on any column and select the more options.

All the columns:
- Type - Each process falls into 1 of 3 categories (Apps, Background, or Windows)
- Publisher - name of the author of the program/file
- PID - process identifier number, unique
- Process name - the file name of the process (ex. Task Manger -> Taskmgr.exe)
- Command line - the full command used to launch the process
- CPU - amount of CPU used by process
- Memory - amount of physical working memory utilized by the process

Process Hacker and Process Explorer are some more in-depth tools...

Command-line equivalent of obtaining info about running processes on Windows -> `tasklist`, `Get-Process`, or `ps` (powershell), and `wmic`

### Task 3 System
The first Windows process on the list is **System**. The PID for System is alwasy **4**. The definition for what it does from Microsoft:

"_The System process (process ID 4) is the home for a special kind of thread that runs only in kernel mode a kernel-mode system thread. System threads have all the attributes and contexts of regular user-mode threads (such as a hardware context, priority, and so on) but are different in that they run only in kernel-mode executing code loaded in system space, whether that is in Ntoskrnl.exe or in any other loaded device driver. In addition, system threads don't have a user process address space and hence must allocate any dynamic storage from operating system memory heaps, such as a paged or nonpaged pool._"

What is normal behavior for this process?

**Image Path**: N/A
**Parent Process**: None
**Number of Instances**: One
**User account**: Local System
**Start time**: At boot time

In process Hacker
**Image Path**: `C:\Windows\System32\ntoskrnl.exe (NT OS kernel)`
**Parent Process**: System Idle Process (0)

What is unusual behavior for this process?
- A parent process (aside from System Idle Process (0) )
- Multiple instances of System (should only be 1)
- A different PID (Should only ever be 4)
- Not running in Session 0

### Task 4 System > smss.exe
The next process is **smss.exe (Session Manager Subsystem)**. This process, also known as the **Windows Session Manager** is responsible for creating new sessions. The first user-mode processes started by the kernel.

This process starts the kernel mode and user mode of the Windows Subsystem. This includes win32k.sys (kernel mode), winsrv.dll (user mode) and crss.exe (user mode)

smss.exe starts csrss.exe (Windows subsystem) and wininit.exe in Session 0, an isolated session for the OS, and csrss.exe and winlogon.exe for Session 1, the user session.  The first child instances in new sessions. This is done smss.exe copying itself into the new session and self-terminating. 

Session 0 (csrss.exe & wininit.exe)

Session 1 (crss.exe & winlogon.exe)

Any other subsystem listed in the `Required` value of
`HKLM\System\CurrentControlSet\Control\Session Manager\Subsystems` is also launched

SMSS is also responsible for creating environment variables, virtual memory paging files and starts winlogon.exe (the Windows Logon Manager).

What is Normal?

**Image Path**: `%SystemRoot%\System32\smss.exe`
**Parent Process**: System
**Number of Instances**: One master instance and child instance per session. The child instance exits after creating the session.
**User Account**: Local System
**Start Time**: Within seconds of boot time for the master instance

What is unusual?
- A different parent process than System(4)
- Image path different than C:\Windows\System32
- More than 1 running process
- User is not SYSTEM
- Unexpected registry entries for Subsystem

### Task 5 csrss.exe
The **csrss.exe (Client Server Runtime Process)** is user-mode side of the Windows subsystem. This is always running and is critical to system operation. If by chance this process is terminated it will result in system failure. It is responsible for the Win32 console window and process thread creation and deletion. For each instance csrsrv.dll, baserv.dll and winsrv.dll are loaded (among others)

It also responsible for making the Windows API available to other processes, mapping drive letters and handling the Windows shutdown process.

What is normal?

**Image Path**: `%SystemRoot%\System32\csrss.exe`
**Parent Process**: Created by an instance of smss.exe that self-terminates
**Number of Instances**: Two or more
**User Account**: Local System
**Start Time**: Within seconds of boot time for the first 2 instances (for session 0 and 1). Start times for additional instances occur as new sessions are created, although often only Session 0 and 1 are created.

What is unusual?
- An actual parent process (smss.exe calls this process and self-terminates)
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue process masquerading as csrss.exe in plain sight
- User is not SYSTEM

### Task 6 wininit.exe
The **Windows Initialization Process, wininit.exe**, is responsible for launching services.exe (Service Control Manager), lsass.exe (Local Security Authority), and lsaiso.exe within Session 0. This is another critical Windows process that runs in the background, along with its child processes.

Note: lsaiso.exe is a process associated with **Credential Guard and Key Guard**. You will only see this process if Credential Guard is enabled.

What is Normal?

**Image Path**: `%SystemRoot%\System32\wininit.exe`
**Parent Process**: Created by one instance of smss.exe
**Number of Instances**: One
**User Account**: Local System
**Start Time**: Within seconds of boot time

What is unusual?
- An actual parent process (smss.exe calls this and self-terminates)
- Image path other than C:\Windows\System32
- Subtle misspellings to hide rogue process in plain sight
- Multiple running instances
- Not running as SYSTEM

### Task 7 wininit.exe -> services.exe
The next process is the **Service Control Manager (SCM)**, which is **services.exe**. Its primary responsibility is to handle system services: loading services, interacting with services, starting/ending services, etc. It maintains a database that can be queried using a Windows built-in utilit, 'sc.exe'

Information regarding services is stored in the registry, `HKLM\System\CurrentControlSet\Services`

This process also loads device drivers marked as auto-start into memory

When a user logs into a machine successfully, this process is responsible for setting the value of the Last Known Good control set (Last Known Good Configuration), `HKLM\System\Select\LastKnownGood`, to that of the CurrentControlSet.

This process is parent to several other key processes: svchost.exe, spoolsv.exe, msmpeng.exe, dllhost.exe, to name a few.

What is Normal?

**Image Path**: `%SystemRoot$\System32\services.exe`
**Parent Process**: wininit.exe
**Number of instances**: One
**User account**: Local System
**Start Time**: Within seconds of boot time

What is Unusual?
- A parent process other than wininit.exe
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue process in plain sight
- Multiple running instances
- Not running as SYSTEM

### Task 8 wininit.exe -> services.exe -> svchost.exe
The **Service Host** (Host process for Windows Services), or **svchost.exe**, is responsible for hosting and managing Windows services

The services running in this process are implemented as DLLs. The DLL to implement is stored in the registry for the service under the `Parameters` subkey in `ServiceDLL`. The full path is `HKLM\SYSTEM\CurrentControlSet\Services\SERVICE NAME\Parameters`

The key identifier in the binary path is `-k`. This is how a legitimate svchost.exe process is called.

The -k parameter is for grouping similar services to share the same process. This concept was based on the OS design and implemented to reduce resource consumption. Starting from **Windows 10 Version 1703** services grouped into host processes changed. On machines running more than 3.5GB of memory, each service will run its own process.

Since svchost.exe will always have multiple running processes on any Windows system, this process has been a target for malicious use. They can name the malware svchost.exe or slightly misspell it such as *scvhost.exe*. Another tactic is to install/call a malicious service (DLL)

What is Normal?

**Image Path**: `%SystemRoot%\System32\svchost.exe`
**Parent Process**: services.exe
**Number of instances**: Many
**User Account**: Varies (SYSTEM, Network Service, Local Service) depending on the svchost.exe instance. In Windows 10 some instances can run as the logged-in user.
**Start time**: Typically within seconds of boot time. Other instances can be started after boot

What is unusual?
- A parent process other than services.exe
- Image file path other than C:\Windows\System32
- Subtle misspellings
- The absence of the -k parameter

### Task 9 lsass.exe
The **Local Security Authority Subsystem Service** (LSASS) is a process that is responsible for enforcing the security policy on the system. It verifies users logging on to a Windows computer or server, handles password changes, and creates access tokens. It also writes to the Security Log

It creates security tokens for SAM (Security Account Manager, AD (Active Directory), and NETLOGON. It uses authentication packages specified in `HKLM\System\CurrentControlSet\Control\Lsa`

This is another process adversaries target. Common tools such as **mimikatz** is used to dump credentials or they mimic this process to hide in plain sight. Again, they do this by either naming their malware by this process name or simply misspelling the malware slightly.

What is normal?

**Image Path**: `%SystemRoot%\System32\lsass.exe`
**Parent Process**: wininit.exe
**Number of Instance**: One
**User account**: Local System
**Start Time**: Within seconds of boot time

What is unusual?
- A parent process other than wininit.exe
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue process in plain sight
- Multiple running instances
- Not running as SYSTEM

### Task 10 winlogon.exe
The **Windows Logon, winlogon.exe**, is responsible for handling the **Secure Attention Sequence** (SAS). This is the ALT+CTRL+DELETE key combination users press to enter their username & password.

This process is also responsible for loading the user profile. This is done by loading the user's NTUSER.DAT into HKCU and via userinit.exe loads the user's shell.

It's also responsible for locking the screen and running the user's screensaver, among other functions. 

What is normal?

**Image Path**: `%SystemRoot%\System32\winlogon.exe`
**Parent Process**: Created by an instance of smss.exe that exits, so analysis tools usually do not provide the parent process name.
**Number of Instance**: One or more
**User Account**: Local System
**Start time**: Within seconds of boot time for the first instance (for Session 1), additional instances occur as new sessions are created, typically through Remote Desktop or Fast User Switching logons.

What is unusual?
- An actual parent process
- Image File path other than C:\Windows\System32
- Subtle misspellings
- Not running as SYSTEM
- Shell value in registry other than explorer.exe

### Task 11 explorer.exe
The **Windows Explorer, explorer.exe** gives the user access to their folders and files. It also provides functionality to other features such as the Start Menu, Taskbar, etc.

The winlogon process runs userinit.exe, which launches the value in `HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell`
Userinit.exe exits after spawning explorer.exe, because of this, the parent process is non-existent.

There will be many child processes for explorer.exe

What is normal?

**Image path**: `%SystemRoot%\explorer.exe`
**Parent Process**: Created by userinit.exe and exits
**Number of instances**: one or more interactively logged-in user
**User Account**: logged-in user(s)
**Start time**: First instance when the first interactive user logon session begins

What is unusual?
- An actual parent process (userinit.exe calls this and exits)
- Image file path other than C:\WIndows
- Running as an unknown user
- subtle misspellings
- Outbound TCP/IP connections







