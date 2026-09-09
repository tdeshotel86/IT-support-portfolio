# SOP-003: Suspected Endpoint Compromise & Triage Isolation

## 1. Purpose & Scope
Defines immediate operational containment procedures for workstations exhibiting indicators of compromise (IoCs), including suspected ransomware execution, unauthorized remote access tools (RATs), active command-and-control (C2) beaconing, or malicious payload execution. Applies to all Tier I/II Service Desk and desktop support technicians.

---

## 2. Critical Directive: Volatile Memory Preservation
> **DO NOT REBOOT OR POWER DOWN THE HOST WORKSTATION.**  
> Powering down destroys volatile memory (RAM), eliminating active process trees, injected memory payloads, cached credentials, and open network socket states essential for root-cause forensic analysis.

---

## 3. Step-by-Step Execution Workflow

### Step 1: Immediate Network Containment
* **Physical Isolation:** Instruct the end user to disconnect the physical Ethernet cable immediately.
* **Wireless Isolation:** Direct the user to turn off Wi-Fi via the hardware toggle or Windows Action Center.
* **EDR Isolation (Preferred):** If using an enterprise Endpoint Detection and Response platform (e.g., Defender for Endpoint, CrowdStrike, SentinelOne), issue an administrative **Network Isolation** command from the security console. This severs local network traffic while maintaining cloud management telemetry.

### Step 2: Capture Volatile Network Connections
From an administrative command line or PowerShell session, export current socket states:

```powershell
netstat -ano | findstr /i "ESTABLISHED" > C:\Temp\established_sockets.txt
```

Look for anomalous high-order outbound ports, foreign IP ranges, or uncommon processes bound to port 443/80.

### Step 3: Inspect Active Process Trees
Identify resource-intensive, unverified, or anomalous executable paths:

```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 20 -Property Id, ProcessName, Path, CPU
```

Flag any binaries running out of temporary locations (e.g., `C:\Users\<user>\AppData\Local\Temp\` or `C:\ProgramData\`).

### Step 4: Audit Startup & Persistence Mechanisms
Check registry persistence keys and anomalous scheduled tasks:

```powershell
Get-CimInstance Win32_StartupCommand | Select-Object Name, Command, Location, User
Get-ScheduledTask | Where-Object {$_.State -ne "Disabled" -and $_.TaskPath -notlike "\Microsoft*"}
```

### Step 5: Identity Revocation
Access **Active Directory / Microsoft Entra ID** immediately:
1. Reset the compromised user's domain password.
2. Invalidate all active user sessions and refresh tokens across all enrolled devices.
3. Place a temporary block on the account pending forensic clearance.

### Step 6: Escalation & Forensic Handover
* Create a **Severity-1 / Critical Security Incident** ticket routed directly to the SOC / Security Operations queue.
* Include:
  * Machine Hostname, MAC address, and internal IP
  * Exported socket and process capture files
  * Suspect email headers or downloaded file samples (quarantined in password-protected ZIP)
* Maintain chain of custody on the physical workstation until re-imaging approval is granted.
