---
title: "Iniciando um Projeto Python"
date: 2024-11-20 00:00:00:0000
categories: [Python]
tags: [python]
---

Este guia documenta as etapas que executo para estabelecer a base de qualquer projeto Python, com foco na configuração do ambiente, controle de versão e estruturação do projeto.

## 1. Repositório Git e GitHub

Se o seu projeto não tiver um repositório local e um remoto, você gosta de arriscar, Git e GitHub são essenciais para rastrear alterações e salvar o seu código.

### Passos:
- Crie um repositório local no diretório do seu projeto:

```bash
git init
```

- Crie um repositório remoto no GitHub.

- Adicione o repositório remoto ao local e envie seu primeiro commit:

```bash
echo "# python-requests-site" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/lorenzouriel/python-requests-site.git
git push -u origin main
```

Um projeto sem controle de versão pode estar em risco se algo der errado com seu código.

## 2. Ambiente Virtual
Usar um ambiente virtual garante que as dependências do seu projeto não interfiram em outros projetos Python na sua máquina. Ele isola seu ambiente e mantém tudo limpo.

### Passos:
- Crie um ambiente virtual:

```bash
python -m venv venv
```

- Ative o ambiente virtual:

```bash
# Windows
.\venv\Scripts\activate

# macOS/Linux
source .venv/bin/activate
```

Uma vez ativado, todos os pacotes Python que você instalar irão para a pasta .venv e não afetarão os outros projetos.

## 3. Use o Pyenv
Pyenv permite gerenciar várias versões do Python em seu sistema.

É útil se você precisar testar seu projeto com diferentes versões do Python ou se quiser manter as versões do Python para seus projetos isoladas.

### Passos:
- Instale o Pyenv usando o [guia de instalação.](https://github.com/pyenv/pyenv)
- Para iniciar, instale uma versão específica do Python:

```bash
pyenv install 3.12.0
```

- Defina a versão local do Python para o seu projeto:

```bash
pyenv local 3.12.0
```

Um arquivo chamado `.python-version` será criado na árvore do seu projeto.

Isso garante que seu projeto usará a versão especificada do Python, independentemente da versão global do sistema.

## 4. Use o Poetry
Poetry é um gerenciador de dependências e uma ferramenta para construção e inicialização de projetos Python.

Ele simplifica o gerenciamento o empacotamento de dependências, facilitando a manutenção e o compartilhamento.

### Passos:

- Instale o Poetry usando o [guia de instalação.](https://python-poetry.org/docs/)
- Depois de instalado, inicialize o Poetry em seu projeto:

```bash
poetry init
```

- Poetry irá criar um arquivo chamado `pyproject.toml`, onde suas dependências serão listadas.

- Para instalar as dependências, rode:

```bash
poetry install
```

- Poetry também permite criar um ambiente virtual e definir a versão python:

```bash
poetry shell  # Crie e acesse o virtual env
poetry env use 3.12.0  # Setar a sua versão do Python
```

Aqui está um exemplo do arquivo `pyproject.toml` com a lib `requests` adicionada:

```bash
[tool.poetry]
name = "python-requests-site"
version = "0.1.0"
description = ""
authors = ["Lorenzo Uriel <your-email@gmail.com>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.12"
requests = "^2.32.3"


[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

## 5. Crie um `.gitignore` e um `.env`

`.gitignore`: Este arquivo informa ao Git quais arquivos e diretórios não devem ser rastreados pelo controle de versão. Isso ajuda a evitar que arquivos confidenciais ou desnecessários sejam enviados para o GitHub.

`.env`: Este arquivo contém variáveis ​​de ambiente, como chaves de API, URLs de banco de dados e outros valores de configuração. É importante manter este arquivo privado, por isso ele nunca deve ser enviado ao repositório.

### Passos: 

- No caso do `.gitignore` eu sempre utilizo o modelo disponibilizado pela toptal, [você pode conferir aqui.](https://www.toptal.com/developers/gitignore/api/python)

- Basta criar os arquivos e adicionar os valores:

```bash
# Windows
New-Item .gitignore
New-Item .env


# macOS/Linux
touch .gitignore
touch .env
```

Este é um ótimo ponto de partida para criar um projeto Python do zero, eu documentei exatamente a maneira que sempre faço. 

Existem várias outras abordagens que você pode adotar para garantir que o projeto seja mantido de acordo com as melhores práticas, pretendo me aprofundar no próximo artigo dessa série.

--

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)
