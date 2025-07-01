File upload vulnerabilities are when a web server allows users to upload files to its filesystem without sufficiently validating things like their name, type, contents, or size. Failing to properly enforce restrictions on these could mean that even a basic image upload function can be used to upload arbitrary and potentially dangerous files instead.

# Impact of file upload vulnerabilities
RCE	Uploading web shells to run system commands
Data Theft	Reading configs, databases, secrets
Malware Distribution	Hosting malware or backdoors
XSS / Client Attacks	Uploading malicious HTML/SVG/JS files
Auth Bypass	Replacing or hijacking authentication mechanisms
DoS	Crashing services or filling disk space
Persistent Backdoors	Maintaining long-term control
Reputation Damage	Blacklisting, SEO damage, legal risks

# How Web Servers Handle Requests for Static Files
What are Static Files?
Static files are files that don’t change when different users request them. These include:
Images (.jpg, .png)
CSS stylesheets (.css)
JavaScript files (.js)
Fonts, PDFs, videos, etc.
They are not processed by the backend — they are just fetched and served.
How Web Servers Handle Static File Requests:
1. Parse the URL Path - Maps it to a file path on the server’s filesystem.
2. Identify File Extension
3. Map Extension to MIME Type - Server uses config to assign a Content-Type header.
4. Serve or Execute Based on Type & Server Config:
- Non-executable (static): File is sent directly (e.g., image, HTML).
- Executable + execution allowed: File is processed (e.g., .php → PHP interpreter).
- Executable + execution not allowed:
Server throws an error Or Sends raw source code (leads to information disclosure).

Key Headers:
Content-Type: Shows MIME type of file (e.g., text/html, image/png).


Web shell
A web shell is a malicious script that enables an attacker to execute arbitrary commands on a remote web server simply by sending HTTP requests to the right endpoint.

# Exploiting unrestricted file uploads to deploy a web shell:
1. The following PHP one-liner could be used to read arbitrary files from the server's filesystem
<?php echo file_get_contents('/path/to/target/file'); ?> 
Once uploaded, sending a request for this malicious file will return the target file's contents in the response. 
2.A more versatile web shell may look something like this:
<?php echo system($_GET['command']); ?>
This script enables you to pass an arbitrary system command via a query parameter as follows:
GET /example/exploit.php?command=id HTTP/1.1

# lab 001
Remote code execution via web shell upload
ques: This lab contains a vulnerable image upload function. It doesn't perform any validation on the files users upload before storing them on the server's filesystem. 
How to proceed
1. upload a basic PHP web shell
2. use it to exfiltrate the contents of the file /home/carlos/secret
How did I do it 
1. Under HTTP history filter window, Under Filter by MIME type, we enabled the Images checkbox. Upload any random image. 
2. Image was fetched using a GET request to /files/avatars/<YOUR-IMAGE>
3. Created exploit.php, containing a script for fetching the contents of Carlos's secret file.
<?php echo file_get_contents('/home/carlos/secret'); ?> and then uplod it. 
4. Change the path of the request to point to your PHP file:
GET /files/avatars/exploit.php HTTP/1.1





#  Exploiting Flawed validation of file upload
multipart/form-data (HTML form submission)
Used for: Sending files or large binary data (e.g., image, PDF, ZIP).
Structure:
1. The request body is split into multiple parts.
Each part contains:
- A boundary (used to separate parts).
- A Content-Disposition header: Specifies field name and filename (if a file).
- Optional Content-Type for each part (e.g., image/jpeg).

# lab 002
Lab: Web shell upload via Content-Type restriction bypass
This lab contains a vulnerable image upload function. It attempts to prevent users from uploading unexpected file types, but relies on checking user-controllable input to verify this. 

Simply open burp suite and upload the .php file and then change the content type as only image/png or image/jpeg are allowed afterwards you can change the path in the get request to exploit.php like the previous question witha single change that is changing the content type.




As a precaution, servers generally only run scripts whose MIME type they have been explicitly configured to execute. Otherwise, they may just return some kind of error message or, in some cases, serve the contents of the file as plain text instead:
GET /static/exploit.php?command=id HTTP/1.1
Host: normal-website.com
A user is accessing a PHP file (exploit.php) and passing a command=id query parameter.
<?php echo system($_GET['command']); ?>
The PHP code takes the command parameter from the URL and runs it on the server using the system() function.
Why It's Dangerous (RCE)
system() executes OS commands. So a user can do:
?command=ls
?command=whoami
?command=rm -rf /
This allows an attacker to run arbitrary commands on the server, which is a critical security vulnerability.



# lab 003
Web shell upload via path traversal
This lab contains a vulnerable image upload function. The server is configured to prevent execution of user-supplied files, but this restriction can be bypassed by exploiting a secondary vulnerability. 

For this lab we uploaded the ../exploit.php file and it got uploaded Notice that the response says The file avatars/exploit.php has been uploaded. This suggests that the server is stripping the directory traversal sequence from the file name.
and then we tried to upload it using obsfucation ..%2fexploit.php and it gets uploaded Server URL-decodes and saves file outside /avatars/, under /files/. Accessing /files/exploit.php executes the script.


# lab 004 

Insufficient blacklisting of dangerous file types
Blocking dangerous extensions like .php is common, but flawed.
Attackers can bypass it using alternative executable extensions like .php5, .phtml, or .shtml.
We tried uploading the exploit.php file and it didnt got uploaded then we noticed the response and we found that its a apache server 
Apache supports a config file called .htaccess that can change behavior of how files are handled in a directory.
( if it were a IIS server is uses web.config file )
You upload a malicious .htaccess file that tells Apache:
AddType application/x-httpd-php .l33t
This tells the server: "Treat .l33t files like PHP and execute them." Now, you upload your PHP exploit again, but this time name it exploit.l33t. When you request /files/avatars/exploit.l33t, Apache executes it as PHP and gives you Carlos’s secret.



Obfuscating file extensions
Common Obfuscation Techniques
1. Case Variation
-  exploit.pHp may bypass case-sensitive filters but still be run as .php.
2. Double Extensions
-  exploit.php.jpg might be checked as .jpg but handled as .php depending on how the server parses it.
3. railing Characters
-  Exploit.php. (with a dot or space) might be ignored during file checks but trimmed by the server when saving.
4. URL Encoding
-  exploit%2Ephp → %2E = ., may bypass validation and be decoded later by the server.
5. Semicolons or Null Bytes
-  exploit.asp;.jpg or exploit.asp%00.jpg — null byte or ; tricks some servers into ignoring the second extension.
6. Multibyte Unicode
- Use characters like \xC0\x2E that may become a dot (.) after decoding, causing the file to be treated as .php.
7. Using exploit.p.phphp

# lab 005
Lab: Web shell upload via obfuscated file extension
This lab contains a vulnerable image upload function. Certain file extensions are blacklisted, but this defense can be bypassed using a classic obfuscation technique. 

To solve this lab, we took the request in the burp and then analysed it and we found that it doesnot aloow nay other file type other than JPG and PNG so we tried obfuscating the file extention first i tried exploit.png.jpg it got uploaded but we were not able to get the carlos secret then we  tried another way of obusfcation that is using thr null byte   exploit.php%00.jpg and then again it got uploaded and we were able to see carlos secret from the get request. 


Flawed validation of the file's contents





