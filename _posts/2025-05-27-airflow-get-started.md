---
title: "AirFlow: Primeiros Passos"
date: 2025-05-27 00:00:00:0000
categories: [AirFlow]
tags: [airflow]
---

Uma das coisas que fiz este ano foi estudar sobre AirFlow. Estou mais focado em entender como tecnologias Open-Source podem ajudar nas atividades do dia a dia da minha empresa.

Então, este artigo é uma reflexo de todas as minhas anotações sobre AirFlow. No próximo, pretendo criar um projeto prático usando Astro.

Vamos começar!

## Apache Airflow
Apache Airflow é uma plataforma open source projetada para criar, agendar e monitorar workflows de forma programática.

Ele foi desenvolvido pela Airbnb (amo vocês) em 2014 e posteriormente doado para a Apache Software Foundation, o Airflow se tornou um exemplo a ser seguido na orquestração de pipelines de dados.

As principais vantagens para mim são:

- **Workflow como Código**: Workflows versionáveis, testáveis e colaborativos  
- **UI**: Visualização clara dos pipelines e monitoramento em tempo real  
- **Comunidade**: Grande ecossistema de operadores e integrações. Você também pode criar os seus próprios.

Airflow é particularmente útil para **Data Engineering**, na automação de pipelines ETL/ELT. Mas você pode utilizá-lo com **DevOps** para agendar tarefas de infraestrutura ou com **Data Science** para orquestrar o treinamento de modelos de ML.

## Core Architectural Components
Apache Airflow segue uma arquitetura modular e distribuída. Ele possui componentes principais que o tornam o que é, cada um com sua responsabilidade única.

![architecture](/assets/images/2025-05-27-airflow-get-started/architecture.png)

### AirFlow Docs

### 1. Metadata Database
Armazena todos os estados e metadados do Airflow. Guarda definições de DAG, estados das tarefas, variáveis, conexões e dados XCom.

O metadata database pode ser configurado para usar bancos relacionais populares como PostgreSQL ou MySQL.

**Principais funcionalidades:**
- Rastreia DAGs, tarefas, seus relacionamentos e histórico de execução  
- Mantém informações de usuários, variáveis e conexões  
- Armazena dados XCom (comunicação entre tarefas)

### 2. Scheduler
Determina quando e como as tarefas devem ser executadas, pense em cronjobs aqui. Com base na programação definida para seu DAG, o scheduler é responsável por disparar as tarefas no momento certo.

**Principais funções:**
- Faz o parsing dos arquivos DAG e os armazena no banco de dados  
- Determina dependências e prontidão das tarefas  
- Enfileira tarefas para execução  
- Lida com falhas e tentativas

### 3. Executor
O executor determina como e onde as tarefas são executadas. Ele gerencia a execução das tarefas, é como o “cérebro” da execução. Está mais na camada de configuração, decidindo sobre a estratégia de distribuição das tarefas.

**Tipos de Executor:**
- **SequentialExecutor** → Uma tarefa por vez  
- **LocalExecutor** → Tarefas em paralelo em uma máquina  
- **CeleryExecutor** → Workers escaláveis horizontalmente  
- **KubernetesExecutor** → Autoescalonamento com pods Kubernetes

### 4. Workers
Executam de fato as tarefas atribuídas pelo scheduler, são o “músculo” da execução. Atuam na camada de implementação, executando fisicamente o código das tarefas.

### 5. Webserver
Fornece a UI/API para monitorar e gerenciar DAGs. Pode ser balanceado para lidar com alto tráfego.

**Funcionalidades:**
- Visualização e gerenciamento de DAGs  
- Inspeção de tarefas  
- Visualização de logs  
- Gerenciamento de variáveis e conexões  
- Controle de acesso de usuários

## Conceitos Principais

Acredito que você já ouviu a palavra **DAG** em alguma conversa, é mais simples do que parece. Isso não é o que vai te dar dor de cabeça quando começar a usar.

![dags](/assets/images/2025-05-27-airflow-get-started/dag.png)

### DAGs (Directed Acyclic Graphs)
Um DAG é o conceito central do Airflow, coletando **Tasks** organizadas com dependências que definem a ordem de execução.

O principal objetivo dos DAGs é definir um conjunto de tarefas e seus relacionamentos. Você pode especificar a ordem de execução e dependências.

Uma funcionalidade útil é determinar a frequência de execução, como você faz com cronjobs.

### Tasks
Tarefas são as unidades básicas de trabalho em um DAG. Existem três tipos principais:

#### 1. Operators
Operadores são templates de tarefas pré-definidos para executar operações específicas. O Airflow oferece muitos operadores para casos comuns.

- **Core** — `BashOperator`, `PythonOperator`: Executam comandos shell ou funções Python  
- **Data Transfer** — `S3ToRedshiftOperator`, `SFTPOperator`: Movem dados entre sistemas  
- **Database** — `PostgresOperator`, `BigQueryExecuteQueryOperator`: Executam queries SQL  
- **Container** — `KubernetesPodOperator`, `DockerOperator`: Executam tarefas containerizadas

#### 2. Sensors
Sensores são operadores especiais que aguardam até uma condição ser atendida antes de continuar.

- `FileSensor`: Espera um arquivo aparecer  
- `HttpSensor`: Verifica se um endpoint web está disponível  
- `ExternalTaskSensor`: Espera uma tarefa de outro DAG  
- `DateTimeSensor`: Espera até um horário específico

#### 3. TaskFlow API
Permite escrever tarefas como funções Python decoradas, facilitando o gerenciamento de dependências.

- Manipulação automática de XCom para passar dados  
- Sintaxe limpa de dependência com `>>` e `<<`  
- Suporte a o decorador `@task`

## Ciclo de Vida das Tasks
- **Scheduled** → Agendado pelo scheduler  
- **Queued** → Aguardando worker  
- **Running** → Em execução  
- **Success/Failed** → Estado final  

## Criando e Gerenciando DAGs

No Airflow, existem três formas principais de declarar uma DAG. Cada abordagem tem seu propósito e se encaixa em diferentes estilos de codificação e níveis de complexidade do fluxo de trabalho.

### 1. Usando um **Context Manager**

Essa é a forma mais comum e direta. Ao usar um gerenciador de contexto (`with`), todas as tasks declaradas dentro dele são automaticamente associadas à DAG.

```python
import datetime
from airflow import DAG
from airflow.operators.empty import EmptyOperator

with DAG(
    dag_id="exemplo_com_contexto",
    start_date=datetime.datetime(2021, 1, 1),
    schedule="@daily",
    catchup=False
):
    EmptyOperator(task_id="tarefa_exemplo")
```

### 2. Usando o **Construtor Padrão (Standard Constructor)**

Você pode criar a DAG separadamente e depois passá-la explicitamente para cada operador.

```python
import datetime
from airflow import DAG
from airflow.operators.empty import EmptyOperator

dag = DAG(
    dag_id="exemplo_com_construtor",
    start_date=datetime.datetime(2024, 3, 23),
    schedule="@daily",
    catchup=False
)

EmptyOperator(task_id="tarefa_exemplo", dag=dag)
```

Dá mais controle sobre onde e como a DAG é utilizada, útil para projetos com múltiplos arquivos ou DAGs reutilizáveis.

### 3. Usando o **Decorador `@dag`**

O decorador `@dag` transforma uma função Python em um gerador de DAGs. É especialmente útil para workflows baseados em TaskFlow (usando `@task`).

```python
import datetime
from airflow.decorators import dag, task

@dag(
    dag_id="exemplo_com_decorator",
    start_date=datetime.datetime(2024, 1, 1),
    schedule="@daily",
    catchup=False
)
def fluxo_de_tarefas():
    @task
    def primeira_tarefa():
        return "Olá, mundo!"

    @task
    def segunda_tarefa(texto):
        print(texto)

    segunda_tarefa(primeira_tarefa())

fluxo_de_tarefas()
```

Ideal para fluxos mais dinâmicos, reutilizáveis e orientados a funções. Facilita testes unitários e modularização.

## Paramêtros Importantes na DAG
* `dag_id`: Identificador único >>> `"daily_processing"`
* `start_date`: Data de início >>> `datetime(2024, 1, 1)`
* `schedule`: Frequência de execução >>> `"@daily"`, `"0 0 * * *"`
* `catchup`: Se deve executar execuções passadas >>> `True` / `False`
* `tags`: Categorias de organização >>> `["etl", "reports"]`
* `default_args`: Argumentos padrão das tarefas >>> `{'retries': 3}`

## Agendamento

DAGs sempre serão executadas de duas formas:
* Quando disparadas manualmente ou via API
* Quando definidas via `schedule`, que é parte essencial de um DAG

### Exemplos de expressão de cron:

* `@once`: Executa o DAG uma única vez, na criação
* `"* * * * *"`: A cada minuto
* `@hourly`: A cada hora
* `@daily`: Uma vez por dia
* `@weekly`: Uma vez por semana
* `@monthly`: Uma vez por mês
* `@yearly`: Uma vez por ano
* `None`: Apenas execução manual

**Exemplos práticos:**

* `"*/10 * * * *"` → A cada 10 minutos
* `"0 8 * * 1–5"` → 8h de segunda a sexta

Claro! Aqui está o texto reescrito com melhorias de clareza, gramática e estilo, mantendo a linguagem acessível e focada em exemplos práticos:

## Gerenciando Dependências entre Tasks

Em uma DAG do Airflow, as tasks raramente funcionam isoladamente. Normalmente, uma task depende da execução de outras tasks anteriores (upstream) e, por sua vez, outras tasks dependem dela (downstream).

Definir essas dependências é essencial para construir a estrutura do grafo acíclico direcionado (DAG – *Directed Acyclic Graph*).

### Usando os Operadores `>>` e `<<`

A forma mais comum e legível de definir dependências entre tasks é utilizando os operadores `>>` (executa antes) e `<<` (executa depois):

```python
task1 >> task2  # task1 deve ser executada antes de task2
task3 << task2  # task2 deve ser executada antes de task3

# task1 executa antes de task2 e task3, que por sua vez executam antes de task4
task1 >> [task2, task3] >> task4
```

### Usando os Métodos `set_upstream` e `set_downstream`

Se preferir uma abordagem mais explícita ou programática, você pode usar os métodos `set_upstream()` e `set_downstream()`.

```python
task1.set_downstream(task2)  # task1 -> task2
task2.set_upstream(task1)    # equivalente à linha acima
```

### Utilizando `cross_downstream` para Múltiplas Dependências

Quando você deseja que todas as tasks de uma lista dependam de todas as tasks de outra lista, use o utilitário `cross_downstream`.

```python
from airflow.models.baseoperator import cross_downstream

# Equivalente a:
# op1 >> op3
# op1 >> op4
# op2 >> op3
# op2 >> op4
cross_downstream([op1, op2], [op3, op4])
```

### Utilizando `chain` para Sequências de Tasks

Para criar sequências de tasks de forma simples e elegante, o Airflow oferece o atalho `chain`.

```python
from airflow.models.baseoperator import chain

# Executa op1 >> op2 >> op3 >> op4
chain(op1, op2, op3, op4)

# Executa múltiplos caminhos paralelos:
# op1 >> op2 >> op4 >> op6
# op1 >> op3 >> op5 >> op6
chain(op1, [op2, op3], [op4, op5], op6)
```

Esses utilitários facilitam a definição de DAGs complexas, mantendo o código limpo, legível e fácil de manter.

## Exemplo Simples

```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from datetime import datetime

with DAG(
    "simple_example",
    start_date=datetime(2024, 1, 1),
    schedule="@daily",
) as dag:

    task1 = BashOperator(
        task_id="print_date",
        bash_command="date"
    )

    task2 = BashOperator(
        task_id="echo_hello",
        bash_command="echo 'Hello Airflow!'"
    )

    task1 >> task2
```

**Saída esperada:**
```
[print_date] 2024-06-20  
[echo_hello] Hello Airflow!
```

**O que isso faz:**

* **DAG**: Executa diariamente a partir de 1 de janeiro de 2024
* **Tarefa 1**: Mostra a data atual
* **Tarefa 2**: Mostra um Olá, Mundo!
* **Dependência**: task1 precisa finalizar antes da task2 iniciar

**Como usar:**
* Salve como `simple_dag.py` na sua pasta `dags/`
* O Airflow detectará automaticamente
* Dispare manualmente via UI ou aguarde a execução programada

Executaremos esse exemplo no próximo tutorial, onde vou falar de **Astro CLI** para gerenciamento do Airflow.

---

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)