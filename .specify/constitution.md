# Constituição — FreePascal/Lazarus Spec-Kit

> Princípios fundamentais que governam todo o desenvolvimento neste projeto.

## Linguagem e Plataforma

Este projeto utiliza **FreePascal/Lazarus (Object Pascal)** com o framework visual **LCL** e acesso a dados via **SQLdb** ou **ZeosLib**. Todo o código gerado DEVE seguir as convenções nativas do **Object Pascal Style Guide** e boas práticas de código limpo LCL.

## Princípios Inegociáveis

### 1. SOLID Always

- **SRP:** Uma classe, uma responsabilidade. Separar validação, persistência e apresentação visual.
- **OCP:** Extensão via interfaces e herança, não modificação de classes existentes.
- **LSP:** Subtipos substituíveis sem quebrar comportamento.
- **ISP:** Interfaces pequenas e focadas.
- **DIP:** Depender de abstrações (interfaces), usar constructor injection.

### 2. Clean Code Always

- Métodos ≤ 20 linhas
- Nomes auto-descritivos em PascalCase
- Guard clauses em vez de nesting
- Constantes nomeadas (sem números mágicos)
- XMLDoc ou PasDoc para APIs públicas e classes base

### 3. Arquitetura em Camadas

```
Presentation → Application → Domain ← Infrastructure
```

O **Domain nunca depende** de outras camadas. A Infrastructure implementa as interfaces definidas em Domain.

### 4. Convenções do Pascal Guide

- Prefixos obrigatórios: `T`, `I`, `E`, `F`, `A`, `L`
- Units nomeadas como: `Projeto.Camada.Dominio.Funcionalidade.pas` (exemplo: `App.Domain.User.Entity.pas`)
- Componentes LCL com prefixo de 3 letras: `btn`, `edt`, `lbl`, `cmb`, `qry`, `ds`, etc.

### 5. Proibições Absolutas

- ❌ `with` statement
- ❌ Variáveis globais dinâmicas
- ❌ Lógica de negócio em event handlers de forms LCL
- ❌ Catch genérico de exceptions omitindo erros nativos
- ❌ God classes ou God units
- ❌ Ignorar gerenciamento de memória (esquecer Try/Finally)

## Processo de Desenvolvimento

1. **Especificar** — Definir requisitos e critérios de aceitação
2. **Planejar** — Projetar interfaces e classes antes de implementar
3. **Implementar** — Código limpo seguindo SOLID e convenções
4. **Testar** — FPCUnit para testes unitários com foco em TDD
5. **Revisar** — Verificar aderência às regras desta constituição
