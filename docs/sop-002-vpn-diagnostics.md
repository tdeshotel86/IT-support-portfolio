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
