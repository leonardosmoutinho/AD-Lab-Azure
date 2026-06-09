# AD Lab — Documentação

## Ambiente

| Item | Valor |
|---|---|
| Domínio | corp.local |
| Forest | corp.local |
| NetBIOS | CORP |
| DC01 IP Privado | 10.0.0.4 |
| DC01 IP Público | 172.206.48.135 |
| WKS01 IP Privado | 10.0.0.5 |
| WKS01 IP Público | 20.96.1.185 |
| VNet | DC01-vnet (10.0.0.0/16) |
| Região | East US 2 |
| Data de criação | 2026-06-08 |

---

## Fase 1 — Infraestrutura Azure

- [x] Resource Group criado: `ad-lab-rg` (East US 2)
- [x] VNet criada: `DC01-vnet` (10.0.0.0/16, subnet default 10.0.0.0/24)
- [x] VM DC01 criada (Windows Server 2022 Datacenter Gen2, Standard_D2s_v7)
- [x] IP privado estático configurado: 10.0.0.4
- [x] RDP conectado via Windows App

📸 Screenshots: `screenshots/fase1-azure/`

---

## Fase 2 — Active Directory

- [x] Role AD DS instalada via Server Manager
- [x] Forest `corp.local` criada
- [x] DC01 promovido a Domain Controller
- [x] Domínio confirmado com `Get-ADDomain`

📸 Screenshots: `screenshots/fase2-ad/`

---

## Fase 3 — Usuários e Grupos

- [x] OUs criadas: `_Employees`, `_Admins`, `_Workstations`
- [x] Usuário `john.doe` criado (membro de Security_Team)
- [x] Usuário `jane.smith` criado (membro de Finance_Department)
- [x] Usuário `hacker.local` criado como Domain Admin (simula atacante)
- [x] Grupos `Security_Team` e `Finance_Department` criados em `_Employees`

📸 Screenshots: `screenshots/fase3-usuarios/`

---

## Fase 4 — VM Cliente (WKS01)

- [x] VM WKS01 criada (Windows 11 Pro 25H2 Gen2, Standard_D2s_v7, East US 2)
- [x] WKS01 conectada à DC01-vnet (mesma VNet da DC01)
- [x] DNS configurado para apontar para DC01: `10.0.0.4`
  - Comando: `Set-DnsClientServerAddress -InterfaceIndex 6 -ServerAddresses "10.0.0.4"`
- [x] WKS01 ingressada no domínio `corp.local`
  - Comando: `Add-Computer -DomainName "corp.local" -Credential "CORP\azureuser" -Restart`
- [x] Domínio confirmado com `(Get-WmiObject Win32_ComputerSystem).Domain` → `corp.local`

📸 Screenshots: `screenshots/fase4-wks01/`

---

## Fase 5 — Testes

### Teste 1 — Enumeração de usuários do domínio
- [x] Executado `net user /domain` na WKS01
- Resultado: listou todos os usuários de `\\DC01.corp.local`
  - azureuser, Guest, hacker.local, jane.smith, john.doe, krbtgt

### Teste 2 — Acesso a Admin Share
- [x] Acesso via `net use \\DC01\C$ /user:CORP\azureuser` confirmado com sucesso
- Demonstra acesso lateral com credenciais de domínio privilegiadas

### Teste 3 — Event Viewer (logs de segurança na DC01)
- [x] Security log aberto no Event Viewer da DC01
- [x] Filtrado por Event ID 4624 (Logon bem-sucedido): **1.227 eventos** registrados
- Eventos relevantes observados: 4624 (Logon), 4634 (Logoff), 4627 (Group Membership)

📸 Screenshots: `screenshots/fase5-testes/`

---

## Observações e Problemas Encontrados

- **East US bloqueado para VMs no Free Trial**: foi necessário usar East US 2
- **B-series indisponível no Free Trial**: usado Standard_D2s_v7 (2 vCPUs, 8GB RAM)
- **DNS via alias falhou na WKS01**: `Set-DnsClientServerAddress` com `-InterfaceAlias` retornou erro; solução foi usar `-InterfaceIndex 6`
- **DC01 precisa estar ligada** para WKS01 ingressar no domínio (erro FailToJoinDomainFromWorkgroup se DC estiver offline)
- **IP público da DC01 muda** ao religar a VM — sempre verificar no portal antes de conectar via RDP
- **⚠️ Lembrete**: deletar `ad-lab-rg` no Azure ao terminar para evitar custos
