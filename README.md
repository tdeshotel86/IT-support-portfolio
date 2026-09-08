# IT-support-portfolio
Virtualized enterprise Active Directory lab (corp.local) featuring Windows Server 2022 and Windows 11. Demonstrates automated OU hierarchy deployment, RBAC security groups, PowerShell administration, NTFS/SMB permissions, and verified negative access control.
Built an enterprise Active Directory domain environment inside an isolated VirtualBox private NAT network (corp.local, 192.168.10.0/24) to simulate real-world IT Tier I/II systems administration and access governance.

Key Technical Implementation & Troubleshooting:
• Workstation Lifecycle & Firmware Remediation: Resolved UEFI bus enumeration conflicts (IncompatiblePciDeviceSupportDxe.efi) by reconfiguring VM device models. Bypassed consumer Windows 11 hardware checks in WinPE via registry flags (LabConfig).
• Network Stack & Directory Discovery: Diagnosed Layer 3 egress "General failure" errors via netsh IP resets and route table flushes. Configured DNS point records (192.168.10.10) to enable AD discovery and automated domain joining via PowerShell.
• Directory Architecture: Designed an enterprise OU hierarchy (CorpEnterprise) to isolate departments, administrative principals, and workstations (CL-02) from unmanaged default containers.
• Role-Based Access Control (RBAC): Automated departmental Global Security Groups (SG-IT-Staff, SG-HR-Staff) and provisioned user accounts (jdoe, asmith). Diagnosed LSASS Kerberos PAC token generation constraints versus service ticket flushes (klist purge).
• Storage Security & Empirical Validation: Configured an enterprise SMB file share on DC-01 with open share permissions restricted by granular NTFS DACLs. Proved least privilege using empirical testing: validated read/modify/write access for IT personnel and verified negative access control (Access Denied) for unauthorized departmental accounts.
<img width="1224" height="832" alt="Screenshot 2026-09-07 212431" src="https://github.com/user-attachments/assets/f8651b7d-44e6-4933-8071-9ae41392bc54" />
<img width="1911" height="1028" alt="Screenshot 2026-09-07 211044" src="https://github.com/user-attachments/assets/ce9aa0ad-c019-48ce-827e-85fe8a092362" />
<img width="1028" height="758" alt="Screenshot 2026-09-07 205026" src="https://github.com/user-attachments/assets/5a5ec5da-cdb1-4a90-b41e-20af6e40a47a" />
<img width="1005" height="676" alt="Screenshot 2026-09-06 215631" src="https://github.com/user-attachments/assets/9e48f67d-d622-44a6-9819-823e8937c0ae" />
<img width="1044" height="757" alt="Screenshot 2026-09-06 153732" src="https://github.com/user-attachments/assets/a9852cd2-4d81-49ab-bba2-edab507cfb0d" />
<img width="1036" height="771" alt="Screenshot 2026-09-06 140331" src="https://github.com/user-attachments/assets/6db18d2b-1207-4fd2-bd96-01612bae7cdf" />
