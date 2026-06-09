# Active Directory Lab no Azure

Lab prático de Active Directory no Azure com simulações de ataque e defesa.

## Estrutura

```
AD-Lab-Azure/
├── screenshots/
│   ├── fase1-azure/       # Infraestrutura Azure
│   ├── fase2-ad/          # Instalação do AD
│   ├── fase3-usuarios/    # Usuários e grupos
│   ├── fase4-cliente/     # VM cliente WKS01
│   └── fase5-testes/      # Testes e simulações
├── docs/
│   └── documentacao.md    # Documentação completa do lab
├── scripts/               # Scripts PowerShell usados
└── README.md
```

## Topologia

| Recurso | Valor |
|---|---|
| Domínio | corp.local |
| Resource Group | ad-lab-rg |
| VNet | 10.0.0.0/16 |
| DC01 (Domain Controller) | 10.0.1.4 |
| WKS01 (Workstation) | 10.0.1.5 |

## Usuários

| Usuário | Grupo | Papel |
|---|---|---|
| john.doe | Security_Team | Funcionário |
| jane.smith | Finance_Department | Funcionário |
| hacker.local | Domain Admins | Simula atacante |

## Fases

1. Infraestrutura Azure (Resource Group, VNet, VM)
2. Instalação do Active Directory
3. Criação de usuários e grupos
4. VM cliente no domínio
5. Testes de ataque e defesa

## Status

- [ ] Fase 1 — Infraestrutura Azure
- [ ] Fase 2 — Active Directory
- [ ] Fase 3 — Usuários e Grupos
- [ ] Fase 4 — VM Cliente
- [ ] Fase 5 — Testes
