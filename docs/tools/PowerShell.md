# PowerShell

### **Basic Syntax**

- General form:
    
    ```powershell
    Cmdlet -Property "pattern*"
    ```
    
- Example:
    
    ```powershell
    Get-ChildItem -Path "C:\Users"  # Lists files in the directory
    ```
    

---

### **Essential Cmdlets**

|Cmdlet|Purpose|Example|
|---|---|---|
|`Get-Command`|Finds available cmdlets|`Get-Command -CommandType Cmdlet`|
|`Get-Help`|Displays cmdlet documentation|`Get-Help Get-Item -Full`|
|`cd`, `Set-Location`|Change directory|`Set-Location -Path C:\Users`|
|`dir`, `Get-ChildItem`|List contents of a directory|`Get-ChildItem -Path .`|
|`New-Item`|Create file/folder|`New-Item -Path . -Name "test.txt" -ItemType "File"`|
|`Remove-Item`|Delete file/folder|`Remove-Item -Path "test.txt"`|
|`Copy-Item`|Copy file/folder|`Copy-Item -Path "test.txt" -Destination "backup.txt"`|
|`Move-Item`|Move/rename file|`Move-Item -Path "old.txt" -Destination "new.txt"`|
|`Get-Content`|Read file content|`Get-Content -Path "test.txt"`|

---

### **Pipelining**

- Chain multiple commands using `|` (pipe)
    
- Example:
    
    ```powershell
    Get-ChildItem | Sort-Object Length
    ```
    

---

### **Filtering and Sorting**

##### `Where-Object`

- Filters based on property:
    
    ```powershell
    Get-ChildItem | Where-Object -Property "Extension" -eq ".txt"
    ```
    

### Comparison Operators

|Operator|Meaning|
|---|---|
|`-eq`|Equal to|
|`-ne`|Not equal to|
|`-gt`|Greater than|
|`-ge`|Greater than or equal to|
|`-lt`|Less than|
|`-le`|Less than or equal to|
|`-like`|Matches pattern (wildcards allowed)|

- Example:
    
    ```powershell
    Get-ChildItem | Where-Object -Property "Name" -like "ship*"
    ```
    

##### `Sort-Object` & `Select-Object`

- Sort by property:
    
    ```powershell
    Get-ChildItem | Sort-Object Length
    ```
    
- Select specific properties:
    
    ```powershell
    Get-Process | Select-Object Name, Id
    ```
    

---

### **Search Inside Files**

```powershell
Select-String -Path "*.log" -Pattern "error"
```

---

### **System Information Cmdlets**

|Cmdlet|Description|
|---|---|
|`Get-ComputerInfo`|System details|
|`Get-LocalUser`|List local users|
|`Get-NetIPConfiguration`|IP & adapter info|
|`Get-NetIPAddress`|Shows IP address|

---

### **Process, Services, and Networking**

|Cmdlet|Use|
|---|---|
|`Get-Process`|List running processes|
|`Get-Service`|List Windows services|
|`Get-NetTCPConnection`|List active TCP connections|
|`Get-FileHash`|Generate file hash (SHA256 default)|

---

### **Modules Management**

|Cmdlet|Description|
|---|---|
|`Find-Module`|Search online modules|
|`Install-Module`|Install from PowerShell Gallery|

---

### **PowerShell for Cybersecurity Roles**

#### Blue Team

- Automate:
    
    - IOC extraction
        
    - Log parsing
        
    - Reverse engineering malware
        
    - System health checks
        

#### Red Team

- Tasks:
    
    - Enumeration scripts
        
    - Remote command execution
        
    - Obfuscation for evasion
        

#### Sysadmins

- Use PowerShell for:
    
    - Configuration enforcement
        
    - Auto-response to alerts
        
    - Secure remote management
        

---

### **Scripting in PowerShell**

- Scripts = `.ps1` files with sequences of commands
    
- Example `myscript.ps1`:
    
    ```powershell
    $user = Get-LocalUser
    $ip = Get-NetIPAddress
    Write-Output "User Info: $user"
    Write-Output "IP Info: $ip"
    ```
    

---

### **Invoke-Command** (Remote Execution)

#### Example 1: Run Script Remotely

```powershell
Invoke-Command -FilePath "C:\scripts\test.ps1" -ComputerName Server01
```

#### Example 2: Run Inline Command Remotely

```powershell
Invoke-Command -ComputerName Server01 -Credential Domain01\User01 -ScriptBlock { Get-Culture }
```

- Runs `Get-Culture` on `Server01` with provided credentials.
    

---

### **Alias Reference**

|Alias|Cmdlet|
|---|---|
|`ls`, `dir`, `gci`|`Get-ChildItem`|
|`cd`|`Set-Location`|
|`rm`|`Remove-Item`|
|`mv`|`Move-Item`|
|`cp`|`Copy-Item`|

Use `Get-Alias` to view all:

```powershell
Get-Alias
```

---