---
description: "Padrões MySQL/MariaDB Database — conexão SQLdb/Zeos, AUTO_INCREMENT, LAST_INSERT_ID(), UPSERT, JSON, stored procedures, transactions"
globs: ["**/*.pas", "**/*.pp", "**/*.sql"]
alwaysApply: false
---

# MySQL / MariaDB Database — Cursor Rules

Use estas regras ao desenvolver com banco de dados MySQL ou MariaDB via SQLdb ou ZeosLib no Lazarus.

## Conexão Obrigatória

```pascal
{ Configuração base }
FConnection.ConnectorType := 'MySQL 8.0'; // Ou o mariadb correspondente
FConnection.HostName := 'localhost';
FConnection.DatabaseName := 'meubanco';
FConnection.UserName := 'root';
FConnection.Password := 'senha';
FConnection.CharSet := 'utf8mb4';  // NUNCA 'utf8' (só 3 bytes!)
FConnection.Transaction := FTransaction;
```

## AUTO_INCREMENT e LAST_INSERT_ID()

```pascal
// ⚠️ MySQL NÃO suporta RETURNING!
// Usar LAST_INSERT_ID() após INSERT

LQuery.SQL.Text := 'INSERT INTO customers (name) VALUES (:name)';
LQuery.ParamByName('name').AsString := ACustomer.Name;
LQuery.ExecSQL;

// Obter ID gerado
LQuery.SQL.Text := 'SELECT LAST_INSERT_ID() AS new_id';
LQuery.Open;
ACustomer.Id := LQuery.FieldByName('new_id').AsInteger;
```

## UPSERT — INSERT ... ON DUPLICATE KEY UPDATE

```pascal
LQuery.SQL.Text :=
  'INSERT INTO customers (cpf, name, email) ' +
  'VALUES (:cpf, :name, :email) ' +
  'ON DUPLICATE KEY UPDATE ' +
  '  name = VALUES(name), email = VALUES(email)';
LQuery.ExecSQL;
```

## JSON Nativo (MySQL 5.7+)

```pascal
{ Gravar JSON }
LQuery.ParamByName('settings').AsString := '{"theme":"dark"}';

{ Ler campo JSON — operador ->> }
LQuery.SQL.Text := 'SELECT settings->>''$.theme'' FROM ... WHERE id = :id';
```

## Stored Procedures e Functions

| Tipo | Chamada |
|------|---------|
| **Procedure** | `CALL sp_nome(:param)` + `ExecSQL` |
| **Function escalar** | `SELECT fn_nome(:param)` + `Open` |
| **Procedure com result set** | `CALL sp_nome(:param)` + `Open` |

## Transactions

```pascal
FTransaction.StartTransaction;
try
  FRepoA.Insert(LObjA);
  FRepoB.Insert(LObjB);
  FTransaction.Commit;
except
  FTransaction.Rollback;
  raise;
end;
```

## Tratamento de Erros MySQL

```pascal
except
  on E: EDatabaseError do
  begin
    raise EConflictException.Create('Falha de DB: ' + E.Message);
  end;
end;
```

## Cuidados Importantes

| Tópico | Regra |
|--------|-------|
| **Charset** | `utf8mb4` SEMPRE (nunca `utf8` — só 3 bytes no MySQL!) |
| **Engine** | `InnoDB` SEMPRE (nunca MyISAM em novos projetos) |
| **RETURNING** | ❌ NÃO existe no MySQL — usar `LAST_INSERT_ID()` |
| **COLLATE** | `utf8mb4_unicode_ci` para comparação case-insensitive correta |
| **Boolean** | `TINYINT(1)` — convenção MySQL, mapear como `AsBoolean` |
| **Timestamps** | `ON UPDATE CURRENT_TIMESTAMP` para auto-update |

## Metadata — Verificar Existência

```sql
-- Tabela existe?
SELECT COUNT(*) FROM information_schema.tables
WHERE table_schema = DATABASE() AND table_name = 'customers';

-- Coluna existe?
SELECT COUNT(*) FROM information_schema.columns
WHERE table_schema = DATABASE() AND table_name = 'customers' AND column_name = 'email';
```

## Proibições em MySQL

- ❌ Concatenar SQL — usar parâmetros
- ❌ `utf8` como charset — usar `utf8mb4`
- ❌ `RETURNING` (não existe) — usar `LAST_INSERT_ID()`
- ❌ `MyISAM` para novas tabelas — usar `InnoDB`
- ❌ `SELECT *` sem `LIMIT` — paginar com `LIMIT/OFFSET`
- ❌ N+1 queries — usar JOIN/subquery
- ❌ `RDB$` para metadata — usar `information_schema`
