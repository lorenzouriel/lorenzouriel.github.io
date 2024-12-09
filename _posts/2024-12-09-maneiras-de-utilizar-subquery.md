---
title: "Maneiras de Utilizar uma Subquery"
date: 2024-12-09 00:00:00:0000
categories: [SQL]
tags: [sql]
---


Existem diversas maneiras de se utilizar uma subquery, algumas são eficientes, outras nem tanto, pode depender muito da sua necessidade. Porém, essa não vai ser a discussão desse artigo, vamos focar em entender as diferentes maneiras de se utilizar um subquery.

As subqueries podem ser usadas dentro de cláusulas como `SELECT`, `FROM`, `WHERE`, e `HAVING`.

## 1. Subquery no `SELECT`

Uma subquery pode ser usada em uma cláusula `SELECT` para calcular valores adicionais com base em outra consulta. Geralmente, é usada para calcular uma coluna derivada.

**Exemplo:**

```sql
SELECT 
	[name],
	(SELECT COUNT(*) FROM [dbo].[goals] WHERE [contact_id] = [id]) [total_goals]
FROM [dbo].[contacts]
```

Aqui, a subquery calcula o total de metas de cada contato. Poderiamos realizar via JOIN e adicionar um `COUNT` direto no campo, se não existisse essa opção de Subquery.


## 2. Subquery no `WHERE` / `IN`

Subqueries podem ser usadas na cláusula `WHERE` junto com o `IN` para filtrar resultados com base em condições. Isso é útil quando você deseja filtrar registros com base em um conjunto de dados calculado por outra consulta.

**Exemplo:**

```sql
SELECT 
	*
FROM [dbo].[habits]
WHERE [id] IN (SELECT [habit_id] FROM [dbo].[tracking] WHERE [date] > '2024-01-06')
```

Nesse caso, a subquery retorna todos os hábitos que já foram praticados a partir de um período incial. Eu filtro os resultados baseado no valor de retorno da minha outra subquery.

A subquery também pode ser usada com a cláusula `NOT IN` para excluir registros com base em um conjunto de valores retornados pela subquery.

**Exemplo:**

```sql
SELECT 
	*
FROM [dbo].[habits]
WHERE [id] NOT IN (SELECT [habit_id] FROM [dbo].[tracking] WHERE [date] > '2024-01-06')
```

## 3. Subquery no `FROM`

Subqueries podem ser usadas na cláusula `FROM` para criar uma tabela temporária ou uma `inline view`. Isso permite realizar operações em um conjunto de dados calculado.

**Exemplo:**

```sql
SELECT 
	COUNT(habit_id) [habits],
	[date]
FROM (SELECT * FROM [dbo].[tracking] WHERE [level] > 0) AS [perfomance_habit]
GROUP BY [date]
```

É uma outra maneira de se utilizar, nesse exemplo eu consulto a tabela de `tracking` onde o desempenho foi maior que 0 e chamo a tabela de `performance_habit`.

## 4. Subquery com `EXISTS`

A subquery pode ser usada com a cláusula `EXISTS` para verificar se uma condição existe. O `EXISTS` retorna `TRUE` se a subquery retornar pelo menos um registro.

**Exemplo:**

```sql
SELECT 
	[name]
FROM [dbo].[contacts] c
WHERE EXISTS (SELECT 1 FROM [dbo].[habits] t WHERE t.[contact_id] = c.[id] AND t.[name] = 'Running')
```

Neste exemplo, a consulta externa retorna os nomes dos contatos cujos hábitos estão relacionados a corrida.

## 5. Subquery no `HAVING`

Você pode usar uma subquery dentro da cláusula `HAVING` para filtrar grupos após a agregação.

**Exemplo:**

```sql
SELECT 
	COUNT(habit_id) AS [habits],
	[date]
FROM (SELECT * FROM [dbo].[tracking] WHERE [level] > 0) AS [perfomance_habit]
GROUP BY [date]
HAVING 
    COUNT(habit_id) > 
	(SELECT AVG(habit_count) 
	 FROM (SELECT COUNT(habit_id) AS habit_count, [date]
	       FROM [dbo].[tracking]
	       WHERE [level] > 0
	       GROUP BY [date]) AS [average_habit_tracking])
```

Esse exemplo retornará as datas em que o número de hábitos rastreados foi maior que a média de todos os dias.

## Tipos de Subqueries:

**Correlacionada:** Neste caso, a subquery depende de uma coluna da consulta principal. Ela é executada para cada linha da consulta externa.

```sql
SELECT 
    [name],
    (SELECT COUNT(*) FROM [dbo].[goals] WHERE [contact_id] = [contacts].[id]) AS [total_goals]
FROM [dbo].[contacts]
```

**Não Correlacionada:** Neste caso, a subquery não depende de nenhuma coluna da consulta principal e é executada apenas uma vez. 


```sql
SELECT 
    [name],
    [email]
FROM [dbo].[contacts]
WHERE [id] IN (SELECT [contact_id] FROM [dbo].[goals] WHERE [achieved] = 1)
```

Se você conhece outras maneiras interessantes de utilizar subqueries, fique à vontade para compartilhar!

Caso queira explorar a mesma base de dados utilizada nos exemplos, recomendo conferir o artigo que escrevi sobre modelagem de dados. Lá, você [encontrará mais informações e o acesso ao modelo utilizado.](https://lorenzouriel.github.io/posts/data-modelling/)

--

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)
