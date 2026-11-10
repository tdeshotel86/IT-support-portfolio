# IT Support Portfolio: Terrell Deshotel

Hands-on Tier I/II IT support work: service desk SOPs, an enterprise Active Directory lab, documented incident resolutions, production web and email infrastructure, and Python scripting.

## Contents
* [Standard Operating Procedures (SOPs)](#standard-operating-procedures-sops)
* [Enterprise Active Directory Lab](#enterprise-active-directory-lab-corplocal)
* [Incident Reports](#incident-reports)
  * [Server Manager Cache Corruption & GUI Fault](#incident-server-manager-cache-corruption--gui-fault)
  * [TCP/IP & DHCP Network Stack Recovery](#incident-tcpip--dhcp-network-stack-recovery)
* [Production Infrastructure: Total Care Squad](#production-infrastructure--web-operations-total-care-squad)
* [Python Scripting (SNHU IT 140)](#python-scripting--developer-workstation-setup-snhu-it-140)

## Standard Operating Procedures (SOPs)
Service desk runbooks written for Tier I/II support teams. Each SOP defines its scope, the security directives that must be followed, and a numbered execution workflow with the commands to run.

| SOP | Procedure | Focus Area |
| :--- | :--- | :--- |
| [SOP-001](docs/sop-001-mfa-reset.md) | Out-of-Band MFA Reset & Identity Verification | Identity & Access Management, Social Engineering Defense |
| [SOP-002](docs/sop-002-vpn-diagnostics.md) | Remote End-User VPN Diagnostics & Tunnel Remediation | Remote Access, Networking |
| [SOP-003](docs/sop-003-endpoint-isolation.md) | Suspected Endpoint Compromise & Triage Isolation | Security Incident Response |
| [SOP-004](docs/sop-004-print-spooler.md) | Windows Print Spooler Crash Recovery & Driver Remediation | Windows Endpoint Support |

Full index: [docs/index.md](docs/index.md)

## Enterprise Active Directory Lab (corp.local)
Virtualized enterprise Active Directory lab (corp.local) featuring Windows Server 2022 and Windows 11. Demonstrates automated OU hierarchy deployment, RBAC security groups, PowerShell administration, NTFS/SMB permissions, and verified negative access control.

Built an enterprise Active Directory domain environment inside an isolated VirtualBox private NAT network (corp.local, 192.168.10.0/24) to simulate real-world IT Tier I/II systems administration and access governance.

### Key Technical Implementation & Troubleshooting
* **Workstation Lifecycle & Firmware Remediation:** Resolved UEFI bus enumeration conflicts (`IncompatiblePciDeviceSupportDxe.efi`) by reconfiguring VM device models. Bypassed consumer Windows 11 hardware checks in WinPE via registry flags (`LabConfig`).
* **Network Stack & Directory Discovery:** Diagnosed Layer 3 egress "General failure" errors via `netsh` IP resets and route table flushes. Configured DNS point records (192.168.10.10) to enable AD discovery and automated domain joining via PowerShell.
* **Directory Architecture:** Designed an enterprise OU hierarchy (CorpEnterprise) to isolate departments, administrative principals, and workstations (CL-02) from unmanaged default containers.
* **Role-Based Access Control (RBAC):** Automated departmental Global Security Groups (SG-IT-Staff, SG-HR-Staff) and provisioned user accounts (jdoe, asmith). Diagnosed LSASS Kerberos PAC token generation constraints versus service ticket flushes (`klist purge`).
* **Storage Security & Empirical Validation:** Configured an enterprise SMB file share on DC-01 with open share permissions restricted by granular NTFS DACLs. Proved least privilege using empirical testing: validated read/modify/write access for IT personnel and verified negative access control (Access Denied) for unauthorized departmental accounts.

<details>
<summary>📸 Screenshots (6)</summary>

<img width="1036" height="771" alt="Screenshot 2026-09-06 140331" src="https://github.com/user-attachments/assets/5894077d-5adc-4431-ae9a-9921f22c54ea" />
<img width="1044" height="757" alt="Screenshot 2026-09-06 153732" src="https://github.com/user-attachments/assets/736bc19d-bc01-4952-bde1-83e478bfc38d" />
<img width="1005" height="676" alt="Screenshot 2026-09-06 215631" src="https://github.com/user-attachments/assets/6913ca77-d581-4f48-9d30-bdeece2e1cc9" />
<img width="1028" height="758" alt="Screenshot 2026-09-07 205026" src="https://github.com/user-attachments/assets/a750a9f3-e724-4d90-b99f-1e2fa3401ffd" />
<img width="1911" height="1028" alt="Screenshot 2026-09-07 211044" src="https://github.com/user-attachments/assets/1615f8ee-3d0e-48f4-a924-1834927cdea5" />
<img width="1224" height="832" alt="Screenshot 2026-09-07 212431" src="https://github.com/user-attachments/assets/aad589c3-b83e-4331-a8c8-a45482fabb5e" />

</details>

## Incident Reports

### Incident: Server Manager Cache Corruption & GUI Fault
**Role:** Tier I/II Technical Support Specialist  
**System / Target:** Oracle VirtualBox 7.x | Guest VM: DC-01 (Windows Server Active Directory Domain Controller)  
**Environment:** Isolated Active Directory Lab (LabNetwork)

#### 1. Issue Summary & Initial Symptoms
* **Application Startup Failure:** Upon logging into the Windows Server GUI, Server Manager launched automatically but immediately threw a critical pop-up error dialog: “Server Manager cannot load the server list. Click OK to reset the server list and continue.”
* **Persistent Error Loop:** Clicking "OK" temporarily cleared the dialogue, but restarting Server Manager or rebooting the server caused the error loop to repeat indefinitely.
* **Management Impairment:** The dashboard failed to poll local and remote server roles, rendering Server Manager unable to display server health, event logs, or manage Active Directory services.

#### 2. Root Cause Analysis (RCA)
* **XML Cache Corruption:** The local user profile's XML configuration file (`ServerList.xml`) responsible for tracking managed servers became corrupted, truncated, or malformed.
* **Trigger Mechanism:** This typically occurs due to an unexpected power state change, virtual machine save-state failure, or an incomplete write operation while Server Manager was open.
* **File Path:** The corrupted configuration file was isolated to the profile path:
  `C:\Users\Administrator\AppData\Roaming\Microsoft\Windows\ServerManager\ServerList.xml`

#### 3. Step-by-Step Resolution & Workarounds
* **GUI Reset Alternative:** Selected OK on the error prompt to instruct Server Manager to automatically overwrite and reset the corrupted XML file to a fresh default state.
* **Administrative PowerShell Clean-Up:** Opened PowerShell as an Administrator and executed automated cleanup scripts to clear hung processes and purge the corrupted cache file:

```powershell
Get-Process ServerManager -ErrorAction SilentlyContinue | Stop-Process -Force
Remove-Item -Path "$env:APPDATA\Microsoft\Windows\ServerManager\ServerList.xml" -Force -ErrorAction SilentlyContinue
Start-Process -FilePath "$env:SystemRoot\System32\ServerManager.exe" -WindowStyle Maximized
```

* **Verification:** Observed that Server Manager generated a fresh, uncorrupted XML file upon launch, bypassed the error loop, and successfully resumed polling local server roles.

#### 4. Core Competencies Demonstrated
* **Windows Server Administration:** Diagnosed and resolved Microsoft Management Console (MMC) and dashboard-level corruption on an Active Directory Domain Controller.
* **File System & Profile Troubleshooting:** Located user-specific AppData configurations and successfully cleared application state caches via PowerShell.
* **Administrative Scripting:** Applied process termination and file-management cmdlets to quickly remediate software faults without requiring an OS reinstall.

<details>
<summary>📸 Screenshots (2)</summary>

<img width="406" height="230" alt="Screenshot 2026-09-16 115508" src="https://github.com/user-attachments/assets/2d07dc59-88a4-407b-a3c2-a149216d3041" />
<img width="1022" height="770" alt="Screenshot 2026-09-16 121051" src="https://github.com/user-attachments/assets/9978f13c-1059-4ce8-a7de-54d8ffd69d76" />

</details>

### Incident: TCP/IP & DHCP Network Stack Recovery
**Role:** Tier I Technical Support Specialist  
**System / Target:** Oracle VirtualBox 7.x | Guest VM: CL-02 (Windows 11 Enterprise 64-bit)  
**Environment:** Isolated Active Directory Lab (LabNetwork)

#### 1. Issue Summary & Initial Symptoms
* **Domain Authentication Failure:** Administrative UAC elevation failed with the error “We can't sign you in with this credential because your domain isn't available” when attempting to authenticate against CORP\Admin.
* **DHCP Command Failure:** Running `ipconfig /release` or `ipconfig /renew` returned a critical system error: “The operation failed as no adapter is in the state permissible for this operation.”
* **Visual Indicators:** Network stack communication was entirely severed, preventing access to the local domain controller and external resources.

#### 2. Root Cause Analysis (RCA)
* **Disabled Link State:** The guest operating system's virtual Ethernet adapter was in a disabled or disconnected physical state (Status: Disconnected), preventing Windows from processing IP configuration commands or broadcasting DHCP requests.
* **Hypervisor-Guest Desync:** Loss of network connectivity directly blocked Active Directory domain trust verification during administrative operations.

#### 3. Step-by-Step Resolution & Commands
1. **Hypervisor Adapter Verification:** Confirmed VM Adapter 1 was properly attached to the designated LabNetwork NAT scope in VirtualBox settings.
2. **Adapter State Diagnostics (PowerShell):** Launched PowerShell as Administrator and queried the local interface status:
   ```powershell
   Get-NetAdapter
   ```
   *Diagnostic finding: Adapter interface was present via Intel PRO/1000 MT Desktop Adapter but required activation.*
3. **Interface Activation:** Force-enabled the network adapter interface to restore physical link state:
   ```powershell
   Enable-NetAdapter -Name "Ethernet" -Confirm:$false
   ```
4. **DHCP Lease Renewal:** Executed the IP configuration renewal utility to request a fresh lease from the virtual DHCP server:
   ```powershell
   ipconfig /renew
   ```
5. **Verification Result:** The adapter successfully negotiated a dynamic IPv4 configuration (192.168.10.4), established a valid subnet mask (255.255.255.0), and restored default gateway routing (192.168.10.1).

#### 4. Core Competencies Demonstrated
* **PowerShell Networking Cmdlets:** Utilized `Get-NetAdapter` and `Enable-NetAdapter` to diagnose and programmatically recover interface link states.
* **TCP/IP & DHCP Troubleshooting:** Resolved enterprise network failure states, corrected adapter state blocks, and validated IP lease acquisition.
* **Active Directory Environment Troubleshooting:** Correlated local network reachability failures with domain credential authentication errors.

<details>
<summary>📸 Screenshots (9)</summary>

<img width="1912" height="1027" alt="Screenshot 2026-09-16 122444" src="https://github.com/user-attachments/assets/e6d261ee-c730-46e0-a44f-9443bf29904f" />
<img width="1891" height="1031" alt="Screenshot 2026-09-16 122502" src="https://github.com/user-attachments/assets/152bea7c-daf4-48dd-bc19-fa7f557560e0" />
<img width="1900" height="1030" alt="Screenshot 2026-09-16 122520" src="https://github.com/user-attachments/assets/6400f32e-80e6-4e50-b6c6-297775e83005" />
<img width="1025" height="767" alt="Screenshot 2026-09-16 123015" src="https://github.com/user-attachments/assets/d6a47d40-b0ed-48b3-981b-6c4491fc3b8d" />
<img width="1020" height="768" alt="Screenshot 2026-09-16 123219" src="https://github.com/user-attachments/assets/617a9baa-1f6a-4979-80df-3cdb7725d191" />
<img width="1020" height="770" alt="Screenshot 2026-09-16 123439" src="https://github.com/user-attachments/assets/2f1d05e5-4c46-48d1-a144-8f6930d8d46c" />
<img width="1020" height="768" alt="Screenshot 2026-09-16 124207" src="https://github.com/user-attachments/assets/0ddb5f30-cf48-4e6a-95da-fe4286806f37" />
<img width="1038" height="782" alt="Screenshot 2026-09-16 124814" src="https://github.com/user-attachments/assets/75730723-a64e-445d-bf32-7c0a05498fc2" />
<img width="1031" height="772" alt="Screenshot 2026-09-16 125225" src="https://github.com/user-attachments/assets/28565491-2f64-42ea-af74-1fecb6db19c4" />

</details>

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

<details>
<summary>📸 Screenshots (2)</summary>

<img width="1899" height="995" alt="Screenshot 2026-09-08 170213" src="https://github.com/user-attachments/assets/a32d47b4-ed7a-476b-8ed5-3337e846ee65" />
<img width="1882" height="998" alt="Screenshot 2026-09-08 164305" src="https://github.com/user-attachments/assets/b78e7580-5eef-4f96-b833-4d4c1909d5f2" />

</details>

## Python Scripting & Developer Workstation Setup (SNHU IT 140)
**Course:** IT 140 – Introduction to Scripting, Southern New Hampshire University  
**Project:** Module Two – Software Development Introduction (Grade: 35/35)  
**Stack:** Python 3.14 | Visual Studio Code | Git | GitHub CLI (`gh`) | Git Bash | PowerShell

### Project Summary
Built a Python command-line program that collects user input, converts data types, and calculates a birth year from the system clock (`datetime.date.today().year`) instead of a hardcoded value, so the script stays accurate in any year. Followed the Software Development Life Cycle (analysis, design, construction, testing, debugging) with a documented module docstring, a `main()` entry point, and a `__name__ == "__main__"` guard. Source code is kept in a private course repository under SNHU academic integrity guidelines.

### Workstation Provisioning & Troubleshooting
* **Toolchain Deployment:** Diagnosed a missing GitHub CLI (`gh: command not found`) and deployed it with the Windows Package Manager (`winget install --id GitHub.cli`), then re-initialized the shell session so the updated PATH loaded.
* **Identity & Authentication:** Authenticated the GitHub CLI using the OAuth browser device-code flow over HTTPS, configured Git as a credential helper (`gh auth setup-git`), and set the global Git commit identity (`user.name` / `user.email`).
* **Shell Input Fault Isolation:** Traced a recurring `No such file or directory` error to an invisible C1 control character (`\302\233`) injected ahead of the `~` path shortcut during paste operations; resolved it by substituting the `$HOME` environment variable.
* **IDE Security Controls:** Cleared VS Code Workspace Trust (Restricted Mode) for the repository root to allow code execution, and installed the Microsoft Python and Python Debugger extensions to enable the interpreter and the Run and Debug workflow.
* **Version Control & CI Validation:** Created a private repository from the course template with `gh repo create --template`, then staged, committed, and pushed changes. The push triggered a GitHub Actions workflow that validated the program automatically (passing check).

### Core Competencies Demonstrated
* **Python Fundamentals:** User input, type conversion (`int()`), arithmetic, f-string output formatting, and use of the standard library.
* **Developer Environment Administration:** CLI tool installation, PATH and shell troubleshooting, and IDE configuration on Windows.
* **Git & GitHub Workflow:** Repository creation from templates, authentication, commits, pushes, and reading CI status.
