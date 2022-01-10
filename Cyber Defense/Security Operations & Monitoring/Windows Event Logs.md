# Windows Event Logs
### Task 1 What are event logs?
Provide an audit trail that can be used to understand the activity of the system and to diagnose problems. They are essential to understand the activities of complex systems, particularly in applications with little user interaction (such as server applications)

This is where SIEMs (**Security information and event management**) such as Splunk and Elastic come into play.

SIEMs will allow you to query the logs from multiple devices instead of manually connecting to a single device to view its logs. On Linux systems the logging system is known as **Syslog**. Within this room, though, we're only focusing on the Windows logging system called Windows Event Logs.

### Task 2 Event Viewer
The Windows Event Logs are not text files that can be viewed using a text editor. However, the raw data can be translated into XML using the Windows API. The events stored in these log files are stored in a proprietary binary format with a .evt or .evtx extension. The log files with the .evtx file extension typically reside in `C:\Windows\System32\winevt\Logs`

There are 3 main ways of accessing these event logs within a Windows system:
1. **Event Viewer** (GUI-based application)
2. **wevtutil.exe** (command-line tool)
3. **Get-WinEvent** (PowerShell cmdlet)

Each method of accessing event logs has its pros and cons. In this section, we'll look at the **Event Viewer** first.

Event Viewer can be launched by CLI as well by typing `eventvwr.msc`

Different types of logs:
- Error: Significant problem such as loss of data or loss of functionality.
- Warning: Not necessarily significant, but may indicate a possible future problem.
- Information: Successful operation of an application, driver, or service
- Success Audit: An audited security access attempt that was successful
- Failure Audit: Security access attempt that failed.

On the left pane, the standard logs are visible under **Windows Logs**.

The next section is the **Applications and Services Logs**. Expand this section and drill down on `Microsoft > Windows > PowerShell > Operational`

By clicking on **Properties**, you can see the log location, size and when it was created, modified, and last accessed.

A brief explanation for each column:
- The first column is **Level**, which is the event type.
- **Data and Time**, which is when the event was logged.
- **Source** is the name of the software that logs the event. 
- **Event ID** are IDs that say what event occured. Note that Event IDs are not unique. Meaning that Event ID 4103 is related to Executing Pipeline but will will have an entirely different meaning in another log.
- **Task Category**, is the event category, and will help you organize events so Event Viewer can filter them.

There's an **Actions** pane. You can open a saved log here, which is useful if the remote machine can't be accessed. The logs can be provided to the analyst.

The **Create Custom View** and **Filter Current Log** are nearly identifical. The only difference is that the **By log** and **By source** radio buttons are grayed out in **Filter Current Log**.

### Task 3 wevtutil.exe
Enables you to retrieve information about event logs and publishers. You can also use this command to install and uninstall event manifests, to run queries, and to export, archive, and clear logs.

As with any tool to get the help:
`wevtutil.exe /?`
or
`wevtutil.exe COMMAND /?` for specific command help

### Task 4 Get-WinEvent
PowerShell cmdlet to get events from event logs and event tracing log files on local and remote computers.

A little bit about filtering:
`Get-WinEvent -LogName Application | Where-Object { $_.ProviderName -Match 'WLMS' }`

When working with large events logs, it's inefficient to send objects down the pipeline to `Where-Object` command. Instead use the Get-WinEvent cmdlet's **FilterHashtable** parameter is recommended to filter event logs.

ex)
```scheme
	Get-WinEvent -FilterHashtable @{
		LogName='Application'
		ProviderName='WLMS'
	}
```

Syntax for the hash table is: `@{ <name> = <value>; [<name> = <value>] ... }`

### Task 5 XPath Queries
Filtering events with **XPath**

The W3C created XPath (XML Path Language)

Example
```scheme
	// The following selects all events from the channel or log file where the
	// severity level is less than or equal to 3 and event occured last 24 hours
	XPath Query: *[System[(Level <= 3) and TimeCreated[timediff(@SystemTime) <= 86400000]]]
```

An XPath event query starts with **''\*''** or '**Event**'.

The command so far looks like: `Get-WinEvent -LogName Application -FilterXPath '*'`

Next working down the XML tree, the next tag is `System`

Now the command is: `Get-WinEvent -LogName Application -FilterXPath '*/System/'`

**Note**: It's best practice to explicity use the keyword `System` but you can use an `*` instead as with the `Event` keyword, `-FilterXPath '*/*'` is still valid.

The Event ID is 100, so plug that in:

`Get-WinEvent -LogName Application -FilterXPath '*/System/EventID=100'`

Same thing but with wevtutil.exe:
`wevtutil.exe qe Application /q:*/System[EventID=100] /f:text /c:1`

If you want to query on a different element, such as `Provider Name`, the syntax will be different. To filter on the provider, we need to use the `Name` attribute of `Provider`.

The XPath query is:
`Get-WinEvent -LogName Application -FilterXPath '*/System/Provider[@Name="WLMS"]'`

You can also chain multiple queries by using `and` in between.

Do to XPath queries for elements within `EventData`. The query will be sightly different.

**Note**: The EventData element doesn't always contain information.

We will build the query for `TargetUserName`. In this case, that will be System.

The XPath query would be
`Get-WinEvent -LogName Security -FilterXPath '*/EventData/Data[@Name="TargetUserName"]="System"'`

**Note**: If the `-MaxEvents` is set to 1, it will return just 1 event.

### Task 6 Event IDs
![[Pasted image 20220109172227.png]]

MITRE and/or MITRE ATT&CK is a good resource to find event IDs to monitor for. For example ATT&Ck ID T1098 (Account Manipulation) has a section sharing tips to mitigate the technique, along with detection tips.


### Task 7 Putting theory into practice
