# SOP-002: Remote End-User VPN Diagnostics & Tunnel Remediation

## 1. Purpose & Scope
Provides a structured troubleshooting workflow for remote personnel experiencing VPN tunnel establishment failures, handshake timeouts, dropped connections, or lack of routing to internal corporate subnets. Applies to Tier I/II Service Desk technicians.

---

## 2. Diagnostic Matrix

| Failure Symptom | Probable Root Cause | Target Remediation Action |
| :--- | :--- | :--- |
| **Handshake Timeout / Error 800** | Local ISP/router blocking IPsec/SSL ports | Switch protocol profile to SSL/TLS; verify home router IPsec passthrough. |
| **Authentication Rejected** | Stale AD credentials or unsynced MFA | Clear cached credentials in Credential Manager; re-sync system clock. |
| **Connected, No Internal Access** | Subnet IP collision or DNS suffix failure | Inspect local routing table; append internal DNS search suffixes. |
| **High Latency / Jitter** | Wi-Fi packet drops or MTU packet blackholing | Transition from Wi-Fi to Ethernet; adjust virtual adapter MTU to 1350. |

---

## 3. Step-by-Step Execution Workflow

### Step 1: Baseline Host Connectivity
Have the user open PowerShell and verify baseline connectivity to external DNS servers:
```powershell
Test-Connection -ComputerName 8.8.8.8 -Count 4

If packet loss is greater than 0%, instruct the user to power-cycle their local ISP gateway/router and connect via a physical Ethernet cable before troubleshooting client software.

Step 2: Synchronize System Clock (Clock Drift Remediation)
Kerberos authentication and SAML/MFA session tokens automatically fail if client-side system clock drift exceeds 5 minutes relative to domain controllers. Force a clock resynchronization:

PowerShell
w32tm /resync /force
Step 3: Flush DNS & Reset TCP/IP Sockets
Purge stale name resolution caches and reset damaged network socket layers:

PowerShell
ipconfig /flushdns
netsh int ip reset
netsh winsock reset
(Note: Instruct the user that a system restart is recommended after completing a Winsock reset).

Step 4: Resolve Local & Corporate Subnet Overlaps
Run route print to determine if the user's home network subnet (such as 192.168.1.0/24 or 192.168.0.0/24) overlaps with the internal corporate subnet.

If overlapping, force corporate DNS suffixes on the virtual adapter or configure split-tunnel exclusion rules.

Step 5: Virtual Adapter Driver Reset
If the client software indicates the virtual network adapter failed to initialize:

Open Device Manager (devmgmt.msc).

Expand the Network adapters section.

Right-click the virtual VPN adapter (e.g., Cisco AnyConnect Virtual Adapter or Palo Alto GlobalProtect Adapter) and select Uninstall device.

Select Action from the top menu > click Scan for hardware changes to force the operating system to reinstall the driver.

Step 6: Validate Internal Network Access
Verify full end-to-end tunnel communication by testing an essential internal service port (SMB/RPC):

PowerShell
Test-NetConnection -ComputerName int-dc01.corp.local -Port 445
Confirm the user can successfully access mapped drives, intranet web applications, and domain controllers.

Step 7: Documentation & Ticket Closure
Log the user's ISP details, client software version, and specific resolution steps (e.g., driver reinstallation, winsock reset, DNS flush) in the internal ticketing notes.

Update ticket status to Resolved and confirm stability with the end user.
