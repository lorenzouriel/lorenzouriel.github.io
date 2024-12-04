---
title: "Apaixonado pelo uv: O Melhor Gerenciador de Projetos Python"
date: 2024-12-03 00:00:00:0000
categories: [Python]
tags: [python]
---

Esqueça tudo o que ensino [neste artigo!](https://lorenzouriel.github.io/posts/iniciando-um-projeto-python/)

O que estou prestes a apresentar a você é uma virada de jogo para o seu projeto: [`uv`.](https://www.youtube.com/watch?v=gwizAUNvUD0)

## Por que você Deveria Usar `uv`?
Já no início, a documentação deixa claro porque o `uv` se destaca entre outras ferramentas. Para mim os principais destaques são:
- `uv` combina as funcionalidades de `pip`, `poetry`, `pyenv`, `virtualenv` e muito mais, em uma única ferramenta.
- É 10 a 100 vezes mais rápido que o `pip`, reduzindo o tempo gasto no gerenciamento de dependências.

Aqui está um gráfico de desempenho da documentação `uv` que demonstra sua vantagem de velocidade:

![uv-performance](/assets/images/2024-12-03-apaixone-se-pelo-uv/uv-performance.svg)

E como utilizar/iniciar?

### Instalação
Você pode instalar o`uv`de várias maneiras:

- Linux/MacOs

```bash
curl -LsSf https://astral.sh/uv/install.sh | eh
```

- Windows

```bash
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

- Usando pip

```bash
pip install uv
```

### Inicialização do Projeto
Para criar um novo projeto, execute:

```bash
uv init
```

Este comando cria 04 arquivos:
- `.python-version:` Especifica a versão Python do projeto.
- `hello.py:` Um script Python inicial simples.
- `pyproject.toml:` configura dependências do projeto, build settings e metadados.
- `README.md:` Um modelo para documentar o propósito e uso do seu projeto.

### Ambiente Virtual
Para configurar um ambiente virtual, execute:

```bash
uv venv
```

Ative o venv:

```bash
.venv\Scripts\activate
```

### Adicionando Dependências
Para adicionar dependências, basta usar:

```bash
uv add typer
```

- Ao adicionar uma dependência, é criado automaticamente um arquivo chamado `uv.lock`. O arquivo `uv.lock` é usado para registrar informações sobre dependências do projeto e suas **versões exatas.**

Se você quiser separar dependências por ambiente, use a flag:

```bash
uv add pytest --dev
```

Você pode adicionar várias bibliotecas de uma só vez:

```bash
uv add fastapi jupyter pandas
```

Você pode remover dependências usando:

```bash
uv remove fastapi
```

### Rodar
Exemplo de execução de testes com pytest:

```bash
uv run pytest
```

Exemplo de execução do hello.py:

```bash
C:\portfolio\uv-article>uv run .\hello.py      
Hello from uv-article!
```

### Gerenciar versões do Python
Para verificar a versão atual do Python em seu ambiente:

```bash
uv execute python --version
```

Instale uma versão específica do Python ou várias versões:

```bash
uv python install 3.12.3

uv python install 3.9 3.10 3.11
```

Recrie seu venv e instale a versão python:

```bash
uv venv --python 3.12.3
```

Você também pode usar a `sync` para recriar seu ambiente virtual também:

```bash
uv sync
```

- Este comando instala a versão especificada do Python e atualiza suas dependências conforme necessário.
- Você pode simplesmente alterar a versão do python em `.python-version` e executar o `uv sync` para recriar o `.venv`


### Formatação de código com Ruff
Para verificar a formatação do código com ruff, execute:

```bash
uv run ruff check
```

To format your code with ruff, run:

```bash
uv run ruff format
```

- Ruff é um linter python.

Você pode fazer muito mais com `uv`, eu escrevi alguns comandos básicos e principais.

Como criei um artigo sobre boas práticas, não havia como não trazer um sobre `uv`. Honestamente, `"uv is all you need all you need is uv"`.

Se você quiser saber mais sobre o `uv` e seus comandos, consulte a [documentação original.](https://medium.com/r?url=https%3A%2F%2Fdocs.astral.sh%2Fuv%2Freference%2Fcli%2F).

--

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)
