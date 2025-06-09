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

# Impact - Severe ( Acesseing accounts you are not supposed to worst case you get to access to preveliged accounts like admin.
