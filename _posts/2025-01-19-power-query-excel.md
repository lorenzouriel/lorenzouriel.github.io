---
title: "Power Query no Excel"
date: 2025-01-19 00:00:00:0000
categories: [Excel]
tags: [excel]
---

Saber escolher a melhor ferramenta não é fácil. Existem diversas opções no mercado e, com elas, inúmeras dúvidas.

Será que essa ferramenta vai atender às minhas necessidades?

Enquanto você reflete sobre isso, o Excel continua sendo utilizado na sua empresa. Então, por que, em vez de perder tempo quebrando a cabeça com a melhor solução para o seu negócio, você não tenta aprimorar o que já tem?

É exatamente isso que pretendo abordar aqui. Quero compartilhar algumas dicas sobre como melhorar o uso do Excel na sua empresa. Chega de relatórios manuais mensais, semanais ou até diários.

## Power Query
O Power Query é uma ferramenta amplamente conhecida por aqueles que utilizam o Power BI. Ele permite conectar, combinar e refinar dados de diversas fontes de maneira prática e eficiente. Se você pensa que o Excel se limita a tabelas e fórmulas básicas, o Power Query pode revolucionar a forma como você trabalha.

Com o Power Query, é possível automatizar a importação de dados de arquivos, bancos de dados e até mesmo da web, aplicando transformações automaticamente sempre que novos dados forem adicionados.

A funcionalidade já está disponível nativamente na versão do Excel 2016 ou posterior. Se você utiliza uma versão mais antiga precisará adicionar como um Add-in, [deixarei este link como referência para ajudá-lo.](https://www.youtube.com/watch?v=PJeCFFCNDnU)

## Primeiros Passos com o Power Query
Nosso objetivo será criar um relatório de metas armazenadas no banco de dados. Para este exemplo, utilizaremos o SQL Server.

O primeiro passo é acessar a aba **Data** (**Dados**) e clicar em **Get Data** (**Obter Dados**).

![get-data](/assets/images/2025-01-19-power-query-excel/get-data.png)

Você encontrará diversas opções de conexão. Ao selecionar **From Database** (**Banco de Dados)**, verá todas as conexões com os SGBDs que o Excel disponibiliza para uso.

Depois de escolher a opção desejada, uma janela será exibida para configurar as informações de conexão. Nessa tela, você precisará fornecer:
- **Server** (**Servidor**)
- **Database** (**Banco de dados**)
- Em **Advanced options** (**Opções Avançadas**), é possível personalizar o tempo limite (timeout) da sua query e adicionar a própria query, caso prefira trazer as informações já processadas do SQL (o que eu recomendo).

No meu exemplo, vou abordar apenas a conexão básica com o servidor e o banco de dados:

![conn-strings](/assets/images/2025-01-19-power-query-excel/conn-strings.png)

Após conectar, ele vai trazer todas as opções de tabelas disponíveis:

![tables](/assets/images/2025-01-19-power-query-excel/tables.png)

Você pode optar por carregar a tabela diretamente clicando em **Load** (**Carregar**) ou iniciar a transformação dos dados clicando em **Transform Data** (**Transformar Dados**).

Ao selecionar **Transform Data**, o Power Query Editor será aberto. Para quem já utiliza o Power BI, essa interface será bastante familiar:

![power-query-editor](/assets/images/2025-01-19-power-query-excel/power-query-editor.png)

No Power Query Editor, você pode transformar colunas e campos, criar campos calculados e realizar diversos tipos de transformações nos dados. 

Todas as etapas de transformação realizadas são registradas no painel à direita da tela, permitindo que você acompanhe e edite cada modificação de forma simples e organizada:

![power-query-steps](/assets/images/2025-01-19-power-query-excel/power-query-steps.png)

Após finalizar todas as transformações, você pode clicar no canto superior esquerdo em **Close & Load** (**Fechar & Carregar**). Isso irá carregar os dados transformados de volta para o Excel, onde você poderá utilizá-los em seu relatório ou análise.

![worksheet](/assets/images/2025-01-19-power-query-excel/worksheet.png)

Automaticamente, os dados serão carregados em uma nova planilha, que será nomeada com o nome da sua consulta, facilitando a organização e identificação das informações:

![close-load](/assets/images/2025-01-19-power-query-excel/close-load.png)

Aqui, você pode editar e personalizar os dados como uma tabela, adicionar campos calculados e aplicar condições.

Agora, a melhor parte: para atualizar os dados, você não precisa repetir todos esses passos. Basta acessar **Data** (Dados) -> **Queries & Connections** (Consultas e Conexões):

![how-to-update](/assets/images/2025-01-19-power-query-excel/how-to-update.png)

A tela à direita será exibida. Para atualizar os dados, basta clicar no ícone de atualização, que aparece no canto da tela:

![update](/assets/images/2025-01-19-power-query-excel/update.png)

---

Caso queira explorar a mesma base de dados utilizada nos exemplos, recomendo conferir o artigo que escrevi sobre modelagem de dados. Lá, você [encontrará mais informações e o acesso ao modelo utilizado.](https://lorenzouriel.github.io/posts/data-modelling/)

--

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)
