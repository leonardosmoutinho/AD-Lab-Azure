# Active Directory Lab no Azure

Lab prático de Active Directory no Azure com simulações de ataque e defesa, documentado com screenshots passo a passo.

## Topologia

| Recurso | Valor |
|---|---|
| Domínio | corp.local |
| Forest | corp.local |
| Resource Group | ad-lab-rg |
| Região | East US 2 |
| VNet | DC01-vnet (10.0.0.0/16) |
| DC01 — Domain Controller | 10.0.0.4 |
| WKS01 — Workstation | 10.0.0.5 |

## Usuários do Domínio

| Usuário | Grupo | Papel |
|---|---|---|
| john.doe | Security_Team | Funcionário |
| jane.smith | Finance_Department | Funcionário |
| hacker.local | Domain Admins | Simula atacante |

---

## Fase 1 — Infraestrutura Azure

Criação do Resource Group, VNet e VM DC01 no Azure.

![Resource Group criado](screenshots/fase1-azure/01-resource-group-criado.png)

![VNet criada](screenshots/fase1-azure/02-vnet-criada.png)

![DC01 review create](screenshots/fase1-azure/03-dc01-review-create.png)

![DC01 deployment complete](screenshots/fase1-azure/04-dc01-deployment-complete.png)

---

## Fase 2 — Active Directory

Instalação do AD DS e promoção da DC01 a Domain Controller com forest `corp.local`.

![Server Manager Dashboard](screenshots/fase2-ad/01-server-manager-dashboard.png)

![AD DS instalado](screenshots/fase2-ad/02-ad-ds-instalado.png)

![AD DS instalado dashboard](screenshots/fase2-ad/03-ad-ds-instalado-dashboard.png)

![Get-ADDomain confirmado](screenshots/fase2-ad/04-get-addomain-confirmado.png)

---

## Fase 3 — Usuários e Grupos

Criação das OUs `_Employees`, `_Admins`, `_Workstations` e dos usuários/grupos do domínio.

![OUs criadas](screenshots/fase3-usuarios/01-ous-criadas.png)

![Usuários e grupos criados](screenshots/fase3-usuarios/02-usuarios-grupos-criados.png)

![Usuários confirmados PowerShell](screenshots/fase3-usuarios/03-usuarios-confirmados-powershell.png)

---

## Fase 4 — VM Cliente (WKS01)

Criação da WKS01 (Windows 11 Pro) na mesma VNet, configuração do DNS e ingresso no domínio.

![WKS01 deployment complete](screenshots/fase4-wks01/01-wks01-deployment-complete.png)

![WKS01 overview](screenshots/fase4-wks01/02-wks01-overview.png)

![DNS configurado para DC01](screenshots/fase4-wks01/03-wks01-dns-configurado.png)

![Domínio confirmado](screenshots/fase4-wks01/04-wks01-dominio-confirmado.png)

**Comandos usados:**
```powershell
# Configurar DNS apontando para DC01
Set-DnsClientServerAddress -InterfaceIndex 6 -ServerAddresses "10.0.0.4"

# Ingressar no domínio
Add-Computer -DomainName "corp.local" -Credential "CORP\azureuser" -Restart

# Confirmar domínio
(Get-WmiObject Win32_ComputerSystem).Domain
```

---

## Fase 5 — Testes de Ataque e Defesa

### Teste 1 — Enumeração de usuários do domínio

Executado `net user /domain` na WKS01, consultando a DC01 e listando todos os usuários.

![Enumeração net user /domain](screenshots/fase5-testes/01-enumeracao-net-user-domain.png)

### Teste 2 — Admin Share (acesso lateral)

Acesso ao `\\DC01\C$` com credenciais de domínio — simula movimento lateral de um atacante.

![Admin Share DC01](screenshots/fase5-testes/02-admin-share-dc01.png)

```powershell
net use \\DC01\C$ /user:CORP\azureuser Lab@Azure2024!
```

### Teste 3 — Event Viewer (monitoramento na DC01)

Logs de segurança na DC01 com **1.227 eventos de Logon (Event ID 4624)** registrados.

![Event Viewer Security Logs](screenshots/fase5-testes/03-event-viewer-security-logs.png)

![Event ID 4624 - Logon](screenshots/fase5-testes/04-event-viewer-4624-logon.png)

---

## Status

- [x] Fase 1 — Infraestrutura Azure
- [x] Fase 2 — Active Directory
- [x] Fase 3 — Usuários e Grupos
- [x] Fase 4 — VM Cliente (WKS01)
- [x] Fase 5 — Testes de Ataque e Defesa

---

## Documentação completa

Ver [docs/documentacao.md](docs/documentacao.md) para detalhes técnicos, comandos e observações.
