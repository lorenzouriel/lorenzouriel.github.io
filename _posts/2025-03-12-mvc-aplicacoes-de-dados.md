---
title: "MVC Pode Ajudar Você a Criar Melhores Aplicações de Dados"
date: 2025-03-12 00:00:00:0000
categories: [Git & GitHub]
tags: [git & github]
---

Aprender padrões de design de software pode ajudar você a criar soluções melhores.

Sou um grande fã de padrões de design de software. Comecei a estudá-los quando percebi que a qualidade do código se eleva quando você incorpora esse tipo de modelo. O código se torna mais legível, simples e colaborativo.

No entanto, há algo importante a notar: esses padrões podem ser aplicados em diversas áreas, incluindo aplicações de dados.

***Você pode trabalhar com dados e gostar de programar.***

MVC é um desses padrões, e tenho muitos exemplos de como ele pode ajudar você a criar código mais limpo. No entanto, o melhor exemplo, na minha opinião, é sua aplicação em aplicações de dados.

Neste artigo, construiremos uma aplicação de dados usando o banco de dados `tracking_habits` que criei anteriormente. O objetivo principal é entender como o código é estruturado dentro do padrão MVC e por que essa separação é benéfica.

Mas, o que é MVC?

## MVC (Model-View-Controller)
MVC é um padrão de design que separa uma aplicação em três componentes principais, cada um responsável por uma parte distinta do processo:
- **Model**: Representa os dados da aplicação, lida com interações no banco de dados e encapsula a lógica e regras de negócio.
- **View**: Exibe os dados para o usuário e gerencia os elementos da interface, garantindo que a informação seja apresentada de forma clara.
- **Controller**: Atua como intermediário entre o Model e a View. Ele processa entradas do usuário, atualiza o Model conforme necessário e atualiza a View para refletir as mudanças.

Em essência, os papéis podem ser resumidos como:
- **Model**: Os dados e a lógica de backend.
- **View**: A interface frontend, com a qual o usuário interage.
- **Controller**: A lógica que responde às ações do usuário e atualiza tanto os dados quanto a interface.

Simples, certo?

Agora, vamos construir nossa aplicação de dados.

## MVC com Streamlit

Primeiramente, precisamos entender a estrutura de pastas. Cada componente precisa estar organizado em sua respectiva pasta:
```sh
src/
├── models/
│   ├── database.py
├── views/
│   ├── ui.py
├── controllers/
│   ├── app_controller.py
├── main.py
```

O `main.py` será responsável por inicializar nosso `app_controller.py`. Isso encapsula a lógica do controlador apenas no `app_controller.py`. Eu criei o `main.py` para servir como ponto de entrada da nossa aplicação.

### Models > `database.py`
O Model gerencia as operações do banco de dados, incluindo a recuperação e atualização de dados.
```python
import pyodbc
import pandas as pd
import dotenv
import os

dotenv.load_dotenv()

CONN_STR = os.getenv('CONN_STR')

def get_contacts():
    """buscar todos os contatos do banco de dados"""
    conn = pyodbc.connect(CONN_STR)
    query = "SELECT * FROM contacts"
    df = pd.read_sql(query, conn)
    conn.close()
    return df

def get_goals():
    """buscar todas as metas do banco de dados"""
    conn = pyodbc.connect(CONN_STR)
    query = "SELECT * FROM goals"
    df = pd.read_sql(query, conn)
    conn.close()
    return df
```

Neste exemplo, criei apenas duas funções para recuperar contatos e metas.

No entanto, você pode criar métodos mais específicos e diferentes consultas para o banco de dados.

Outra opção é dividir ainda mais a lógica:
```sh
src/
├── models/
│   ├── contacts.py
│   ├── goals.py
```

Isso é recomendado se seu código ficar muito grande.

### Views > `ui.py`
A View gerencia a apresentação e interação do usuário.
```python
import streamlit as st
import matplotlib.pyplot as plt
import seaborn as sns

def show_contacts(df):
    """exibir contatos na UI"""
    st.write(" ## Contacts ")
    st.dataframe(df)

def show_goals(df):
    """exibir metas na UI"""
    st.write(" ## Goals ")
    st.dataframe(df)
```

### Controller > `app_controller.py`
O Controller coordena a interação entre o Model e a View.
```python
import models.database as model
import views.ui as view
import streamlit as st

def main():
    """lógica principal da aplicação"""
    st.sidebar.title("Navigation")
    option = st.sidebar.radio("Go to", ["Contacts", "Goals"])

    if option == "Contacts":
        df = model.get_contacts()
        view.show_contacts(df)
    elif option == "Goals":
        df = model.get_goals()
        view.show_goals(df)
```

Depois de entender tudo isso, você pode simplesmente rodar: `streamlit run main.py`

## Benefícios
**Escalabilidade** é o maior benefício aqui. Ao adotar MVC, é fácil expandir e modificar cada camada independentemente. Em vez de tentar depurar um código com 2000 linhas, você pode rapidamente seguir erros direto no componente responsável.

Outro grande benefício é a **colaboração**. Equipes de backend e frontend podem trabalhar separadamente, cada uma focada em sua camada sem interferir na outra.

Isso também leva à **reutilização**, permitindo que você reutilize seu código em diferentes projetos.

## Mais Exemplos de MVC em Dados
Eu sei, nem todo mundo cria códigos como este. Às vezes, trabalhamos em relatórios, APIs e ETLs em vez de aplicativos tradicionais.

Mas adivinhe?

O conceito de MVC pode ser aplicado a eles!

### 1. Relatórios 
- **Modelo**: Busca e estruturação de dados de um banco de dados ou API.
- **Visualização**: Exibição de relatórios em gráficos, tabelas e painéis.
- **Controlador**: Manipulação de interações do usuário, como filtragem, exportação e agendamento de relatórios.

### 2. ETL (Extração, Transformação, Carregamento)
- **Modelo**: Manipula extração de dados, lógica de transformação e armazenamento.
- **Visualização**: Fornece atualizações de status, logs e visualizações de dados.
- **Controlador**: Gerencia a execução do fluxo de trabalho, tratamento de erros e entradas do usuário

### 3. APIs
- **Modelo**: Define o esquema do banco de dados e as regras de negócios.
- **View**: Atua como respostas de API, formatando saída JSON ou XML.
- **Controller**: Gerencia solicitações de API, autenticação e tratamento de respostas.

É aquela frase famosa: o conceito existe e você já o aplicou, só não sabia ainda.

## Explicação do Fluxo:
1. Interação do usuário com a interface do Streamlit.
2. A View (`views/ui.py`) exibe dados e gráficos.
3. O Controller (`controllers/app_controller.py`) gerencia a lógica, capturando ações do usuário e solicitando dados do Model.
4. O Model (`models/database.py`) recupera informações do SQL Server.
5. Os dados são enviados de volta para o Controller, que é passado para a View, atualizando a interface do usuário.

--

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)