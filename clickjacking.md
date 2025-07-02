Clickjacking is an interface-based attack in which a user is tricked into clicking on actionable content on a hidden website by clicking on some other content in a decoy website.

Clickjacking	                                                       
Definition- Tricking users into clicking something hidden or disguised
CSRF (Cross-Site Request Forgery)
Forcing a user to perform actions without their intent

Clickjacking	                                                       
The user interface (visual deception)
CSRF (Cross-Site Request Forgery)
The user's session/authentication with a target website

Clickjacking	
User interaction-Yes — user must click on something	
CSRF (Cross-Site Request Forgery)
Not always — can be triggered automatically

Clickjacking	
Attack method	Hiding a legitimate site in an invisible iframe	
CSRF (Cross-Site Request Forgery)
Sending forged requests using the user's cookies

Clickjacking	
Target- Any button or link the attacker can visually hijack	
CSRF (Cross-Site Request Forgery)
State-changing actions (e.g., money transfer, password change)

Clickjacking	
Doesn’t rely on authentication	
CSRF (Cross-Site Request Forgery)
Requires user to be logged in to target site

Clickjacking	
Main defense: X-Frame-Options, Content-Security-Policy (frame-ancestors)	
CSRF (Cross-Site Request Forgery)
Anti-CSRF tokens, SameSite cookies, origin checks

Clickjacking	
Impact example	Trick user into clicking “Delete Account” button
CSRF (Cross-Site Request Forgery)
Example scenario	Fake "Play" button overlays a "Buy Now" button

Clickjacking	
Silent POST request to change email/password without consent
CSRF (Cross-Site Request Forgery)
Auto-submit form transfers money from victim’s bank account


# How to construct a basic clickjacking attack
Clickjacking attacks use CSS to create and manipulate layers. The attacker incorporates the target website as an iframe layer overlaid on the decoy website.







