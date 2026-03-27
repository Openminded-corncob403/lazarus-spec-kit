---
description: "Convenções FreePascal Lazarus — nomenclatura, estilo, formatação"
globs: ["**/*.pas", "**/*.pp", "**/*.lpr", "**/*.lpk"]
alwaysApply: true
---

# Convenções FreePascal Lazarus — Cursor Rules

## Nomenclatura

- **PascalCase** para todos os identificadores
- Palavras reservadas em **minúsculas** (`begin`, `end`, `if`, `nil`, `string`)
- Prefixos: `T` (classes), `I` (interfaces), `E` (exceptions), `F` (campos privados), `A` (parâmetros), `L` (variáveis locais)
- Units: `Projeto.Camada.Dominio.Funcionalidade.pas` (ou `.pp`)
- Componentes: prefixo de 3 letras (`btn`, `edt`, `lbl`, `cmb`, `pnl`, `qry`, `ds`, `con`)

## Formatação

- Indentação: 2 espaços
- Limite: 120 caracteres por linha
- `begin` na mesma linha para blocos `if`/`for`/`while`
- `begin` em nova linha para corpo de métodos

## Seções Obrigatórias da Unit

```pascal
unit Nome;
interface
uses { ... };
type { Enums → Interfaces → Classes }
implementation
uses { imports extras };
{ Implementação agrupada por classe }
end.
```

## Documentação

- PasDoc ou XMLDoc para métodos e propriedades públicas
- Comentários em português para projetos brasileiros
- Não comentar código auto-explicativo

## Gerenciamento de Memória

- `try/finally` com `Free` (ou `FreeAndNil`) para objetos temporários
- Interfaces Corba ou COM para reference counting (ARC) se ativado
- Variáveis locais com prefixo `L`
- Owner pattern para componentes visuais (LCL)

## Proibições (Atenção a Delphi-ismos)

- ❌ Delphi-ismos: `.dfm` (use `.lfm`), `System.JSON` (use `FpJSON`), `GetLocaleFormatSettings` (use `DefaultFormatSettings`).
- ❌ Assinaturas incorretas: Não use `override` ao implementar métodos de interface.
- ❌ Listas tipadas sem compatibilidade: Prefira `specialize TFPGList<T>` (unit `fgl`) a `TList<T>`.
- ❌ `with` statement
- ❌ Variáveis globais
- ❌ Catch genérico (`except on E: Exception`) sem tratar ou relançar apropriadamente
- ❌ Números mágicos — use constantes
- ❌ Strings hardcoded — use `resourcestring` ou constantes
- ❌ Métodos > 20 linhas
