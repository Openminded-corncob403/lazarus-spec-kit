# GitHub Copilot — Instruções para Projetos FreePascal/Lazarus

## Contexto

Este é um projeto **FreePascal/Lazarus (Object Pascal)** que segue princípios SOLID, clean code e o Object Pascal Style Guide. Consulte `AGENTS.md` na raiz do projeto para a referência completa de convenções.

## Diretrizes Gerais

1. **Sempre gere código em Object Pascal** (FreePascal/Lazarus) salvo quando explicitamente solicitado em outra linguagem.
2. **Use PascalCase** para todos os identificadores. Palavras reservadas em minúsculas (ex: `begin`, `end`, `class`).
3. **Respeite os prefixos** da convenção Pascal: `T` (classes), `I` (interfaces), `E` (exceptions), `F` (campos privados), `A` (parâmetros), `L` (variáveis locais).
4. **Prefira interfaces** a classes concretas para dependências, garantindo isolamento.
5. **Use constructor injection** para injeção de dependência.
6. **Nunca coloque lógica de negócio em event handlers** de forms LCL (`OnClick`, `OnChange`, etc.). Delegue para services ou controllers.
7. **Padrões FPC/Lazarus vs Delphi:** Use `.lfm` (não `.dfm`), `specialize TFPGList<T>` (unit `fgl`), `FpJSON` (não `System.JSON`), `DefaultFormatSettings`, e garanta que interfaces tenham GUID sem usar `override` em suas implementações.

## Estilo de Código

### Indentação e Formatação
- Indentação: **2 espaços** (sem tabs)
- `begin` na **mesma linha** de `if`, `for`, `while`, `with` quando em bloco único
- Limite de **120 caracteres** por linha
- `begin` em **nova linha** para a implementação de métodos.

### Seções de Unit
Ordenar seções da unit conforme:
```pascal
unit Nome;

interface

uses
  { RTL/LCL units },
  { Units do projeto };

type
  { Enums e Records }
  { Interfaces }
  { Classes }

implementation

uses
  { Units adicionais exclusivas de implementação };

{ Implementações }

end.
```

### Declaração de Variáveis
A declarar variáveis, agrupe-as no início do bloco de implementação (FreePascal padrão):
```pascal
var
  LCustomer: TCustomer;
  LCount: Integer;
begin
  LCustomer := TCustomer.Create('João');
  try
    // lógica
  finally
    LCustomer.Free;
  end;
end;
```

## Error Handling

- Use **exceptions específicas** (criar classes de exception por domínio):
  ```pascal
  EBusinessRuleException = class(Exception);
  EEntityNotFoundException = class(Exception);
  ```
- **Guard clauses** no início do método em vez de nesting (indentação) profunda.
- **Try/finally** para gerenciamento de memória imperativo.
- **Try/except** apenas para tratamento real de erros focados (ex: `on E: EDatabaseError do`), nunca para fluxo de controle.

## Documentação

- Gerar **PasDoc / XMLDoc** para métodos e propriedades públicas da camada de Domínio.
- Comentários em **português** para projetos brasileiros.
- Não comente o óbvio.

## Padrões de Projeto

Ao criar novas funcionalidades, siga a arquitetura em camadas:
- **Domain:** Entidades, Value Objects, Interfaces contratuais.
- **Application:** Services, Use Cases, DTOs.
- **Infrastructure:** Repositories (SQLdb/Zeos), APIs externas.
- **Presentation:** Forms nativos LCL ou Web (IntraWeb).

## O que NÃO gerar

- ❌ Não use `with` statement.
- ❌ Não crie variáveis globais ou Units globais sem proteção de thread.
- ❌ Não use números mágicos — declare constantes declarativas (`const`).
- ❌ Não faça catch genérico (`except on E: Exception do ShowMessage`). Use tratamentos tipados.
- ❌ Não misture acesso direto ao banco (queries) com UI.
- ❌ Não ignore o `Free` de objetos com tempo de vida local (use try..finally obrigatório).

## Frameworks e Ferramentas

### Horse (REST)
- Controller: declara `class procedure RegisterRoutes`
- Middleware comum: `THorse.Use(Jhonson)` para serialização JSON
- Rotas: pluralizadas e em kebab-case (`/api/order-items`).
- Delegar a regra para Services.

### IntraWeb (Web Stateful)
- Jamais declare dados dinâmicos em `var` global da unit do AppForm. Utilize `UserSession`.
- Formulários devem herdar de `TIWAppForm` no lugar de `TForm` (LCL puro).
- Evite código da LCL Clássica bloqueante, como `ShowMessage()` padrão.
- Ao instanciar queries em runtime, isole seu escopo da thread vinculada à requisição, para evitar colisões entre sessões simultâneas.

### Projeto ACBr (Automação Comercial)
- Isolar a lógica fiscal em classes de Serviço (ex: `TNFeService`) para não prender a UI LCL diretamente aos loops complexos e callbacks do componente.
- Lembre-se de liberar instâncias criadas em runtime na mesma thread local de emissão.

### SQLdb / Zeos (Banco de Dados)
- Prefira queries parametrizadas (nunca concatene input de usuário no SQL).
- Gerencie as **Transactions explícitas** (`Commit` / `Rollback`) nas operações de negócio complexas.
- Ao usar **Firebird**: Dialect 3, charset UTF8.
- Ao usar **PostgreSQL**: IDENTITY para chaves e JSONB nativo para dados semi-estruturados, UPSERT nativo suportado.
- Ao usar **MySQL / MariaDB**: charset `utf8mb4` e engine `InnoDB`. N+1 queries devem ser evitadas.

---

## 🧵 Threads e Multi-Threading no Lazarus

- **Regra de Ouro:** NUNCA acessar de forma cega componentes visualmente visíveis na LCL fora da thread principal.
- Use `TThread.Queue` (não-bloqueante) ou `TThread.Synchronize` (bloqueante) na thread background param interagir com os Forms.
- Proteja instâncias compartilhadas usando primitivas como `TCriticalSection` (Enter na Try/Leave obrigatoriamente no Finally).
- Não crie polling baseando-se em `Sleep()` excessivo dentro da main thread (isso congela completamente a UI do Lazarus).

## 🛑 Gerenciamento de Memória Nativo

- Em Object Pascal todo `TObject` instanciado livremente deve ser limpo. A palavra-chave `try` deve abrir logo após o `Create`. 
- Repassar instâncias recém-criadas direto para procedures (`Process(TObjeto.Create)`) provoca vazamento de memória silencioso. Instancie em variável local, abra o try/finally, e passe a variável (`Process(VarLoc)`).
- As instâncias com escopo de gestão ARC (onde se herda e acessa vias `IInterface`) são isentas de cleanup manual (Free), servindo como excelentes aliadas em Injeção de Dependências.
