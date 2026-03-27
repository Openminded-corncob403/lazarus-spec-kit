# Estrutura e Convenções — FreePascal/Lazarus

## Arquitetura em Camadas

```
src/
├── Domain/           ← Entidades, Value Objects, Interfaces de repositório
├── Application/      ← Services, Use Cases, DTOs
├── Infrastructure/   ← Implementações concretas, componentes SQLdb, conexões Zeos
└── Presentation/     ← Forms nativos LCL e IntraWeb `TIWAppForm`, ViewModels
```

## Regra de Dependência

```
Presentation → Application → Domain ← Infrastructure
```

- **Domain** nunca conhece a infraestrutura local (exemplo, SQLdb). Possui interfaces declarativas.
- **Application** orquestra fluxos e depende primariamente do Domínio.
- **Infrastructure** provê as injeções concretas requeridas nos contratos estritos.
- **Presentation** aciona unicamente fluxos da Application layer (nunca disparando queries ou comandos DB diretos através dos Form events dos botões LCL `OnClick`).

## Nomenclatura de Units

### Padrão
```
{Projeto}.{Camada}.{Domínio}.{Funcionalidade}.pas
```

*(Exemplo: `MeuApp.Domain.Customer.Entity.pas`, `MeuApp.Presentation.Customer.Edit.pas`)*

## Nomenclatura Padrão de Componentes LCL em Forms

| Componente LCL | Padrão (Prefixo) |
|---|---|
| TButton | btn |
| TEdit | edt |
| TLabel | lbl |
| TComboBox | cmb |
| TPanel | pnl |
| TStringGrid | grd |
| TSQLQuery / TZQuery | qry |
| TSQLConnection | con |

## Seções FPC da Unit

O escopo do `interface` e do `implementation` deve ser usado cirurgicamente, evitando criar funções vazadas globais ou Units poluídas no contexto de `uses`. Toda referência utilitária que possa ser carregada diretamente nas classes locais da implementação, que fique nas uses exclusivas do `implementation`.
