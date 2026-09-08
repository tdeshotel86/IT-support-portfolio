# IT-support-portfolio
Virtualized enterprise Active Directory lab (corp.local) featuring Windows Server 2022 and Windows 11. Demonstrates automated OU hierarchy deployment, RBAC security groups, PowerShell administration, NTFS/SMB permissions, and verified negative access control.
Built an enterprise Active Directory domain environment inside an isolated VirtualBox private NAT network (corp.local, 192.168.10.0/24) to simulate real-world IT Tier I/II systems administration and access governance.

Key Technical Implementation & Troubleshooting:
• Workstation Lifecycle & Firmware Remediation: Resolved UEFI bus enumeration conflicts (IncompatiblePciDeviceSupportDxe.efi) by reconfiguring VM device models. Bypassed consumer Windows 11 hardware checks in WinPE via registry flags (LabConfig).
• Network Stack & Directory Discovery: Diagnosed Layer 3 egress "General failure" errors via netsh IP resets and route table flushes. Configured DNS point records (192.168.10.10) to enable AD discovery and automated domain joining via PowerShell.
• Directory Architecture: Designed an enterprise OU hierarchy (CorpEnterprise) to isolate departments, administrative principals, and workstations (CL-02) from unmanaged default containers.
• Role-Based Access Control (RBAC): Automated departmental Global Security Groups (SG-IT-Staff, SG-HR-Staff) and provisioned user accounts (jdoe, asmith). Diagnosed LSASS Kerberos PAC token generation constraints versus service ticket flushes (klist purge).
• Storage Security & Empirical Validation: Configured an enterprise SMB file share on DC-01 with open share permissions restricted by granular NTFS DACLs. Proved least privilege using empirical testing: validated read/modify/write access for IT personnel and verified negative access control (Access Denied) for unauthorized departmental accounts.
<img width="1036" height="771" alt="Screenshot 2026-09-06 140331" src="https://github.com/user-attachments/assets/5894077d-5adc-4431-ae9a-9921f22c54ea" />
<img width="1044" height="757" alt="Screenshot 2026-09-06 153732" src="https://github.com/user-attachments/assets/736bc19d-bc01-4952-bde1-83e478bfc38d" />
<img width="1005" height="676" alt="Screenshot 2026-09-06 215631" src="https://github.com/user-attachments/assets/6913ca77-d581-4f48-9d30-bdeece2e1cc9" />
<img width="1028" height="758" alt="Screenshot 2026-09-07 205026" src="https://github.com/user-attachments/assets/a750a9f3-e724-4d90-b99f-1e2fa3401ffd" />
<img width="1911" height="1028" alt="Screenshot 2026-09-07 211044" src="https://github.com/user-attachments/assets/1615f8ee-3d0e-48f4-a924-1834927cdea5" />
<img width="1224" height="832" alt="Screenshot 2026-09-07 212431" src="https://github.com/user-attachments/assets/aad589c3-b83e-4331-a8c8-a45482fabb5e" />

## Production Infrastructure & Web Operations: Total Care Squad
**Live URL:** [totalcaresquad.com](https://totalcaresquad.com)  
**Role:** Systems & Operations Lead / Web Administrator  

### Technical Implementation & Infrastructure
* **Domain & DNS Management:** Architected DNS zone configurations including A, CNAME, and MX records to route traffic and establish production email services.
* **Email Security & Deliverability:** Configured and validated SPF, DKIM, and DMARC records to secure corporate domain identity, mitigate spoofing risks, and optimize inbound/outbound deliverability.
* **Security & Availability:** Enforced automated HTTPS/TLS encryption certificates to protect user session confidentiality and maintain web security compliance.
* **Service Lifecycle:** Managed end-to-end site staging, deployment, uptime monitoring, and client-facing service catalog documentation.


### 🤖 Autonomous AI IT Receptionist & Intake Portal ("Nico")
* **Live Interactive Deployment:** [Total Care Squad Receptionist](https://totalcaresquad-receptionist.streamlit.app)
* **Source Code:** [github.com/tdeshotel86/tcs-ai-receptionist](https://github.com/tdeshotel86/tcs-ai-receptionist)
* **Architecture:** Streamlit Cloud | Python | Google GenAI SDK (`gemini-2.0-flash`) | SMTP Automation
* **Key Features:**
  * **Automated Tier-1 Intake:** Triages requests across 5 technical service tracks (Cybersecurity, Surveillance Installation, Custom Web, IT Troubleshooting, and AI Receptionist Setup).
  * **Structured Function Calling:** Translates unstructured client chat into typed schemas (`FunctionDeclaration`) for automated dispatch.
  * **Automated Dispatch:** Dispatches formatted incident tickets directly to technical dispatch via secure SMTP without third-party middleware.
  * **High-Availability Fault Tolerance:** Implements model cascade fallbacks to handle API rate limits (429) and upstream server capacity spikes (503).

<img width="1899" height="995" alt="Screenshot 2026-09-08 170213" src="https://github.com/user-attachments/assets/a32d47b4-ed7a-476b-8ed5-3337e846ee65" />
<img width="1882" height="998" alt="Screenshot 2026-09-08 164305" src="https://github.com/user-attachments/assets/b78e7580-5eef-4f96-b833-4d4c1909d5f2" />


