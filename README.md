# 🚀 FreePascal Lazarus AI Spec-Kit

<div align="center">

**Um ecossistema opinativo de regras, *skills* e *steerings* para elevar o desenvolvimento FreePascal e Lazarus ao patamar state-of-the-art com Inteligência Artificial.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Lazarus](https://img.shields.io/badge/Lazarus-Object%20Pascal-red?logo=lazarus)](https://www.lazarus-ide.org/)
[![GitHub Copilot](https://img.shields.io/badge/GitHub%20Copilot-Ready-blue?logo=github)](https://github.com/features/copilot)
[![Cursor](https://img.shields.io/badge/Cursor-Rules-purple)](https://cursor.sh)
[![Claude](https://img.shields.io/badge/Claude-Skills-black)](https://claude.ai)
[![Gemini](https://img.shields.io/badge/Gemini-Skills-orange?logo=google)](https://gemini.google.com)
[![Kiro](https://img.shields.io/badge/Kiro-Steering-teal)](https://kiro.dev)

</div>

## Patrocínio

**Componentes Delphi/Lazarus**
<www.inovefast.com.br>

Integrações com plataformas de pagamento e serviços (Asaas, MercadoPago, Cielo, PagSeguro, D4sign, Webstore, MelhorEnvio, Groq )

**i9DBTools**
<www.inovefast.com.br/i9dbTools/>
Gerencie MySQL, PostgreSQL, Firebird e SQLite em um só lugar, com IA para gerar e explicar SQL em linguagem natural, otimizar queries e criar dados fake brasileiros em segundos.

## 📋 Índice

- [O que é este projeto?](#-o-que-é-este-projeto)
- [Por que usar?](#-por-que-usar)
- [Ferramentas de IA Suportadas](#-ferramentas-de-ia-suportadas)
- [Política de Ignore (IA)](#-política-de-ignore-ia)
- [Principais Diretrizes](#-principais-diretrizes-ensinadas-à-ia)
- [Frameworks Suportados](#️-frameworks-e-bibliotecas-suportados)
- [Estrutura do Kit](#-estrutura-do-kit)
- [Quick Start](#-quick-start)
- [Exemplos de Código](#-exemplos-de-boas-práticas)
- [Contribuições](#-contribuições)

---

## 💡 O que é este projeto?

O **FreePascal Lazarus AI Spec-Kit** não é um framework de código — é um conjunto de **diretrizes de comportamento** para sua IA favorita. Ele "ensina" o assistente a escrever código FreePascal:

- ✅ **Limpo** — sem *god classes*, sem lógica de negócio em `OnClick`
- ✅ **Seguro** — zero *memory leaks* com formatação explícita em blocos `try..finally` e ARC nativo via Interfaces
- ✅ **Testável** — TDD com FPCUnit / DUnit, Fakes via interface, sem banco real nos testes
- ✅ **Arquitetado** — SOLID, DDD, Repository/Service Pattern e *clean architecture*

> Diga adeus à IA que mistura acesso a banco com a camada de apresentação, esquece o `try..finally` ou ignora Injeção de Dependência.

---

## 🤔 Por que usar?

| Sem o Spec-Kit | Com o Spec-Kit |
|---|---|
| IA gera código com lógica no `OnClick` | IA isola camadas corretamente |
| `TStringList.Create` sem `try..finally` | Padrão ouro de memória aplicado sempre |
| Testes acoplados ao banco real | Fakes via interface, testes rápidos e isolados |
| Nomenclatura inconsistente | `A`-params, `F`-fields, `T`-types, verbos nos métodos |
| `with` statement e variáveis globais | Code smells bloqueados proativamente |

---

## 🤖 Ferramentas de IA Suportadas

| Ferramenta | Arquivo de Configuração | Como Funciona |
|---|---|---|
| **GitHub Copilot** | `.github/copilot-instructions.md` | Pre-prompt injetado no Workspace/Chat |
| **Cursor** | `.cursor/rules/*.md` | Rules carregadas por contexto |
| **Claude** | `.claude/skills/*/SKILL.md` | Skills modulares, prontas para empacotar e subir no Claude |
| **Google Gemini / Antigravity** | `.gemini/skills/*/SKILL.md` | Skills modulares por domínio |
| **Kiro AI** | `.kiro/steering/*.md` | Restrições de stack e arquitetura |
| **Qualquer IA** | `AGENTS.md` | Regras universais (raiz do projeto) |

> **Padrão adotado para skills:** provedores baseados em skills usam a mesma convenção de diretório no projeto: `.provider/skills/<skill-slug>/SKILL.md`. No caso do Claude, cada pasta em `.claude/skills/` pode ser empacotada individualmente para upload.

---

## 🧹 Política de Ignore (IA)

O projeto aplica uma política explícita de redução de contexto para melhorar segurança, performance e precisão das respostas dos agentes.

Escopo atual excluído do contexto padrão de IA:

- `doc/`
- `examples/`

Configurações usadas no repositório:

- `.gitignore` (base comum)
- `.cursorignore` e `.cursorindexingignore` (Cursor)
- `.geminiignore` (Gemini CLI)
- `.claude/settings.json` com `permissions.deny` (Claude Code)
- `.vscode/settings.json` com `files.exclude` e `search.exclude` (VS Code/Copilot Chat)

Documentação completa e rationale:

- `doc/tutorial-usando-ia-com-spec-kit.md` (seção: **1.1 Política de Ignore (Segurança e Performance)**)

---

## 🌟 Principais Diretrizes Ensinadas à IA

### 🧠 Memory Management Zero-Leak

A IA obriga o padrão: todo `.Create` sem *Owner* exige `try..finally` na linha **imediatamente** subsequente. Também ensina o uso de **Interfaces** (ARC) para *Garbage Collection* nativa — sem `Free` manual.

```pascal
// ✅ Padrão Ouro — gerado SEMPRE pela IA com o Spec-Kit
var LList: TStringList;
begin
  LList := TStringList.Create;
  try
    LList.Add('item');
  finally
    LList.Free;
  end;
end;
```

### 🧪 TDD com FPCUnit

Fluxo *Red-Green-Refactor* com Fakes isolados por interface. Sem acoplamento ao banco de dados nos testes.

```pascal
[Test]
procedure ProcessOrder_WithoutStock_RaisesException;
begin
  AssertException(
    EInvalidOrderException,
    procedure begin FSut.Process(FEmptyOrder); end
  );
end;
```

### 🏛️ SOLID e DDD

- **S** — Uma classe, uma responsabilidade. `TCustomerValidator` não salva no banco.
- **O** — Extensão via interfaces, sem modificar código existente.
- **L** — Herança só com contrato claro. Interfaces preferidas.
- **I** — Interfaces pequenas e específicas. Evite interfaces gigantes.
- **D** — Injeção de dependência no construtor, nunca instâncias concretas hardcoded.

```pascal
// ✅ DIP na prática
constructor TOrderService.Create(
  ARepo: IOrderRepository;
  ANotifier: INotificationService);
begin
  inherited Create;
  FRepo := ARepo;
  FNotifier := ANotifier;
end;
```

### 📖 Clean Code — Pascal Guide

Nomenclaturas consistentes e obrigatórias:

| Categoria | Convenção | Exemplo |
|---|---|---|
| Parâmetros | Prefixo `A` | `ACustomerName` |
| Campos privados | Prefixo `F` | `FCustomerName` |
| Variáveis locais | Prefixo `L` | `LCustomer` |
| Classes | Prefixo `T` | `TCustomerService` |
| Interfaces | Prefixo `I` | `ICustomerRepository` |
| Exceções | Prefixo `E` | `ECustomerNotFound` |

---

## 🛠️ Frameworks e Bibliotecas Suportados

| Framework | Domínio | Regras Incluídas |
|---|---|---|
| **IntraWeb** | Web Stateful | TIWAppForm, UserSession isolation, Memory Management |
| **Horse** | REST APIs Minimalistas | Estrutura Controller/Service/Repository, middleware |
| **ACBr** | Automação Comercial (NFe, CF-e, Boleto) | Isolamento fiscal, sem cruzar com UI (LCL) |
| **Firebird Database** | Banco de Dados Corporativo | Conexão SQLdb/Zeos, PSQL, generators, transactions |
| **PostgreSQL Database** | Banco de Dados Moderno | Conexão SQLdb/Zeos, UPSERT, JSONB, Full-Text Search, PL/pgSQL |
| **MySQL / MariaDB** | Banco de Dados Popular | Conexão SQLdb/Zeos, AUTO_INCREMENT, UPSERT, JSON, FULLTEXT |
| **FPCUnit / DUnit** | Testes Unitários | Red-Green-Refactor, Fakes via interface |
| **Design Patterns GoF** | Padrões de Projeto | Creational, Structural e Behavioral com interfaces e ARC |
| **Threading** | Multi-Threading | TThread, Thread-safety LCL |
| **Refatoração de Código** | Code Smells e Técnicas | Extract Method/Class, Guard Clauses, Strategy, Parameter Object |

---

## 📂 Estrutura do Kit

```
lazarus-spec-kit/
│
├── AGENTS.md                        # 🌐 Regras universais (Copilot, Claude, Gemini, Kiro)
│
├── .github/
│   └── copilot-instructions.md      # 🤖 Pre-prompt para GitHub Copilot
│
├── .cursor/
│   └── rules/
│       ├── freepascal-conventions.md# Nomenclatura e convenções
│       ├── memory-exceptions.md     # Padrões de memória e exceções
│       ├── tdd-patterns.md          # TDD e FPCUnit
│       ├── solid-patterns.md        # SOLID e DDD
│       ├── design-patterns.md       # ✨ Design Patterns GoF (Creational, Structural, Behavioral)
│       ├── refactoring.md           # ✨ Refatoração de código
│       ├── intraweb-patterns.md     # IntraWeb State e UI
│       ├── horse-patterns.md        # Horse REST Framework
│       ├── acbr-patterns.md         # Automação Comercial (ACBr)
│       ├── firebird-patterns.md     # ✨ Firebird Database
│       ├── postgresql-patterns.md   # ✨ PostgreSQL Database
│       ├── mysql-patterns.md        # ✨ MySQL/MariaDB
│       └── threading-patterns.md    # ✨ Threading (TThread)
│
├── .claude/
│   ├── README.md                    # Skills do Claude e convenção de empacotamento
│   └── skills/
│       ├── clean-code/              # Clean Code e Pascal Guide
│       ├── lazarus-memory-exceptions/# Memory management e try..finally
│       ├── lazarus-patterns/        # Repository, Service, Factory
│       ├── design-patterns/         # ✨ Design Patterns GoF (23 padrões)
│       ├── refactoring/             # ✨ Refatoração (10 técnicas)
│       ├── intraweb-framework/      # IntraWeb Web Stateful
│       ├── tdd-fpcunit/             # TDD com FPCUnit
│       ├── horse-framework/         # Horse REST API
│       ├── acbr-components/         # Componentes ACBr
│       ├── test-fpcunit/            # Testes unitários com FPCUnit
│       ├── firebird-database/       # ✨ Firebird Database
│       ├── postgresql-database/     # ✨ PostgreSQL Database
│       ├── mysql-database/          # ✨ MySQL/MariaDB
│       ├── threading/               # ✨ Threading (TThread)
│       └── code-review/             # Revisão de código
│
├── .gemini/
│   └── skills/
│       ├── clean-code/              # Clean Code e Pascal Guide
│       ├── lazarus-memory-exceptions/# Memory management e try..finally
│       ├── lazarus-patterns/         # Repository, Service, Factory
│       ├── design-patterns/         # ✨ Design Patterns GoF (23 padrões)
│       ├── refactoring/             # ✨ Refatoração (10 técnicas)
│       ├── intraweb-framework/      # IntraWeb Web Stateful
│       ├── tdd-fpcunit/              # TDD com FPCUnit
│       ├── horse-framework/         # Horse REST API
│       ├── acbr-components/         # Componentes ACBr
│       ├── test-fpcunit/            # Testes unitários com FPCUnit
│       ├── firebird-database/       # ✨ Firebird Database
│       ├── postgresql-database/     # ✨ PostgreSQL Database
│       ├── mysql-database/          # ✨ MySQL/MariaDB
│       ├── threading/               # ✨ Threading (TThread)
│       └── code-review/             # Revisão de código
│
├── .kiro/
│   └── steering/
│       ├── product.md               # Visão do produto
│       ├── tech.md                  # Stack tecnológica
│       ├── structure.md             # Arquitetura de camadas
│       └── frameworks.md            # Guias de frameworks
│
└── examples/
    ├── clean-unit-example.pas        # Unit bem organizada (Golden Path)
    ├── memory-exception-example.pas  # Memória e exceções corretas
    ├── repository-pattern.pas        # Repository Pattern completo
    ├── service-pattern.pas           # Service Pattern completo
    ├── design-patterns-example.pas   # ✨ Design Patterns GoF na prática
    ├── refactoring-example.pas       # ✨ Refatoração antes/depois
    ├── tdd-fpcunit-example.pas       # TDD e FPCUnit na prática
    ├── horse-api-example.pas         # API REST com Horse
    ├── acbr-service-example.pas      # Emissão NFe com ACBr
    ├── firebird-repository-example.pas # ✨ Repository com SQLdb/Zeos + Firebird
    ├── postgresql-repository-example.pas # ✨ Repository com SQLdb/Zeos + PostgreSQL
    ├── mysql-repository-example.pas  # ✨ Repository com SQLdb/Zeos + MySQL
    └── threading-example.pas         # ✨ Threading patterns (TThread)
```

---

## ⚡ Quick Start

### 1. Clone ou baixe o kit

```bash
git clone https://github.com/delphicleancode/lazarus-spec-kit.git
```

### 2. Copie para a raiz do seu projeto Lazarus

```
Seu-Projeto/
├── MeuApp.lpi
├── MeuApp.lpr
├── AGENTS.md          ← copie da raiz
├── .github/           ← copie a pasta
├── .cursor/           ← copie a pasta
├── .claude/           ← copie a pasta
├── .gemini/           ← copie a pasta
└── .kiro/             ← copie a pasta
```

### 3. A IA assume as regras automaticamente

- **Cursor** — Lê os `.cursor/rules/*.md` automaticamente pelo contexto
- **GitHub Copilot** — Lê `.github/copilot-instructions.md` no workspace
- **Claude** — Usa as skills em `.claude/skills/`; cada pasta pode ser zipada para upload no Claude
- **Antigravity / Gemini** — Skills em `.gemini/skills/` são ativadas por demanda
- **Kiro** — Lê `.kiro/steering/*.md` como contexto fixo de produto

> **Nenhuma configuração adicional necessária.** Abra o projeto, use sua IA preferida e observe a diferença.

---

## 💡 Exemplos de Boas Práticas

### Arquitetura de Camadas

```
src/
├── Domain/         ← Entidades, Value Objects, Interfaces de Repositório
├── Application/    ← Services, Use Cases, DTOs
├── Infrastructure/ ← Repositórios SQLdb/Zeos, APIs externas
└── Presentation/   ← Forms LCL, ViewModels
tests/
└── Unit/           ← Projetos FPCUnit com Fakes isolados
```

> **Regra de dependência:** `Presentation → Application → Domain ← Infrastructure`
> O **Domain nunca** depende de outras camadas.

### Guard Clauses (sem nesting desnecessário)

```pascal
procedure ProcessOrder(AOrder: TOrder);
begin
  if not Assigned(AOrder) then
    raise Exception.Create('AOrder não pode ser nil');
  if AOrder.Items.Count = 0 then
    raise Exception.Create('Pedido precisa ter ao menos um item');
  if not AOrder.IsValid then
    raise Exception.Create('Validação do pedido falhou');

  // lógica real aqui, sem nesting
  FRepository.Save(AOrder);
  FNotifier.Send(AOrder.Customer.Email);
end;
```

### Teste com Fake via Interface

```pascal
type
  TFakeOrderRepository = class(TInterfacedObject, IOrderRepository)
  private
    FOrders: TObjectList;
  public
    constructor Create;
    destructor Destroy; override;
    procedure Save(AOrder: TOrder);
    function FindById(AId: Integer): TOrder;
  end;

  TOrderServiceTest = class(TTestCase)
  private
    FSut: TOrderService;
    FRepo: IOrderRepository;
  protected
    procedure SetUp; override;
    procedure TearDown; override;
  published
    procedure TestPlaceOrder_ValidOrder_SavesToRepository;
    procedure TestPlaceOrder_EmptyItems_RaisesException;
  end;
```

---

## 🤝 Contribuições

Pull Requests são bem-vindos! Se seu framework ou biblioteca Lazarus/FreePascal favorita precisa de um guia para a IA, adicione:

1. **Rule do Cursor** → `.cursor/rules/seu-framework.md`
2. **Skill do Claude** → `.claude/skills/seu-framework/SKILL.md`
3. **Skill do Gemini** → `.gemini/skills/seu-framework/SKILL.md`
4. **Referência** → mencione no `AGENTS.md`

### Como contribuir

```bash
# Fork e clone
git fork https://github.com/delphicleancode/lazarus-spec-kit
git clone https://github.com/SEU-FORK/lazarus-spec-kit

# Crie uma branch descritiva
git checkout -b feat/add-zeos-patterns

# Commit e Pull Request
git commit -m "feat: add ZeosLib patterns"
git push origin feat/add-zeos-patterns
```

---

<div align="center">

Deixe um cafézinho para o autor pix: <pix@inovefast.com.br> ☕

Feito com ❤️ para a comunidade **Lazarus/FreePascal**.

*Se este kit te ajudou, deixe uma ⭐ no repositório!*

</div>
