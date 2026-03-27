# Plano Técnico: [Nome da Feature]

## Visão Geral

<!-- Resumo de como a feature será implementada tecnicamente -->

## Arquitetura

### Diagrama de Camadas

```
Presentation (LCL / Web Form)
    │
    ▼
Application (Service)
    │
    ▼
Domain (Entity + Interface)
    ▲
    │
Infrastructure (Repository SQLdb/Zeos)
```

### Sequência de Operação

```
[Form] → chama → [Service.Create()] → valida → [Repository.Insert()] → [DB]
```

## Componentes a Criar

### Domain Layer

| Arquivo | Tipo | Descrição |
|---------|------|-----------|
| `*.Domain.[X].Entity.pas` | Entidade | Classe com propriedades e validações de domínio |
| `*.Domain.[X].Repository.Intf.pas` | Interface | Contrato abstrato de acesso a dados |

### Application Layer

| Arquivo | Tipo | Descrição |
|---------|------|-----------|
| `*.Application.[X].Service.Intf.pas` | Interface | Contrato do service |
| `*.Application.[X].Service.pas` | Service | Lógica de negócio independente com constructor injection |

### Infrastructure Layer

| Arquivo | Tipo | Descrição |
|---------|------|-----------|
| `*.Infra.[X].Repository.pas` | Repository | Implementação SQLdb / Zeos do repository abstrato |
| `*.Infra.Factory.pas` | Factory | Factory method para construir service e repassar a dependência (repository) |

### Presentation Layer

| Arquivo | Tipo | Descrição |
|---------|------|-----------|
| `*.Presentation.[X].List.pas` | Form LCL | Tela de listagem/pesquisa nativa |
| `*.Presentation.[X].Edit.pas` | Form LCL | Tela de inclusão/edição de dados |

## Dependências entre Componentes

```
[Edit Form] → IService → IRepository → TSQLConnection / TZConnection
```

## Migração de Banco de Dados

```sql
-- Migration: YYYY-MM-DD_create_[tabela]
CREATE TABLE [tabela] (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  -- campos
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME
);
```

## Riscos e Considerações

- [Risco 1 e como mitigar]
- [Risco 2 e como mitigar]

## Checklist de Conformidade

- [ ] Segue SOLID (SRP, OCP, LSP, ISP, DIP)
- [ ] Clean code (métodos ≤ 20 linhas, nomes descritivos)
- [ ] Convenções de Pascal para FPC (prefixos T, I, E, F, A, L)
- [ ] Documentação de APIs públicas
- [ ] Try/finally imediato para todo objeto temporário alocado
- [ ] Guard clauses em vez de loops de indentação pesada
- [ ] Sem `with`, sem declarações visuais globais não controladas
