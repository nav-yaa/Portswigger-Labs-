# Authentication 
Verifying the identity of thr user. 
## Types 
- Based on knowledge factors - Something you might know like a password or security answer
- Based on possession factors - Something you might have like a mobile device or security token 
- Based on inherence factors - Something you are like biometerics or pattern of behaviour

# Difference between authentication and authorization
- Authorization - Who are you?
- Autehtication - What are you allowed to do?

# How do authentication vulnerabilities arise?

 ## 1. Weak Authentication Mechanisms
 - No or weak rate limiting (brute-force or credential stuffing).
 - Simple/default passwords allowed (admin/admin).
 - No multi-factor authentication (MFA).
 - Poor password policy (short, no complexity, no expiry).

 ## 2. Logic Flaws or Poor Implementation (Broken Authentication)
 - Session management flaws (like predictable session IDs).
 - Authentication bypass via unvalidated redirects or insecure endpoints.
 - Password reset flaws (like no token expiry or token reusability).
 - Improper implementation of OAuth or JWT (e.g., accepting unsigned tokens).
 - Misconfigured access controls on protected resources (e.g., user can directly access /admin).

# Impact - Severe 
Acesseing accounts you are not supposed to worst case you get to access to preveliged accounts like admin.

# Username enumeration
While attempting to brute-force , you should pay particular attention to: 
- Status codes
- Error messages
- Response times

# Lab 01
Username enumeration via different responses
Brute Force attack, for the username we will check for Incorrect password in the response section, and for passwords we will check for the response code, 302 amongst all the 200s.

# Lab 02
Username enumeration via subtly different responses
Brute Force attack, for username well check for punctuation error in "Invalid username or password." we can use grep for this. For password check the response code, 302 amongst 200s.

# Lab 03
Username enumeration via response timing
Does the site start rejecting after a few tries from same IP?
Use X-Forwarded-For Header 
X-Forwarded-For is an HTTP header used to indicate the original IP address of a client when the request goes through a proxy or load balancer.
We can check after 3-4 invalid logins the ip gets blocked we can use the header for this. We can then use Pitchfork attack and change the values for the header and the username keeping th password intact (long string password). From these two columns Response received and Response completed options we can guess the username then the same proccess for password but this time check the response code, 302 amongst 200s.




















