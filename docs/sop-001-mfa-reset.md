[SOP-mfa-reset.docx](https://github.com/user-attachments/files/31988303/SOP-mfa-reset.docx)
SOP: Out-of-Band MFA Reset & Identity Verification Protocol
1. Purpose & Scope
Establishes a mandatory verification procedure for resetting Multi-Factor Authentication (MFA) tokens or clearing authenticator lockouts to prevent social engineering, SIM-swapping, and credential harvesting. Applies to all Service Desk personnel.
2. Core Requirements
•	Never clear an MFA token or issue a temporary access pass based solely on an inbound phone call, SMS, or unauthenticated chat.
•	Out-of-band callback is mandatory for all requests.
3. Execution Steps
•	Step 1: Ticket Logging & Intake
Log an incident ticket with the user's corporate email, employee ID, and reported issue.
•	Step 2: HR/Directory Validation
Cross-reference the user's contact information in Active Directory/Entra ID with the internal HR record. Confirm:
o	Full Legal Name
o	Employee ID Number
o	Direct Manager Name
•	Step 3: Out-of-Band Callback Verification
Terminate the inbound call. Initiate a direct callback to the employee's pre-registered phone number on file in HR/Active Directory (or initiate a video call via Microsoft Teams/Zoom to visually confirm corporate badge ID).
•	Step 4: Manager Dual-Authorization
For remote personnel without access to video verification, obtain written approval from the listed department manager via internal corporate messaging or an approved ticketing escalation.
•	Step 5: Token Reset & Temporary Access Pass (TAP)
Navigate to Microsoft Entra Admin Center > Users > select target user > Authentication Methods:
o	Revoke existing MFA sessions.
o	Generate a Temporary Access Pass (TAP) with a maximum validity window of 1 hour, marked as One-Time Use.
•	Step 6: User Re-Enrollment Guidance
Remain on the line while the user signs in to mysignins.microsoft.com using the TAP and pairs a new authenticator device.
•	Step 7: Closure & Audit Trail
Document the callback timestamp, verification method used, and manager authorization in the ticket notes. Close the ticket as Resolved.

