---
description: "Padrões de desenvolvimento com IntraWeb em FreePascal/Lazarus — controle de sessão, threads, TIWAppForm, memory management"
globs: ["**/*.pas"]
alwaysApply: false
---

# IntraWeb Framework — Cursor Rules

Use estas regras ao desenvolver ou dar manutenção em aplicações IntraWeb utilizando FreePascal e Lazarus.

## Arquitetura e Sessão

- **Sessão do Usuário:** Para dados sensíveis ou estado do usuário, SEMPRE utilize a propriedade `UserSession`. NUNCA utilize variáveis globais isoladas na unit do formulário. O IntraWeb é stateful e Multi-threaded, variáveis globais causam atropelamento de sessão.
- **TIWAppForm:** Todo e qualquer formulário visual renderizado deve herdar de `TIWAppForm`. Não utilize `TForm` (LCL) nesses casos.
- **Thread Safety:** Requisições simultâneas em IntraWeb correm em threads separadas. O acesso a instâncias de banco (como o TSQLConnection) deve ser protegido ou idealmente, usar um DataModule unicamente instanciado por sessão.

## Memory Management

- Sempre crie formulários IntraWeb em runtime usando o construtor padrão passando `WebApplication`: `TIWForm1.Create(WebApplication);`.
- O IntraWeb usa controles de fluxo nativos para limpeza. Não chame o `.Free` manualmente nos formulários base a menos que expressamente fora do ciclo dinâmico gerenciado.

## Anti-Patterns Evitados

- ❌ Formulários `ShowModal` baseados em LCL tradicional
- ❌ Uso de variáveis globais que não sejam protegidas ou locais em `UserSession`
- ❌ Criação de controles IntraWeb (`TIWEdit`, `TIWLabel`) injetados diretamente na unit LCL (não se mistura).
