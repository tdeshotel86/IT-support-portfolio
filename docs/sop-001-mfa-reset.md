# SOP-001: Out-of-Band MFA Reset & Identity Verification Protocol

## 1. Purpose & Scope
Establishes a mandatory verification procedure for resetting Multi-Factor Authentication (MFA) tokens or clearing authenticator lockouts, mitigating social engineering, SIM-swapping, and credential harvesting threats. Applies to all Service Desk and Tier I/II IT personnel.

---

## 2. Core Operational Requirements
> **CRITICAL SECURITY DIRECTIVE:** Never clear an MFA token, unbind a security key, or issue a temporary access pass based solely on an inbound phone call, SMS, or unauthenticated chat session. An out-of-band verification callback is mandatory for all requests without exception.

---

## 3. Step-by-Step Execution Workflow

### Step 1: Ticket Logging & Intake
* Log an incident ticket in the service management portal with the user's corporate email, employee ID, and reported issue.
* Set category to `Access & Identity Management` > `MFA Remediation`.

### Step 2: HR & Directory Cross-Validation
* Search for the user in **Active Directory / Microsoft Entra ID**.
* Cross-reference directory contact data against the corporate HR database to verify:
  * Full Legal Name
  * Employee ID Number
  * Official Title & Department
  * Reporting Manager

### Step 3: Out-of-Band Verification Callback
* Terminate the inbound call or chat session immediately.
* Place an outbound call directly to the employee's pre-registered corporate phone number on file.
* *Alternative Video Verification:* If the employee is fully remote without a registered secondary phone, initiate a Microsoft Teams video call to visually confirm the requester's corporate badge ID and face.

### Step 4: Manager Dual-Authorization
* If the user cannot complete secondary voice/video verification, trigger an approval request to the user’s designated department manager via corporate email or internal ticketing escalation.
* Verification must not proceed without documented managerial sign-off.

### Step 5: Session Revocation & Temporary Access Pass (TAP)
* In the **Microsoft Entra Admin Center**, navigate to **Users** > select target user > **Authentication Methods**.
* Click **Revoke MFA sessions** to invalidate all active session tokens across registered devices.
* Select **Add authentication method** > choose **Temporary Access Pass (TAP)**.
* Configure TAP parameters:
  * **Activation Duration:** 1 hour maximum
  * **One-time use:** Enabled
* Securely convey the one-time TAP to the verified user over the active out-of-band call.

### Step 6: Guided User Re-Enrollment
* Instruct the employee to navigate to `https://mysignins.microsoft.com` in a private/incognito browser window.
* Guide the user through authenticating with the TAP and pairing their new authenticator application or FIDO2 security token.
* Confirm that push notifications and conditional access policies pass inspection.

### Step 7: Ticket Documentation & Audit Trail
* Document the following verification artifacts in the private ticket work notes:
  * Out-of-band phone number dialed or Teams video call timestamp
  * Manager authorization ticket ID (if applicable)
  * TAP issuance confirmation timestamp
* Update ticket status to **Resolved** and notify the user.


