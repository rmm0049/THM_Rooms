# OpenVAS 
#### Task 1 Introduction
OpenVAS (Open Vulnerability Assessment Scanner) is an application used to scan endpoints and web applications to identify and detect vulnerabilities. It is commonly used by corporations as part of their mitigation solutions to quickly identify any gaps in their production or even development servers or applications.

### Task 2 GVM Framework Architecture
THe OpenVAS is built off the GreenBone Vulnerability Management (GVM) solution and is only one of the appliances that is released from GreenBone.

![](https://community.greenbone.net/uploads/default/optimized/1X/901766e0fa6b6cb6aee9f4702f46fb8a703a332b_2_690x323.png)

#### Vulnerability/Information Feed (NVT, SCAP, CERT, User Data, Community Feed)
Contains all information and vulnerability tests that come from the Greenbone Community Feed that will be the main baseline for testing against systems.

#### Back-End (OSP, OpenVAS, Targets)
The back-end infrastructure is waht will be actually conducting all of the vulnerability scanning and processing data and NVTS through OpenVAS and GVM. GVM will be the middle man between the scanners and the front-end user interfaces.

#### Front-End (GSA, Web Interfaces)
This is what you interact with when you navigate to OpenVAS in the browser. The web interfaces are built off of the Greenbone Security Assistant and make life easier for an analyst or operator when working with OpenVAS or other forms of scanners through the GVM.

### Task 3 Installing OpenVAS



