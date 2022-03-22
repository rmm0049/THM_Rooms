# Extender
Allows you to add extension into Burp Suite

### Task 2 Interface
The default view in the Extender interface gives us an overview of the extensions that we have loaded into Burp Suite. The first box provides you with a list of extensions that we have installed and allows us to activate or deactivate them for this project

The options to the left of this box allow you to uninstall extensions, with the Remove button or install new ones from files on the disk with the Add button. These could be either modules that you've coded or modules that have been made available on the Internet but are not in the BApp store.

### Task 3 The BApp Store
The Burp App store Store (BApp) gives you a way to easily list official extensions and integrate them seamlessly with Burp Suite. Extensions can be written in a variety of languages -- most commonly Java or Python (which requires the Jython interpreter)

### Task 4 Jython
First, need to download an up-to-date copy of the Jython JAR archive. Looking for the **Jython Standalone** option

Save the JAR file, then switch to "Options" tab in Extender.

Go to "Python Environment" section, and set the "Location of Jython standalone JAR file" to the path of th archive:

Same thing to install -> Jruby

### Task 5 The Burp Suite API
Whilst coding our own modules is far outwith the scope of this, it is worth looking at how such a task might be approached.

Extender exposes a large number of API Endpoints that new modules can hook into when integrating with Burp Suite.

