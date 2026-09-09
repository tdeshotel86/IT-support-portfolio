# SOP-004: Windows Print Spooler Crash Recovery & Driver Remediation

## 1. Purpose & Scope
Provides standard operating procedures for Tier I/II IT support specialists resolving persistent print queue deadlocks, Print Spooler service termination (Error 1068 / RPC Server Unavailable), and corrupted vendor print drivers across local workstations and mapped network shared printers.

---

## 2. Common Error Indicators
* Print queue indicates *"Document - Deleting"* but remains stuck indefinitely.
* Event Viewer logs **Event ID 7031** or **Event ID 7034**: *The Print Spooler service terminated unexpectedly.*
* Applications freeze, hang, or crash when invoking the native Windows print dialog.

---

## 3. Step-by-Step Execution Workflow

### Step 1: Halt Print Spooler Service
Launch an elevated administrative PowerShell prompt and force-stop the spooler daemon:

```powershell
Stop-Service -Name Spooler -Force
```

### Step 2: Purge Stuck Spool Cache Files
Corrupt `.SPL` (print data) and `.SHD` (shadow header) files locked in the queue cache prevent the service from initializing cleanly. Purge all cached spool files:

```powershell
Remove-Item -Path "$env:SystemRoot\System32\spool\PRINTERS\*" -Force -Recurse
```

### Step 3: Restart & Validate Service State
Configure the Print Spooler service to launch automatically and restart the daemon:

```powershell
Set-Service -Name Spooler -StartupType Automatic
Start-Service -Name Spooler
Get-Service -Name Spooler | Select-Object Name, Status, StartType
```

### Step 4: Isolate & Remove Corrupted Driver Packages
If the Spooler crashes immediately upon receiving a new print command, the vendor driver package is corrupted:
1. Open the **Run** dialog (`Win + R`), type `printui.exe /s /t2`, and press **Enter**.
2. Under the **Drivers** tab, locate and select the target printer driver.
3. Click **Remove**.
4. Select **Remove driver and driver package**, then click **OK**.
*(Note: If prompted that the driver is currently in use, stop the Spooler service, remove the package, and restart the service).*

### Step 5: Clean Enterprise Driver Installation
* Do not rely on generic Windows Update drivers for production multi-function printers (MFPs).
* Download the certified **PCL6 Universal Print Driver** or vendor-specific driver package directly from the manufacturer's official support portal (HP, Brother, Ricoh, Canon).
* Execute the installer using local administrator privileges.

### Step 6: Test Page Verification
Issue a test print command directly via CIM to verify end-to-end driver-to-hardware communication:

```powershell
$DefaultPrinter = Get-CimInstance Win32_Printer -Filter "Default=True"
Invoke-CimMethod -InputObject $DefaultPrinter -MethodName PrintTestPage
```

### Step 7: Ticket Resolution & Documentation
* Confirm physical page output and quality with the end user.
* Document the removed driver version, replacement driver installed, and queue remediation steps in the ticket notes.
* Update ticket status to **Resolved**.
