# Tarefas: [Nome da Feature]

## Legenda

- `[ ]` — Pendente
- `[/]` — Em progresso
- `[x]` — Concluída

## 1. Domain Layer

- [ ] Criar entidade `T[Nome]` em `*.Domain.[X].Entity.pas`
  - [ ] Definir propriedades e validações de domínio
  - [ ] Criar enum `T[Nome]Status` ou relacionados
  - [ ] Documentar propriedades vitais

- [ ] Criar interface contratual `I[Nome]Repository` em `*.Domain.[X].Repository.Intf.pas`
  - [ ] Assinar métodos CRUD padrão (FindById, FindAll, Insert, Update, Delete)
  - [ ] Incluir consultas específicas requeridas pela engine

## 2. Application Layer

- [ ] Criar interface do service `I[Nome]Service` em `*.Application.[X].Service.Intf.pas`
  - [ ] Mapear as assinaturas blindadas de UI

- [ ] Criar service concreto `T[Nome]Service` em `*.Application.[X].Service.pas`
  - [ ] Constructor Injection obrigatório com dependência em `I[Nome]Repository`
  - [ ] Implementar validações de negócio isoladas
  - [ ] Guard clauses focadas
  - [ ] Blocos ≤ 20 linhas (Clean Code)

## 3. Infrastructure Layer

- [ ] Criar repository `T[Nome]Repository` em `*.Infra.[X].Repository.pas`
  - [ ] Codificar a implementação de `I[Nome]Repository` focando no SQLdb / ZeosLib
  - [ ] Proteger as Query temporárias nativas (`TSQLQuery`/`TZQuery`) com Try/Finally no uso local
  - [ ] Parametrizar a passagem de SQL Strings 100% blindado contra SLQ Injection, nunca concatenando strings

- [ ] Atualizar o registro da Factory em `*.Infra.Factory.pas`
  - [ ] Configurar método `Create[Nome]Repository`
  - [ ] Configurar injeção natural em `Create[Nome]Service`

- [ ] Setup do Banco de Dados
  - [ ] Preparar a migração
  - [ ] Executar contra o schema dev

## 4. Presentation Layer

- [ ] Criar LCL Form para a Listagem `Tfrm[Nome]List` em `*.Presentation.[X].List.pas`
  - [ ] PageControl e navegação por abas
  - [ ] Exibição formatada em DBGrid nativa ou StringGrid customizada
  - [ ] Botões base LCL de fluxo de estado

- [ ] Criar LCL Form para as Edições (CRUD) `Tfrm[Nome]Edit` em `*.Presentation.[X].Edit.pas`
  - [ ] Vincular lógica puramente à resposta do Service via Interface
  - [ ] Travar persistência através dos eventos do Form base
  - [ ] Notificar erros isoladamente por blocos de Exception tipados

## 5. Testes

- [ ] Inicializar bateria unitária FPCUnit no projeto de Testes correspondente
  - [ ] Validar comportamento padrão (happy path)
  - [ ] Validar comportamento de exceções geradas pelo Service de domínio
  - [ ] Interceptar Memory Leaks na Suite LCL TestRunner

- [ ] Mock de Infraestrutura
  - [ ] Fake Repository implementando `I[Nome]Repository` para os testes

## 6. Revisão Final

- [ ] Registrar acesso ao form novo
  - [ ] Code review com foco na independência LCL
  - [ ] Commit e Push finalizando Spec
