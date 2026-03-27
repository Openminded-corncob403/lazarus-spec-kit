# Stack Técnica — FreePascal/Lazarus

## Linguagem e Ferramentas

- **Linguagem:** Object Pascal
- **Compilador:** FreePascal (FPC 3.x)
- **Build System:** lazbuild (arquivos `.lpi` / `.lpr`)
- **IDE Nativa:** Lazarus IDE (3.x ou superior)

## Bibliotecas Principais

| Componente | Uso |
|-----------|-----|
| **LCL** (Lazarus Component Library) | Interfaces desktop nativas cross-platform |
| **SQLdb / ZeosLib** | Acesso rápido, universal e nativo a banco de dados |
| **FPCUnit** | Framework oficial nativo de testes unitários |

## Bancos de Dados Suportados

- **Firebird** — Banco de dados robusto de adoção massiva no ecossistema FPC.
- **PostgreSQL** — Focado em microsserviços web modernos e JSONB.
- **MySQL / MariaDB** — Adoção massiva para instâncias web com MySQL.
- **SQLite** — Protótipos portáteis locais e embedded.

### Firebird — Regras Críticas no Lazarus

- **Driver:** Acesso nativo rápido pelo SQLdb.
- **Dialect 3 SEMPRE:** Configuração imperativa para `SQLDialect := 3`.
- **CharacterSet UTF8:** Lazarus utiliza UTF8 nativamente na LCL, garanta match no DB.
- **RETURNING:** Componentes requerem instrução de query do framework específico (Open).
- **Skills / Rules:** Ver `firebird-database` em skills.

### PostgreSQL e MySQL — Regras

- Utilizar bibliotecas específicas do S.O. na runtime. Ex: `libpq.dll`, `libmysql.dll`.
- PostgreSQL priorizar Identity Columns; MySQL não tem select via returning, requer calls manuais de insert ID via framework.

### Padrões Estritos FPC (Evite Delphi-ismos)

- **Interfaces e Assinaturas:** Interfaces devem conter `['{GUID}']` para o correto ARC. Métodos da interface implementados na classe não devem conter `override`.
- **Generics:** Coleções genéricas devem usar `specialize TFPGList<T>` (unit `fgl`).
- **Bibliotecas:** Evite bibliotecas `System.*` do Delphi (ex: use `FpJSON` no lugar de `System.JSON`).
- **Localização:** Use `DefaultFormatSettings` de `sysutils`.
- **UI:** Extensões visualmente exclusivas `.lfm`, nunca `.dfm`.

### Threading & Multi-Threading — Regras Críticas

- **Regra de Ouro:** Acesso a form UI `(LCL)` NUNCA pode acorrer de theads filhas não sincronizadas.
- **TThread.Queue / TThread.Synchronize:** Padrão ouro oficial de comunicação de threads com a UI.
- **Sincronização Atômica:** Qualquer recurso (string lists, variaveis int globais) compartilhado por threads deve invocar Locks como `TCriticalSection` obrigatoriamente (dentro de finally blocks).

## Padrões de Código e Gerenciamento

### Tipos de Arquivo FPC/Lazarus

| Extensão | Descrição |
|----------|-----------|
| `.pas` / `.pp` | Unit (código-fonte) |
| `.lpr` | Lazarus Project file |
| `.lpi` | Lazarus Project Info (Metadata/Configuração build) |
| `.lpk` | Lazarus Package info |
| `.lfm` | Form design layout visual de interfaces LCL |

### Variáveis Locais e Block Scope

No compilador FreePascal / Lazarus prefere-se a declaração clássica limpa na cláusula global da *implementation* do método, instanciando-se a respectiva variável assim que acionar o *begin*, visando a proteção *try/finally*. Evite declarações embaralhadas.

### Testagem e Qualidade (TDD)

- Uso padrão do framework **FPCUnit**.
- O desenvolvimento deve ser isolado (Domain/App Services) via padronização de injeção de dependências sem tocar LCL diretamente no backend em tests.
- Todo Mock deverá ser gerenciado com instâncias Fake ou usando implementações interface-driven (`CORBA` isoladas ou normais ARC) para prevenir *memory leaks* após o test case falhar na suite FPC.
