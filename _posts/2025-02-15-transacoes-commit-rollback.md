---
title: "Transações com COMMIT e ROLLBACK"
date: 2025-02-15 00:00:00:0000
categories: [SQL]
tags: [sql]
---

Se você já trabalhou com um banco de dados relacional, sabe o que é uma transação e o quão importante ela é no banco de dados.

Então, você sabe que ao trabalhar com esse tipo de banco de dados, é essencial garantir a consistência e a integridade dos dados. Palavras bonitas, né?

O SQL fornece **transações** para lidar com operações que envolvem várias consultas. As transações nos permitem confirmar as alterações quando tudo ocorre bem ou revertê-las em caso de erros.

Este artigo explora as **instruções DTL** usando um esquema de banco de dados que criamos [neste artigo sobre modelagem de dados.](https://lorenzouriel.github.io/posts/data-modelling/)

## Entendendo Transações
Você já ouviu falar sobre **ACID**? [Tudo se resume a transações.](https://www.youtube.com/watch?v=RvPflBqV0OM)

Uma transação é uma sequência de instruções SQL que são executadas como uma única unidade de trabalho. Ela segue as propriedades ACID:
- **Atomicidade:** Garante que todas as operações dentro de uma transação sejam concluídas com sucesso. Se uma falhar, toda a transação é revertida.
- **Consistência:** Assegura a integridade dos dados antes e depois da transação.
- **Isolamento:** Impede que transações interfiram umas nas outras.
- **Durabilidade:** Garante que, uma vez confirmada, a transação permaneça no banco de dados.

### Comandos de Transação no SQL
- **BEGIN TRANSACTION:** Marca o início de uma transação.
- **COMMIT:** Salva todas as alterações feitas na transação.
- **ROLLBACK:** Desfaz todas as alterações caso ocorra um erro.

## Trabalhando com Transações

### O COMMIT
Vamos inserir um novo contato junto com objetivos e hábitos relacionados.

```sql
BEGIN TRANSACTION;

-- Insere novo contato
INSERT INTO [contacts] ([name], [surname], [email], [phone_number])
VALUES ('Lorenzo', 'Uriel', 'lorenzouriel@gmail.com', '1234567890');

-- Insere uma meta para o contato
INSERT INTO [goals] ([name], [description], [contact_id], [start], [end])
VALUES ('Read 10 Books', 'Complete reading 10 books this year', 2, '2025-01-01', '2025-12-31');

-- Insere uma hábito para o contato
INSERT INTO [habits] ([name], [description], [contact_id], [per_week], [per_month], [per_year])
VALUES ('Daily Exercise', 'Exercise for at least 30 minutes', 2, 5, 20, 240);

COMMIT;
```

- Se todas as instruções `INSERT` forem executadas com sucesso, o `COMMIT` vai salvar e finalizar a transação.
- Se alguma instrução falhar, devemos garantir o uso de `ROLLBACK` para manter a integridade dos dados. Isso nos leva ao exemplo abaixo.


### O ROLLBACK
Imagine que tentamos inserir dados, mas encontramos um erro.

```sql
BEGIN TRANSACTION;

BEGIN TRY

    -- Insere novo contato
    INSERT INTO [contacts] ([name], [surname], [email], [phone_number])
    VALUES ('Lorenzo', 'Uriel', 'lorenzouriel@gmail.com', '1234567890');
   
    -- Erro intencional: definindo um contact_id incorreto (-1)
    INSERT INTO [goals] ([name], [description], [contact_id], [start], [end])
    VALUES ('Learn SQL', 'Master SQL in 6 months', -1, '2025-02-01', '2025-08-01');
    
    COMMIT; -- Isso não será executado se ocorrer um erro

END TRY
BEGIN CATCH

    -- Reverte a transação em caso de erro
    ROLLBACK;
    PRINT 'Transaction failed. All changes rolled back.';

END CATCH;
```

Neste exemplo:
- O `contact_id = -1` inválido causa uma violação de restrição de chave estrangeira.
- O bloco `BEGIN CATCH` captura o erro e executa `ROLLBACK`.
- Isso garante que nenhum dado parcial seja salvo.

Isto é o que acontece quando você executa:
- ![result-query](/assets/images/2025-02-15-transacoes-commit-rollback/result-query.png)

Ainda não entendeu? Vamos analisar melhor este exemplo!

### Analisando o Exemplo
#### 1. `BEGIN TRANSACTION`

```sql
BEGIN TRANSACTION;
```

Isso inicia uma nova transação e o SQL Server marca este ponto no log de transações.

#### 2. Bloco `BEGIN TRY`

```sql
BEGIN TRY

END TRY
```

Esse bloco garante que os erros sejam capturados e tratados corretamente. Então, se tudo for executado com sucesso, a transação será confirmada. Se ocorrer um erro, o controle será transferido para o bloco `CATCH`.


#### 3. Inserts (com erro intencional)

```sql
INSERT INTO [contacts] ([name], [surname], [email], [phone_number])
VALUES ('Lorenzo', 'Uriel', 'lorenzouriel@gmail.com', '1234567890');

INSERT INTO [goals] ([name], [description], [contact_id], [start], [end])
VALUES ('Learn SQL', 'Master SQL in 6 months', -1, '2025-02-01', '2025-08-01');
```

Todas essas operações não são imediatamente confirmadas, mas ficam no log de transações.
A linha de inserção falha porque `-1` é um `contact_id` inválido. Devido à restrição de chave estrangeira em `contact_id`, o SQL Server gera um erro.

#### 4. `COMMIT`

```sql
COMMIT;
```

Esta linha nunca é executada porque o erro na etapa anterior transfere o controle para o bloco CATCH.

#### 5. Bloco `BEGIN CATCH`

```sql
BEGIN CATCH
  PRINT 'Transaction failed. All changes rolled back.';
END CATCH;
```

Aqui podemos tratar o erro, adicionando mensagens de `PRINT` e a instrução `ROLLBACK`. O SQL Server não confirma nenhuma alteração feita no bloco `TRY`.

#### 6. `ROLLBACK` Transaction

```sql
ROLLBACK;
```

Desfaz todas as alterações feitas dentro da transação. Como o SQL Server segue o algoritmo de **Write-Ahead Logging (WAL)**, ele pode reverter o banco de dados para seu estado anterior usando o log de transações.

> [O SQL Server utiliza um algoritmo de Write-Ahead Logging (WAL), que garante que nenhuma modificação de dados seja gravada no disco antes que o registro associado no log seja gravado no disco. Isso mantém as propriedades ACID para uma transação.](https://medium.com/r?url=https%3A%2F%2Flearn.microsoft.com%2Fen-us%2Fsql%2Frelational-databases%2Fsql-server-transaction-log-architecture-and-management-guide%3Fview%3Dsql-server-ver16)

Desde o início da transação, você não pode fazer um `SELECT` na sua tabela devido a um lock gerado pela inicío da transação:
- ![example-query-lock](/assets/images/2025-02-15-transacoes-commit-rollback/example-query-lock.png)

Até que o `COMMIT` ou `ROLLBACK` ocorra, a tabela ficará inacessível. Isso é **ACID**!

Perfeito! Este é um bom exemplo de como funciona e como pode salvar você em algumas situações.

Mas como isso funciona na camada de arquitetura?

### Como as Transações Funcionam na Arquitetura do SQL Server

O SQL Server gerencia transações por meio do **[Transaction Manager](https://medium.com/@lorenzouriel/sql-tuning-sql-server-architecture-b39cf03fc8ac)**, que garante as propriedades **ACID (Atomicidade, Consistência, Isolamento, Durabilidade).**
- ![architecture-layer](/assets/images/2025-02-15-transacoes-commit-rollback/architecture-layer.png)

Aqui está como funciona internamente:
- O SQL Server grava todas as mudanças no log de transações (arquivo `LDF`) antes de fazer modificações no banco de dados real (arquivo `MDF`).
- Se uma transação for confirmada (`COMMIT`), as mudanças são gravadas permanentemente no disco.
- Se uma transação falhar, o SQL Server usa o log para reverter as mudanças (via `ROLLBACK`), garantindo que o banco de dados volte ao seu estado anterior.
- Durante o andamento da transação, o SQL Server aplica locks nas tabelas para evitar inconsistências de dados. Os níveis de isolamento controlam como as transações interagem entre si, garantindo que elas não interfiram umas nas outras.

--

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)