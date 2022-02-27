# The Basics
### Task 1 Intro

A look at the Burp Suite framework.

### Task 2 What is Burp Suite?
A framework written in Java that aims to provide a one-stop shop for web application penetration testing. In many ways, the goal is achieved as Burp is very much the industry standard tool for hands on web app security assessments. Commonly used when assessing mobile applications, as the same features which make it so attractive for web app testing translate almost perfectly into testing APIs powering most mobile apps.

Burp can capture and manipulate all of the traffic between an attacker and a webserver: this is the core of the framework. After capturing requests, we can choose what to do with them.

Free version => **Burp Suite Community Edition**
Paid => **Burp Suite Professional & Burp Suite Enterprise**

Burp suite provides constant scanning for target web apps

### Task 3 Features of Burp Community
Community version has a relatively limited feature-set compared to the professional version, but still has a lot of useful tools

- **Proxy**: Most well-known feature of Burp Suite, the proxy allows you to intercept and modify requests/responses when interacting with web applications.
- **Repeater**: Allows you to capture, modify and then resend the same request numerous times. This feature can be absolutely invaluable, especially when we need to craft a payload through trial and error.
- **Intruder**: Although harshly rate-limited in Burp Community, allows you to spray an endpoint with requests, often to fuzz endpoints.
- **Decoder**: Either decodes captured information, or encoding a payload prior to sending it to the target.
- **Comparer**: Allows you to compare two pieces of data at either word or byte level.
- **Sequencer**: Used to assess the randomness of tokens such as session cookie values or other supposedly random generated data. If the algorithm is not generating secure random values, then this could open up some devastating avenues for attack.

In addition to the myriad of in-built features, the Java codebase also makes it very easy to write extensions to add to the functionality of the framework.

### Task 4 Installation
PortSwigger has default installations for Linux, MacOS and Windows on their Burp website.

**Note:** It is not default added to your PATH in Linux when executing, so add it if you want to run it from anywhere.

### Task 5 The Dashboard
Contains:
- Tasks
	- define background tasks that Burp Suite will run whilst we use the application.
- Event Log
	- tells us what Burp Suite is doing
- Issue activity (pro version only)
	- Exclusive to pro version, it would list all of the vulnerabilities that it found by the automated scanner.
- Advisory
	- Gives more info about the vulnerabilities found, as well as references and suggested remedies.

### Task 6 Navigation

**Shortcuts**
- `Ctrl + Shift + D`: Switch to the dashboard
- `Ctrl + Shift + T`: Target Tab
- `Ctrl + Shift + P`: Proxy Tab
- `Ctrl + Shift + I`: Intruder tab
- `Ctrl + Shift + R`: Repeater tab

### Task 7 Options
- Global settings can be found in the *User options* tab along the top menu bar.
- Project-specific settings can be found in the *Project options* tab.

### Task 9 Connecting through Proxy (FoxyProxy)
1. We could use the embedded browser
2. We can configure our local web browser to proxy our traffic through Burp; this is more common and so will be the focus of this task.

Burp Proxy runs by default locally on `127.0.0.1:8080`.

### Task 10 Proxying HTTPS
You can run in to trouble once we start using HTTPS. Specifically, Firefox telling us that the Portswigger Certificate Authority (CA) isn't authorized to secure the connection.

We need to add Portswigger cert to the list of trusted certificate authorities.

1. Head to http://burp/cert; download the file `cacert.der`
2. Go to `about:preferences` in the Firefox and select `View Certificates`
3. Import the certificate and check `Trust this CA to identify websites`

### Task 11 The Burp Suite Browser
Burp Suite has a built in browser to use instead of using your local browser to view traffic. It is located in the Intercept tap within the Proxy.

Solutions to errors:
- The smart option: We could create a new user and run Burp Suite under a low privilege account.
- The easy option: Go to `Project options -> Misc -> Embedded Browser` and check the `Allow the embedded browser to run without a sandbox` option.

### Task 12 Scoping and Targeting
It can get extremely tedious having Burp capturing all of our traffic. When it logs everything (including traffic to sites we aren't targeting), it muddies up logs we may later wish to send to clients. In short, allowing Burp to capture everything can quickly become a massive pain.

Setting a scope for the project allows us to define what gets proxied and logged. We can restrict Burp Suite to *only* target the web application(s) that we want to test. Go over to the "Target" tab, right-clicking our target from our list on the left, then choosing "Add to scope". Burp will then ask whether to stop logging anything which isn't our scope - most of the time we want this yes.

### Task 13 Site Map and Issue Definitions

Three sub tabs under Target:
- **Site Map**: Allows us to map out the apps we are targeting in a tree structure. Every page that we visit will show up here, allowing us to automatically generate a site map for the target simply by browsing around the web app. Burp Pro would also allow us to spider the targets automatically.
- **Scope**: we have already seen the Scope sub-tab -> allows you to control the target scope
- **Issue Definitions**: Whilst we don't have access to Burp Suite vulnerability scanner, we do still have access to a list of all vulnerabilities it looks for.

 ### Task 14 Example Attack

 We see that on the site map that there is the form page `http://server_ip/ticket/`



