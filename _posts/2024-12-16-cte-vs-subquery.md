---
title: "CTEs vs. Subqueries"
date: 2024-12-16 00:00:00:0000
categories: [SQL]
tags: [sql]
---

Time Iron Man ou Time Cap? É exatamente assim que me sinto quando debatemos o que é melhor: CTEs ou Subqueries.

No início, é sempre a mesma pergunta: *“Se consigo fazer isso com uma Subquery, por que devo usar uma CTE?”*

Ao final deste artigo, espero que você consiga explicar quando escolher uma ou outra abordagem.

Vou listar algumas vantagens que você pode obter ao utilizar uma CTE.

## 1. Leitura e Manutenção da Query
Claro, este é o primeiro e mais impactante ponto: você está escrevendo código apenas para você ou para outros desenvolvedores que poderão trabalhar com ele mais tarde?

Uma **CTE** fornece uma maneira de definir um conjunto de resultados temporário que pode ser consultado diversas vezes na consulta. Isso torna a consulta mais fácil de ler e compreender, especialmente para consultas complexas.

Uma **Subquery** pode ser mais difícil de ler, especialmente quando está incorporada em `WHERE`, `SELECT` ou outras cláusulas. A lógica pode não ser imediatamente óbvia.

### Exemplo:

```sql
-- CTE
WITH active_goals AS (
    SELECT 
        contact_id, 
        COUNT(*) AS total_goals
    FROM goals
    WHERE status = 1
    GROUP BY contact_id
)
SELECT 
    c.name, 
    c.surname, 
    ag.total_goals
FROM contacts c
JOIN active_goals ag ON c.id = ag.contact_id
WHERE ag.total_goals > 2;

-- Subquery
SELECT 
    c.name, 
    c.surname, 
    (
        SELECT COUNT(*) 
        FROM goals g 
        WHERE g.contact_id = c.id AND g.status = 1
    ) AS total_goals
FROM contacts c
WHERE (
        SELECT COUNT(*) 
        FROM goals g 
        WHERE g.contact_id = c.id AND g.status = 1
    ) > 2;
```

Você sabe exatamente o que a consulta significa e o objetivo apenas lendo a query principal, depois você pode manipular da maneira que quiser.

## 2. Reutilização da Query 
Uma **CTE** pode ser referenciado diversas vezes na mesma consulta, o que é útil quando você precisa reutilizar um cálculo ou agregação complexa em diferentes partes da consulta.

Uma **Subquery** só pode ser usada uma vez na consulta, ou seja, se você precisar do mesmo resultado em vários lugares, precisará repetir a Subquery.

### Exemplo:

```sql
-- CTE
WITH active_habits AS (
    SELECT 
        contact_id, 
        COUNT(*) AS habit_count
    FROM habits
    WHERE status = 1
    GROUP BY contact_id
)
SELECT 
    c.name, 
    c.surname
FROM contacts c
JOIN active_habits ah ON c.id = ah.contact_id
WHERE ah.habit_count > 2

UNION

SELECT 
    c.name, 
    c.surname
FROM contacts c
JOIN active_habits ah ON c.id = ah.contact_id
WHERE ah.habit_count < 0;

-- Subquery
SELECT 
    c.name, 
    c.surname
FROM contacts c
WHERE (
        SELECT COUNT(*) 
        FROM habits h 
        WHERE h.contact_id = c.id AND h.status = 1
    ) > 2

UNION

SELECT 
    c.name, 
    c.surname
FROM contacts c
WHERE (
        SELECT COUNT(*) 
        FROM habits h 
        WHERE h.contact_id = c.id AND h.status = 1
    ) < 0;
```

### 3. Query Recursiva
Uma **CTE** recursiva é útil para trabalhar com dados hierárquicos. Permite consultar o conjunto de resultados do **CTE** em sua própria definição.

[*Uma consulta recursiva em SQL é uma consulta que se chama repetidamente até atingir uma condição de encerramento.*](https://medium.com/@ugorjicalebchijindu/explaining-recursive-query-in-sql-38c043fcc740)

As **Subqueries** não foram projetadas para lidar com consultas recursivas.

### Exemplo:

```sql
WITH goal_hierarchy AS (
    -- Select the top level goals
    SELECT id, name, description, contact_id, parent_goal_id, 1 AS level
    FROM goals
    WHERE contact_id = 1 AND parent_goal_id IS NULL

    UNION ALL

    -- Select subgoals and increment the level
    SELECT g.id, g.name, g.description, g.contact_id, g.parent_goal_id, cte.level + 1
    FROM goals g
    JOIN goal_hierarchy cte ON g.parent_goal_id = cte.id
)
-- Get all goals and subgoals
SELECT 
 id, 
 name,
 description, 
 level
FROM goal_hierarchy
ORDER BY level, id;
```

Começamos selecionando metas que não possuem um campo pai (`parent_goal_id IS NULL`). Para cada meta selecionada, juntamos o **CTE** com a tabela de metas para encontrar suas submetas.

A coluna `level` ajuda a rastrear a profundidade de cada meta na hierarquia

## 4. Performance 
Em muitos casos, as **CTEs** podem melhorar o desempenho, especialmente se o mesmo conjunto de resultados for referenciado várias vezes.

Uma **Subquery** geralmente é otimizada pelo mecanismo de banco de dados, mas pode ser menos eficiente se a mesma **Subquery** precisar ser avaliada diversas vezes.

Não creio que isso terá um grande impacto. Performance depende muito da arquitetura do seu banco de dados e de como as tabelas estão relacionadas. 

*Desempenho tem tudo a ver com a compreensão do seu banco de dados.*

## 5. Tratamento de erros
Isso é mais fácil com um **CTE**, sem dúvidas.

Uma **CTE** é declarada separadamente no início da consulta, é mais fácil de depurar. Você também pode isolar uma **CTE** em uma consulta separada para verificar os resultados antes de usá-lo na consulta final.

As **Subqueries** podem ser difíceis, pois estão todas dentro da consulta principal.

## Conclusão
Use **CTEs** para:
- Melhor leitura, manutenção e reutilização.
- Consultas recursivas.
- Trabalhar com lógica complexa em múltiplas partes de uma consulta.

Use **Subqueries** para:
- Consultas simples.
- Se sua consulta não for repetida.

Caso queira explorar a mesma base de dados utilizada nos exemplos, recomendo conferir o artigo que escrevi sobre modelagem de dados. Lá, você [encontrará mais informações e o acesso ao modelo utilizado.](https://lorenzouriel.github.io/posts/data-modelling/)

--

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)
