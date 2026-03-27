---
description: Test-Driven Development (TDD) rigoroso com FPCUnit, nomenclaturas e injeção de dependências para isolamento com fakes e mocks.
globs: *Test*.pas, *.pas
---

# FreePascal TDD e Testes de Unidade (FPCUnit)

- **Test-First (Red-Green-Refactor):** Ao implementar novas regras sob ordem de "TDD", você DEVE estruturar primeiro o Test Case e suas asserções (`Assert`) falhas, antes de conceber os algoritmos concretos da lógica de negócio.
- **Nomenclatura Padrão Expressiva:** Prefixos de métodos de teste devem seguir o escopo semântico `Metodo_Cenario_Expectativa`. Exemplo: `[Test] procedure CalculateTax_EmptyOrder_ThrowsException;`.
- **Framework Padrão:** Use `FPCUnit`. Sempre decore as classes de teste herdando de `TTestCase`, definindo métodos na seção `published` e setups no `SetUp` / `TearDown`.
- **Isolamento Total (Fakes/Mocks):** NUNCA escreva um teste de unidade que envolva acoplamento direto com Banco de Dados `TSQLConnection`, APIs externas, Rede ou LCL/Forms. Tudo o que é externo à Service ou Entidade testada deve ser simulado no arquivo do teste em uma subclasse isolada FAKE implementando a Interface de dependência (`IMyRepository`).
- **Validação de Exceções:** Para provar Guard Clauses de domínio, instancie obrigatoriamente um bloco de código anônimo ou método procedural usando o `AssertException()` injetando o tipo esperado de `Exception`.
