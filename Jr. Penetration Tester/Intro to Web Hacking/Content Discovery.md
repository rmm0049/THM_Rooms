# Content Discovery
### Task 1 What Is Content Discovery?
Content can be many things, a file, video, picture, bakup, a website feature. In this context we're talking about not normal things we see on a website and that weren't always intended for publicly viewing.

Three main ways to do content discovery is **Manually, Automated, and OSINT**.

### Task 2 Robots.txt
This file is a document that tells the search engine which pages they're not allowed to show on their search engine results or ban specific engines from crawling the website altogether. These pages may be areas such as administration portals or files meant for the website's customers. This file gives us a great list of locations on the website that the owners don't want us to discover.

### Task 3 Manually Discovery - Favicon
THe favicon is a small icon displayed in the browser's address bar or tab used for branding a website.

Sometimes when frameworks are used to build a website, a favicon that is part of the installation gets leftover, and if the website developer doesn't replace this with a custom one, this can give us a clue on what framework is in use. OWASP keeps a database of common framework icons that you can use to check against the targets favicon.

### Task 4 Manual Discovery - Sitemap.xml
Unlike robots.txt, sitemap.xml gives a list of every file the website owner wishes to be listed on a search engine. These can sometimes contain areas of the website that are a bit more difficult to navigate to or even list some old webpages.

### Task 5 Manual Discovery - HTTP Headers
The HTTP headers can sometimes be useful to determine what programming/scripting language was used to build the website or the webserver software such as Apache or Nginx. We can `curl http://<ip> -v` to get verbose and return the header information.

### Task 6 Manual Discovery - Framework Stack
Once we have determined the framework of a website, you can locate the framework's website. From there, we can learn more about the software and other information, possible leading to more content we can discover.

### Task 7 OSINT - Google Hacking / Dorking
This involves utilizing Google's advanced search engine features, which allow you to pick out custom content. You can, for instance pick out results from a certain domain name, using the **site** filter ex) `site:tryhackme.com`

Filters:
- site -> site:tryhackme.com -> results only from specified web address
- inurl -> inurl:admin -> returns results that have the specified word in the URL
- filetype -> filetype:pdf -> particular file extension type
- intitle -> intitle:admin -> returns results that contain specified word in the title of page

### Task 8 OSINT - Wappalyzer
An online tool and browser extension that helps identify the technologies that a website uses.

### Task 9 OSINT - Wayback Machine
A historical archive of websites that dates back to the late 90s. You can search a domain name, and it will show you all the times the service scraped the web page and saved the contents.

https://archive.org/web/

### Task 10 OSINT - GitHub

Based on the technology called Git. It is a **version control system** that tracks changes to a files in a project. This is makes working in teams easier and you can see what each team member and what changes have been made to a file. The central location is called a **repository** for the other users to then pull those changes to their local machines.

### Task 11 OSINT - S3 buckets
Storage device provided by AWS, allowing people to save files and even static webiste content in the cloud accessible over HTTP and HTTPS. The owner of the files can set access permissions to either make files public, private and even writable. Sometimes these access permissions are set incorrectly and inadvertently allow access to files that shouldn't be available to the public.

The format of S3 buckets is http(s)://{**name**}**s3.amazonaws.com** where {name} is decided by the owner

### Task 12 Automated Discovery
Using tools such as ffuf, dirb, and gobuster to automate the process of finding things like directories, subdomains, files, etc.