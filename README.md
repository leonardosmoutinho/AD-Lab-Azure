# Active Directory Lab on Azure

Hands-on Active Directory lab built on Azure, with attack and defense simulations, documented step by step with screenshots.

## Topology

| Resource | Value |
|---|---|
| Domain | corp.local |
| Forest | corp.local |
| Resource Group | ad-lab-rg |
| Region | East US 2 |
| VNet | DC01-vnet (10.0.0.0/16) |
| DC01 — Domain Controller | 10.0.0.4 |
| WKS01 — Workstation | 10.0.0.5 |

## Domain Users

| Username | Group | Role |
|---|---|---|
| john.doe | Security_Team | Employee |
| jane.smith | Finance_Department | Employee |
| hacker.local | Domain Admins | Simulates attacker |

---

## Phase 1 — Azure Infrastructure

Created the Resource Group, VNet, and DC01 VM on Azure.

![Resource Group created](screenshots/fase1-azure/01-resource-group-criado.png)

![VNet created](screenshots/fase1-azure/02-vnet-criada.png)

![DC01 review create](screenshots/fase1-azure/03-dc01-review-create.png)

![DC01 deployment complete](screenshots/fase1-azure/04-dc01-deployment-complete.png)

---

## Phase 2 — Active Directory

Installed AD DS role and promoted DC01 to Domain Controller with forest `corp.local`.

![Server Manager Dashboard](screenshots/fase2-ad/01-server-manager-dashboard.png)

![AD DS installed](screenshots/fase2-ad/02-ad-ds-instalado.png)

![AD DS installed dashboard](screenshots/fase2-ad/03-ad-ds-instalado-dashboard.png)

![Get-ADDomain confirmed](screenshots/fase2-ad/04-get-addomain-confirmado.png)

---

## Phase 3 — Users and Groups

Created OUs `_Employees`, `_Admins`, `_Workstations` and domain users/groups.

![OUs created](screenshots/fase3-usuarios/01-ous-criadas.png)

![Users and groups created](screenshots/fase3-usuarios/02-usuarios-grupos-criados.png)

![Users confirmed via PowerShell](screenshots/fase3-usuarios/03-usuarios-confirmados-powershell.png)

---

## Phase 4 — Client VM (WKS01)

Created WKS01 (Windows 11 Pro) on the same VNet, configured DNS, and joined the domain.

![WKS01 deployment complete](screenshots/fase4-wks01/01-wks01-deployment-complete.png)

![WKS01 overview](screenshots/fase4-wks01/02-wks01-overview.png)

![DNS configured to point to DC01](screenshots/fase4-wks01/03-wks01-dns-configurado.png)

![Domain join confirmed](screenshots/fase4-wks01/04-wks01-dominio-confirmado.png)

**Commands used:**
```powershell
# Set DNS to point to DC01
Set-DnsClientServerAddress -InterfaceIndex 6 -ServerAddresses "10.0.0.4"

# Join the domain
Add-Computer -DomainName "corp.local" -Credential "CORP\azureuser" -Restart

# Confirm domain membership
(Get-WmiObject Win32_ComputerSystem).Domain
```

---

## Phase 5 — Attack and Defense Tests

### Test 1 — Domain User Enumeration

Ran `net user /domain` from WKS01, querying DC01 and listing all domain users.

![net user /domain enumeration](screenshots/fase5-testes/01-enumeracao-net-user-domain.png)

### Test 2 — Admin Share (lateral movement)

Accessed `\\DC01\C$` using domain admin credentials — simulates lateral movement by an attacker.

![Admin Share DC01](screenshots/fase5-testes/02-admin-share-dc01.png)

```powershell
net use \\DC01\C$ /user:CORP\azureuser Lab@Azure2024!
```

### Test 3 — Event Viewer (monitoring on DC01)

Reviewed security logs on DC01. Filtered by **Event ID 4624 (Successful Logon)**: **1,227 events** recorded.

![Event Viewer Security Logs](screenshots/fase5-testes/03-event-viewer-security-logs.png)

![Event ID 4624 - Logon](screenshots/fase5-testes/04-event-viewer-4624-logon.png)

---

## Status

- [x] Phase 1 — Azure Infrastructure
- [x] Phase 2 — Active Directory
- [x] Phase 3 — Users and Groups
- [x] Phase 4 — Client VM (WKS01)
- [x] Phase 5 — Attack and Defense Tests

---

## Full Documentation

See [docs/documentation.md](docs/documentation.md) for technical details, commands, and observations.
