---
description: "Padrões de Threading em FreePascal/Lazarus — TThread, Synchronize/Queue, TCriticalSection, thread-safety"
globs: ["**/*.pas"]
alwaysApply: false
---

# Threading & Multi-Threading — Cursor Rules

Use estas regras ao trabalhar com threads em FreePascal/Lazarus.

## Regra de Ouro

> **NUNCA acesse componentes visuais (LCL) de uma thread secundária.**
> Use `TThread.Synchronize` (bloqueante) ou `TThread.Queue` (não-bloqueante).

## Abordagens Disponíveis no FPC

| Abordagem | Quando Usar |
|-----------|-------------|
| `TThread` (herança de `Execute`) | Controle total, workers permanentes, servers |
| `TThread.Synchronize` | Atualizar UI de forma bloqueante |
| `TThread.Queue` | Atualizar UI de forma não-bloqueante (PREFERIR) |
| `TCriticalSection` | Seção crítica clássica (Enter/Leave no finally) |
| `TThreadList` | Lista thread-safe com LockList/UnlockList |
| `TEvent` | Sinalização entre threads (WaitFor/SetEvent) |
| `InterlockedIncrement`/`InterlockedDecrement` | Operações atômicas em Integer |
| `TMultiReadExclusiveWriteSynchronizer` | Cache: muitas leituras, poucas escritas |

> **Nota FPC:** As primitivas PPL do Delphi (`TTask`, `TParallel.For`, `IFuture`, `TInterlocked`, `TMonitor`, `TThreadedQueue`) **NÃO existem no FreePascal**. Use exclusivamente `TThread`, `TCriticalSection`, `TEvent` e funções atômicas nativas (`InterlockedIncrement`).

## Atualizar UI a Partir de Thread

```pascal
{ Queue: não-bloqueante (PREFERIR) }
TThread.Queue(nil, @AtualizarLabel);

{ Synchronize: bloqueante (quando precisa do resultado) }
TThread.Synchronize(nil, @AtualizarLabel);
```

## Thread com Herança (Padrão Recomendado)

```pascal
type
  TDataLoaderThread = class(TThread)
  protected
    procedure Execute; override;
  end;

procedure TDataLoaderThread.Execute;
begin
  while not Terminated do
  begin
    // Processar dados
    DoWork;

    // Atualizar UI via Queue
    Queue(@NotificarProgresso);
  end;
end;
```

## Thread-Safety

```pascal
{ TCriticalSection — SEMPRE Leave no finally }
FLock.Enter;
try
  Inc(FSharedCounter);
finally
  FLock.Leave;
end;

{ InterlockedIncrement — Operação atômica nativa FPC }
InterlockedIncrement(FProcessed);
```

## Cancelamento

```pascal
{ Via Terminated em TThread }
while not Terminated do
begin
  DoWork;
end;
```

## TEvent — Sinalização entre threads

```pascal
uses syncobjs;

FStopEvent := TEvent.Create(nil, True, False, '');

{ Thread: esperar sinal }
if FStopEvent.WaitFor(500) = wrSignaled then
  Break;

{ Main thread: sinalizar parada }
FStopEvent.SetEvent;
```

## Proibições de Threading no Lazarus

- ❌ Acessar LCL diretamente de thread secundária
- ❌ `Sleep()` na main thread (congela a UI!)
- ❌ `FreeOnTerminate := True` + `WaitFor` (crash!)
- ❌ Acessar variáveis compartilhadas sem lock
- ❌ Ignorar exceções em threads (são silenciosas!)
- ❌ Locks aninhados em ordem diferente (deadlock!)
- ❌ `TCriticalSection.Leave` fora de `finally`
- ❌ Usar `TTask`, `TParallel` ou `IFuture` (não existem no FPC!)
