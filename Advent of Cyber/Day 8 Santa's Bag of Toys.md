# Santa's Bag of Toys
### Room Concept
McSkidy was notified of some terrible news! Santa's laptop, which he uses to prepare his bag of toys for Christmas, is missing! We believe a minion at the Grinch Enterprise stole it, but we need to find out for sure. It is up to us to determine what actor compromised the laptop and recover Santa's bag of toys!

Unfortunately, The Best Festival Company had minimal monitoring tools on Santa's laptop (he is the boss, after all)! All we have to work with are some PowerShell Transcription Logs we were able to remotely recover just after it went missing. You can find the transcription logs within the `SantasLaptopLogs` folder on the Desktop of the attached Windows virtual machine.

##### The Windows Registry
The Windows Registry is a large database of operating system settings and configurations which are organized into `hives`, each containing `keys` and their corresponding `values`. The PowerShell transcription logging can be enabled in this way "per user" via the `HKEY_CURRENT_USER` registry hive, or across the entire machine through the `HKEY_LOCAL_MACHINE`.

##### Looking at the PowerShell logs
Found that the file `UsrClass.dat` was encrypted using `certutil.exe` into base64 encoding. Using CyberChef, the file was decoded back to is original file. Then the program ShellBags was used to analyze the .dat file. It found an interesting directory `.github` and what appeared to be a repository called `SantaRat`. A search on GitHub resulted in an account called Grinchiest to own these repositories. He installed a RAT on Santa's laptop and was able to modify Santa's bag of toys.

##### Messing with Santa's Bag of Toys
Grinchiest used a tool to collect the contents with a `UHA` archive. UHA archive is similar to ZIP or RAR, but it's faster and has better compression rates. It's a rare thing to see. The UHA file was password protected though!

##### Looking at GitHub Repositories
There's repository called `operation-bag-of-toys` and looking through the commit history, we see that `bag_of_toys.uha` was uploaded and the commit message had the password to the file.
password -> TheGrinchiestGrinchmasOfAll

Opening the .hua file allows us to recovery Santa's bag of toys, all 228 of them.