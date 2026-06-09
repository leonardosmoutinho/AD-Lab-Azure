# AD Lab — Documentation

## Environment

| Item | Value |
|---|---|
| Domain | corp.local |
| Forest | corp.local |
| NetBIOS | CORP |
| DC01 Private IP | 10.0.0.4 |
| DC01 Public IP | 172.206.48.135 |
| WKS01 Private IP | 10.0.0.5 |
| WKS01 Public IP | 20.96.1.185 |
| VNet | DC01-vnet (10.0.0.0/16) |
| Region | East US 2 |
| Creation date | 2026-06-08 |

---

## Phase 1 — Azure Infrastructure

- [x] Resource Group created: `ad-lab-rg` (East US 2)
- [x] VNet created: `DC01-vnet` (10.0.0.0/16, default subnet 10.0.0.0/24)
- [x] VM DC01 created (Windows Server 2022 Datacenter Gen2, Standard_D2s_v7)
- [x] Static private IP configured: 10.0.0.4
- [x] RDP connected via Windows App

📸 Screenshots: `screenshots/fase1-azure/`

---

## Phase 2 — Active Directory

- [x] AD DS role installed via Server Manager
- [x] Forest `corp.local` created
- [x] DC01 promoted to Domain Controller
- [x] Domain confirmed with `Get-ADDomain`

📸 Screenshots: `screenshots/fase2-ad/`

---

## Phase 3 — Users and Groups

- [x] OUs created: `_Employees`, `_Admins`, `_Workstations`
- [x] User `john.doe` created (member of Security_Team)
- [x] User `jane.smith` created (member of Finance_Department)
- [x] User `hacker.local` created as Domain Admin (simulates attacker)
- [x] Groups `Security_Team` and `Finance_Department` created under `_Employees`

📸 Screenshots: `screenshots/fase3-usuarios/`

---

## Phase 4 — Client VM (WKS01)

- [x] VM WKS01 created (Windows 11 Pro 25H2 Gen2, Standard_D2s_v7, East US 2)
- [x] WKS01 connected to DC01-vnet (same VNet as DC01)
- [x] DNS configured to point to DC01: `10.0.0.4`
  - Command: `Set-DnsClientServerAddress -InterfaceIndex 6 -ServerAddresses "10.0.0.4"`
- [x] WKS01 joined domain `corp.local`
  - Command: `Add-Computer -DomainName "corp.local" -Credential "CORP\azureuser" -Restart`
- [x] Domain confirmed with `(Get-WmiObject Win32_ComputerSystem).Domain` → `corp.local`

📸 Screenshots: `screenshots/fase4-wks01/`

---

## Phase 5 — Tests

### Test 1 — Domain User Enumeration
- [x] Ran `net user /domain` from WKS01
- Result: listed all users from `\\DC01.corp.local`
  - azureuser, Guest, hacker.local, jane.smith, john.doe, krbtgt

### Test 2 — Admin Share Access
- [x] Access via `net use \\DC01\C$ /user:CORP\azureuser` completed successfully
- Demonstrates lateral movement using privileged domain credentials

### Test 3 — Event Viewer (security logs on DC01)
- [x] Security log opened in DC01 Event Viewer
- [x] Filtered by Event ID 4624 (Successful Logon): **1,227 events** recorded
- Relevant events observed: 4624 (Logon), 4634 (Logoff), 4627 (Group Membership)

📸 Screenshots: `screenshots/fase5-testes/`

---

## Issues and Observations

- **East US quota blocked for VMs on Free Trial**: East US 2 was used instead
- **B-series unavailable on Free Trial**: Standard_D2s_v7 used (2 vCPUs, 8GB RAM)
- **DNS via alias failed on WKS01**: `Set-DnsClientServerAddress` with `-InterfaceAlias` returned an error; fix was to use `-InterfaceIndex 6`
- **DC01 must be running** for WKS01 to join the domain (FailToJoinDomainFromWorkgroup error if DC is offline)
- **DC01 public IP changes** when the VM is restarted — always check the portal before connecting via RDP
- **⚠️ Reminder**: delete `ad-lab-rg` in Azure when finished to avoid charges
