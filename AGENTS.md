# FreePascal Lazarus AI Spec-Kit — AGENTS.md

> Este arquivo é reconhecido automaticamente por **Antigravity**, **GitHub Copilot**, **Cursor** e **Kiro**.
> Ele define as regras universais para desenvolvimento FreePascal e Lazarus com IA.

## Linguagem e Stack

- **Linguagem:** Object Pascal (FreePascal / FPC)
- **IDE Nativa:** Lazarus
- **Frameworks:** LCL (Lazarus Component Library)
- **Banco de dados:** SQLdb, ZeosLib (SQLite, PostgreSQL, Firebird, MySQL, SQL Server)
- **Testes:** FPCUnit, DUnit
- **Build:** lazbuild / fpc
- **Extensões de arquivo:** `.pas` / `.pp` (units), `.lfm` (forms), `.lpr` (project), `.lpk` (package), `.lpi` (project info)

## Padrões Específicos FreePascal / Lazarus (vs Delphi)

Como a IA pode ser enviesada para Delphi, reforce sempre os seguintes padrões nativos FPC:
- **Interfaces e Assinaturas:** Interfaces devem declarar um GUID (`['{GUID}']`) para correto funcionamento de ARC ou compatibilidade COM. Ao implementar os métodos da interface na classe concreta, **nunca** use a diretiva `override` (a menos que esteja sobrescrevendo um método herdado da classe base).
- **Generics:** Utilize `specialize TFPGList<T>` (da unit `fgl`) como padrão para listas genéricas, evitando a sintaxe `TList<T>` direta do Delphi.
- **Bibliotecas Nativas (JSON):** Para operações JSON, utilize a biblioteca nativa `FpJSON` (`jsonparser`, `fpjson`). **Não utilize** `System.JSON` que é exclusivo do Delphi.
- **Formatação Regional:** Utilize `DefaultFormatSettings` (da unit `sysutils`) no lugar de funções do Windows como `GetLocaleFormatSettings`.
- **Arquivos de UI:** Reforce o uso apenas de **`.lfm`**. Jamais utilize ou gere referências a `.dfm`.

## Convenções de Nomenclatura — Pascal Guide

### Regra Geral

Usar **PascalCase** (InfixCaps) para todos os identificadores. Palavras reservadas sempre em **minúsculas** (`begin`, `end`, `if`, `then`, `else`, `nil`, `string`).

### Prefixos Obrigatórios

| Tipo | Prefixo | Exemplo |
|------|---------|---------|
| Classe | `T` | `TCustomerRepository` |
| Interface | `I` | `ICustomerRepository` |
| Exception | `E` | `ECustomerNotFound` |
| Campo privado | `F` | `FCustomerName` |
| Parâmetro | `A` | `ACustomerName` |
| Tipo enumerado | `T` | `TOrderStatus` |
| Itens de enum | prefixo curto | `osNew`, `osPending`, `osClosed` |

### Nomenclatura de Units

```
NomeProjeto.Camada.Dominio.Funcionalidade.pas
```

Exemplos:

- `MeuApp.Domain.Customer.Entity.pas`
- `MeuApp.Infra.Customer.Repository.pas`
- `MeuApp.Application.Customer.Service.pas`
- `MeuApp.Presentation.Customer.View.pas`

### Nomenclatura de Métodos

- Métodos de ação: usar verbos — `Execute`, `CreateOrder`, `ValidateCustomer`
- Getters: prefixo `Get` — `GetCustomerName`
- Setters: prefixo `Set` — `SetCustomerName`
- Funções booleanas: prefixo `Is`, `Has`, `Can` — `IsValid`, `HasPermission`, `CanDelete`

### Nomenclatura de Testes de Unidade (TDD)

- Seguir o padrão genérico comportamental em testes FPCUnit ou DUnit: `Action_Condition_ExpectedResult`
- Exemplo: `ProcessOrder_WithoutStock_RaisesException`, `CalculateTotal_WithDiscount_ReturnsLowerValue`
- Crie fakes na unit de teste com prefixo `TFake` (ex: `TFakeInventoryRepository`)

### Nomenclatura de Forms e DataModules

- Tipo: `TfrmCustomerEdit`, `TdmDatabase`
- Variável: `frmCustomerEdit`, `dmDatabase`
- Unit: `MeuApp.Presentation.Customer.Edit.pas`

### Componentes em Forms

Usar prefixo de 3 letras indicando o tipo (LCL padrão):

| Componente | Prefixo | Exemplo |
|-----------|---------|---------|
| TButton | `btn` | `btnSave` |
| TEdit | `edt` | `edtName` |
| TLabel | `lbl` | `lblName` |
| TComboBox | `cmb` | `cmbStatus` |
| TDBGrid | `dbg` | `dbgCustomers` |
| TPanel | `pnl` | `pnlTop` |
| TPageControl | `pgc` | `pgcMain` |
| TTabSheet | `tab` | `tabSearch` |
| TDataSource | `ds` | `dsCustomers` |
| TSQLQuery / TZQuery | `qry` | `qryCustomers` |
| SQLConnection / TZConnection | `con` | `conMain` |
| TMemo | `mmo` | `mmoObservation` |
| TCheckBox | `chk` | `chkActive` |
| TDateTimePicker | `dtp` | `dtpBirthDate` |
| TImage | `img` | `imgPhoto` |
| TListView | `lvw` | `lvwItems` |
| TTreeView | `tvw` | `tvwCategories` |
| TToolBar | `tlb` | `tlbMain` |
| TActionList | `act` | `actMain` |
| TPopupMenu | `pmn` | `pmnGrid` |
| TTimer | `tmr` | `tmrRefresh` |
| TStatusBar | `stb` | `stbMain` |

### Projeto ACBr (Automação Comercial)

| Componente | Prefixo | Exemplo |
|-----------|---------|---------|
| TACBrNFe | `acbrNFe` | `acbrNFe1` ou `acbrNfeEmissor` |
| TACBrCTe | `acbrCte` | `acbrCteMain` |
| TACBrBoleto | `acbrBoleto` | `acbrBoletoCob` |
| TACBrTEFD | `acbrTef` | `acbrTefVisa` |
| TACBrPosPrinter | `acbrPosPrinter`| `acbrPosPrinterCaixa` |
| TACBrSAT | `acbrSat` | `acbrSatFiscal` |
| TACBrCEP | `acbrCep` | `acbrCepBusca` |

**Nota ACBr:** Evite prender a UI (LCL) diretamente em eventos interativos do componente. Isole a lógica fiscal.

## Frameworks REST (Horse)

### Horse Framework

Horse é um framework REST minimalista perfeitamente compatível com FreePascal/Lazarus. Convenções:

- **Controller:** Classe com `class procedure RegisterRoutes`
- **Handler:** `class procedure Nome(AReq: THorseRequest; ARes: THorseResponse; ANext: TProc)`
- **Middleware:** `procedure Nome(AReq: THorseRequest; ARes: THorseResponse; ANext: TProc)`
- **Rotas:** Kebab-case, plural — `/api/customers`, `/api/order-items`
- **JSON:** Usar middleware `Jhonson` para serialização automática com FpJSON
- **CORS:** Usar middleware `Horse.CORS`
- **Estrutura:** Controllers separados de Services, Services separados de Repositories
- **Pacotes:** `boss install horse horse-jhonson horse-cors horse-jwt`

## Frameworks Web Stateful (IntraWeb)

### IntraWeb Framework

O IntraWeb suporta a criação de aplicações Web com design tradicional via Object Inspector, focado em stateful components no servidor.

- **Sessão do Usuário:** Para dados de sessão, NUNCA use variáveis em units globais (var section). Utilize sempre o objeto global `UserSession`.
- **Formulários Web:** As telas devem derivar de `TIWAppForm` no lugar de classes LCL (`TForm`).
- **Instanciação LCL Cega:** Não cruze componentes da LCL padrão (como botoes comuns, queries) diretamente na UI sem encapsulamento de sessão (threads colidem requisições). Sempre instancie componentes `TIW` (como `TIWEdit`, `TIWButton`).

## Banco de Dados Firebird

Firebird é um banco de dados corporativo excelente para uso com Lazarus. Acesso recomendado via **SQLdb** ou **ZeosLib**.

### Regras Essenciais Firebird

- **Dialect 3 SEMPRE** — Dialect 1 é legado InterBase e causa ambiguidade com `DATE`
- **CharacterSet UTF8** — obrigatório para suporte correto a acentos na LCL (Lazarus usa UTF-8 nativamente)
- **Queries parametrizadas** — nunca concatenar strings em SQL
- **Execução** — Use `ExecSQL` para INSERT/UPDATE/DELETE. Para `RETURNING`, frameworks como Zeos conseguem mapear internamente, ou via stored procedures.
- **Generators** para auto-increment com triggers `BEFORE INSERT`
- **Transactions explícitas** para operações compostas (SQLTransaction.Commit/Rollback)

### Anti-Patterns Firebird

- ❌ `SQLDialect := '1'` — usar SEMPRE `'3'`
- ❌ Concatenar SQL — usar parâmetros
- ❌ Ignorar `CharacterSet` — definir `UTF8`
- ❌ Ignorar transactions — em SQLdb/Zeos tudo roda dentro de uma transaction. Gerencie o Commit.

> **Skills:** `.claude/skills/firebird-database/SKILL.md`, `.gemini/skills/firebird-database/SKILL.md`
> **Rules:** `.cursor/rules/firebird-patterns.md`

## Banco de Dados PostgreSQL

Acesso via **SQLdb** (driver `PostgreSQL`) ou **ZeosLib** (`postgresql`).

### Regras Essenciais PostgreSQL

- **IDENTITY em vez de SERIAL** — usar `GENERATED ALWAYS AS IDENTITY` para novos projetos (PG 10+)
- **UPSERT nativo** — `INSERT ... ON CONFLICT (col) DO UPDATE SET ...`
- **JSONB** — para dados semi-estruturados, indexável com GIN
- **ENUM types** — `CREATE TYPE status AS ENUM ('active', 'inactive')`
- **PL/pgSQL** — Functions (`RETURNS TABLE` = Selectable), Procedures (`CALL`, PG 11+)
- **Transactions explícitas** — Suporta `SAVEPOINT` e gerenciamento estrito de commits
- **Full-Text Search** — `tsvector` + `tsquery` com índice GIN

### Anti-Patterns PostgreSQL

- ❌ Concatenar SQL — usar parâmetros
- ❌ `SERIAL` em novos projetos — usar `IDENTITY`
- ❌ `SELECT *` em tabelas grandes — selecionar colunas necessárias
- ❌ N+1 queries — usar JOIN ou subquery
- ❌ Guardar JSON como TEXT — usar `JSONB`

> **Skills:** `.claude/skills/postgresql-database/SKILL.md`, `.gemini/skills/postgresql-database/SKILL.md`
> **Rules:** `.cursor/rules/postgresql-patterns.md`

## Banco de Dados MySQL / MariaDB

Acesso via **SQLdb** (driver `MySQL`) ou **ZeosLib** (`mysql` / `mariadb`).

### Regras Essenciais MySQL

- **`utf8mb4` SEMPRE** — `utf8` no MySQL só tem 3 bytes. Usar `utf8mb4`
- **AUTO_INCREMENT + LAST_INSERT_ID()** — Para obter o ID recém-inserido
- **UPSERT nativo** — `INSERT ... ON DUPLICATE KEY UPDATE`
- **JSON nativo** — tipo `JSON` com operadores `->>`/`JSON_EXTRACT`
- **InnoDB SEMPRE** — nunca MyISAM em novos projetos
- **Transactions explícitas** — Componente Transaction próprio ou configuração de AutoCommit no Zeos

### Anti-Patterns MySQL

- ❌ `utf8` como charset — usar `utf8mb4`
- ❌ `MyISAM` em novas tabelas — usar `InnoDB`
- ❌ Concatenar SQL — usar parâmetros
- ❌ `SELECT *` sem `LIMIT` — paginar resultados
- ❌ N+1 queries — usar JOIN ou subquery

> **Skills:** `.claude/skills/mysql-database/SKILL.md`, `.gemini/skills/mysql-database/SKILL.md`
> **Rules:** `.cursor/rules/mysql-patterns.md`

## Threads e Multi-Threading

Threads são fundamentais para manter a UI responsiva. FreePascal usa a classe base `TThread`.

### Regra de Ouro

> **NUNCA acesse componentes visuais da LCL diretamente de uma thread secundária.**
> Use `TThread.Synchronize` (bloqueante) ou `TThread.Queue` (não-bloqueante) para atualizar a interface no Lazarus.

### Abordagens de Threading

| Abordagem | Quando Usar |
|-----------|-------------|
| `TThread` (herança) | Forma mais flexível e tradicional no FPC |
| `TThread.Execute` | Código que deve rodar fora da thread principal |
| `SyncObjs` unit | Para `TCriticalSection` e primitivas de sincronização |

### Thread-Safety

- **`TCriticalSection`** — Seção crítica clássica (`Enter`/`Leave` SEMPRE no `finally`)
- **`InterlockedIncrement`** — Operações atômicas nativas e rápidas
- **`TThreadList`** — Lista thread-safe com `LockList`/`UnlockList`

### Anti-Patterns de Threading

- ❌ Acessar LCL diretamente de thread secundária
- ❌ `Sleep()` na main thread (congela a UI do Lazarus)
- ❌ Acessar variáveis compartilhadas sem lock
- ❌ `TCriticalSection.Leave` fora de `finally`

> **Skills:** `.claude/skills/threading/SKILL.md`, `.gemini/skills/threading/SKILL.md`
> **Rules:** `.cursor/rules/threading-patterns.md`

## Princípios SOLID em FreePascal

### S — Single Responsibility Principle (SRP)

Cada unit e cada classe deve ter **uma única responsabilidade**:

```pascal
// ✅ BOM — responsabilidades separadas
TCustomerValidator = class
  function Validate(ACustomer: TCustomer): Boolean;
end;

TCustomerRepository = class(TInterfacedObject, ICustomerRepository)
  function FindById(AId: Integer): TCustomer;
  procedure Save(ACustomer: TCustomer);
end;
```

### O — Open/Closed Principle (OCP)

Classes devem ser **abertas para extensão**, fechadas para modificação. Use herança e interfaces:

```pascal
type
  IReportExporter = interface
  ['{11111111-1111-1111-1111-111111111111}']
    procedure Export(AReport: TReport);
  end;

  TPdfExporter = class(TInterfacedObject, IReportExporter)
    procedure Export(AReport: TReport);
  end;
```

### L — Liskov Substitution Principle (LSP)

Subtipos devem ser substituíveis pelo tipo base sem quebrar o comportamento:

```pascal
// ✅ BOM — qualquer ICustomerRepository funciona
procedure TCustomerService.LoadCustomer(ARepo: ICustomerRepository);
begin
  // funciona com TSQLdbCustomerRepo, TMemoryCustomerRepo, TMockCustomerRepo
  FCustomer := ARepo.FindById(FCustomerId);
end;
```

### I — Interface Segregation Principle (ISP)

Interfaces pequenas e coesas:

```pascal
// ✅ BOM — interfaces segregadas
type
  IReadableRepository = interface
  ['{GUID-HERE}']
    function FindById(AId: Integer): TObject;
  end;

  IWritableRepository = interface
  ['{GUID-HERE}']
    procedure Save(AEntity: TObject);
    procedure Delete(AId: Integer);
  end;
```

### D — Dependency Inversion Principle (DIP)

Dependa de **abstrações** (interfaces), não de implementações concretas:

```pascal
type
  TOrderService = class
  private
    FOrderRepo: IOrderRepository;
  public
    constructor Create(AOrderRepo: IOrderRepository);
  end;
```

## Clean Code — Regras Essenciais

### 1. Métodos Curtos

- Máximo **20 linhas** por método.

### 2. Nomes Auto-Descritivos

```pascal
// ❌ RUIM
procedure Proc1(S: string; N: Integer);

// ✅ BOM
procedure SendNotificationEmail(const ARecipientEmail: string; ATemplateId: Integer);
```

### 3. Evitar Números Mágicos

```pascal
const
  MINIMUM_AGE = 18;
if ACustomer.Age > MINIMUM_AGE then
```

### 4. Guard Clauses

```pascal
procedure ProcessOrder(AOrder: TOrder);
begin
  if not Assigned(AOrder) then
    raise Exception.Create('AOrder cannot be nil');
  
  // lógica principal sem grande indentação
end;
```

### 5. Try/Except Focado e Tipado

```pascal
try
  PerformCriticalAction;
except
  on E: EDatabaseError do
    raise Exception.Create('Falha de DB: ' + E.Message);
  on E: Exception do
  begin
    Logger.LogError('Erro genérico', E);
    raise; 
  end;
end;
```

## Padrões de Projeto Recomendados

| Padrão | Uso em FreePascal |
|--------|-------------------|
| **Repository** | Abstrai acesso a dados via interface (SQLdb, Zeos, etc.) |
| **Service** | Contém lógica de negócio orquestrando repositories e outros services |
| **Factory** | Cria instâncias de objetos complexos ou com dependências |
| **Observer** | Usar events ou interfaces para desacoplar notificações |

## Anti-Patterns a Evitar

- ❌ **God class / God unit** 
- ❌ **Acoplamento direto a forms** — lógica de negócio em `OnClick` de botões
- ❌ **Uses circular** 
- ❌ **Variáveis globais** 
- ❌ **Strings hardcoded** — usar `resourcestring`
- ❌ **`with` statement** — evitar `with` pois reduz legibilidade e dificulta debug

## Gerenciamento de Memória (Crítico)

- **Blocos Vigiados:** Toda vez que instanciar um `TObject` diretamente, siga com um `try..finally` caso a liberação ocorra no mesmo escopo local.

```pascal
// ✅ O Padrão Ouro para Objetos Descartáveis locais no FPC
var LList: TStringList;
begin
  LList := TStringList.Create;
  try
    LList.Add('item');
  finally
    LList.Free; // Free and Nil can also be used if needed
  end;
end;

// ✅ Objetos com dono (Owner) - Componentes LCL
TMyComponent := TMyComponent.Create(Self); // Owner assume liberação

// ✅ Interfaces garantem ARC automático
var LService: IMyService;
begin
  LService := TMyService.Create; 
  LService.DoSomething;
  // Memória é limpa automaticamente
end;
```

## Documentação

- Usar **PasDoc** ou **XMLDoc** simplificado para métodos públicos:

```pascal
/// <summary>
///   Localiza um cliente pelo CPF informado.
/// </summary>
function FindByCpf(const ACpf: string): TCustomer;
```

- Comentários em **português** para projetos focados no Brasil.

## Estrutura de Camadas (Arquitetura)

```
src/
├── Domain/           ← Entidades, Value Objects, Interfaces de repositório
├── Application/      ← Services, Use Cases, DTOs
├── Infrastructure/   ← Implementações (SQLdb, Zeos), APIs externas
└── Presentation/     ← Forms (LCL), ViewModels
tests/
└── Unit/             ← Projetos FPCUnit isolados
```

> **Regra de dependência:** `Presentation → Application → Domain ← Infrastructure`
> O Domain **nunca** depende de outras camadas. Os `tests` dependem de `Application` e `Domain` mas injetam implementações mock/fake da `Infrastructure`.
