---
title: "Mantendo um Projeto Python"
date: 2024-12-02 00:00:00:0000
categories: [Python]
tags: [python]
---

Você já iniciou seu projeto Python seguindo as melhores práticas que mencionei no [artigo anterior, certo?](https://lorenzouriel.github.io/posts/iniciando-um-projeto-python/)

Agora é hora de focar na manutenção do seu projeto, isso envolve garantir que o projeto permaneça organizado, funcional, fácil de compartilhar e escalar.

## 1. requirements.txt
O arquivo requirements.txt lista todas as dependências que seu projeto precisa para executar. Este arquivo permite que outras pessoas recriem seu ambiente e automatizem pipelines.

### Passos:
- Para gerar o arquivo, use o comando:

```bash
pip freeze > requirements.txt
```

- Para instalar dependências do arquivo, use:

```bash
pip install -r requirements.txt
```

## 2. Atualize seu README.md e dê uma Chance para o MkDocs
Um projeto bem documentado oferece inúmeros benefícios, não apenas para aqueles que irão herdar ou se interessar por ele, mas também para você mesmo ao revisitar no futuro.

Pode confiar!

### Seu README.md deve ter:
- Descrição do projeto
- Instruções de instalação e como executar
- Exemplos de uso
- Diretrizes de contribuição (código aberto)

### MkDocs é uma ferramenta para criação de sites de documentação:
- Instale o MkDocs:

```bash
pip install mkdocs

# or 

poetry add mkdocs
```

- Crie um projeto de documentação:

```bash
mkdocs new project-docs
```

- Hospede o site localmente com:

```bash
cd project-docs
mkdocs serve
```

- Realize o deploy no GitHub Pages com:

```bash
mkdocs gh-deploy
```

Agora você terá um projeto bem documentado!

## 3. Pre-commit

Os Pre-commit hooks automatizam as verificações antes de permitir que as alterações sejam confirmadas. Isso pode ajudá-lo a economizar muito tempo e garantir a qualidade do código, evitando erros comuns.

### Passos
- Instale o pre-commit com:

```bash
pip install pre-commit
```

- Adicione um arquivo `.pre-commit-config.yaml` com hooks para tarefas de como corrigir espaços em branco ou realizar linting.

```bash
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0 
    hooks:
      - id: check-yaml # check-yaml: Garante que seus arquivos YAML sejam válidos.
      - id: end-of-file-fixer # end-of-file-fixer: Garante que haja exatamente uma nova linha no final dos arquivos.
      - id: trailing-whitespace # trailing-whitespace: Remove espaços em branco à direita dos arquivos.
      - id: debug-statements # debug-statements: Avisa sobre sobras de instruções print() ou pdb.
  - repo: https://github.com/psf/black
    rev: 23.9.1 
    hooks:
      - id: black # black: Formata seu código Python de acordo com o estilo de código Black.
        language_version: python3
```

- Instale os hooks usando:

```bash
pre-commit install
```

- Teste:

```bash
pre-commit run --all-files
```

- Resultado esperado:

```bash
(venv) PS C:\WVS\project-docs> pre-commit run --all-files
check yaml...............................................................Passed
fix end of files.........................................................Passed
trim trailing whitespace.................................................Passed
debug statements (python)................................................Passed
black....................................................................Passed
```

Agora, cada commit executará essas verificações automaticamente.


## 4. Docker

Se estiver compartilhando seu projeto usando um arquivo `requirements.txt`, você também deve considerar usar o Docker da mesma maneira. 

Faça com que todos os seus projetos sejam Docker First porque é o método mais eficaz para garantir que outras pessoas possam replicá-los e executá-los facilmente. 

E, claro, garante ambientes consistentes em todos os estágios de desenvolvimento e teste.

### Passos:

- Crie um `Dockerfile` definindo o ambiente e as dependências do projeto.

```Dockerfile
# Use a imagem oficial do Python como imagem base
FROM python:3.12-slim

# Defina o diretório de trabalho dentro do contêiner
WORKDIR /app

# Copie o arquivo de requisitos e instale as dependências
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copie o código do aplicativo no contêiner
COPY src/ ./src/

# Exponha a porta em que o aplicativo é executado
EXPOSE 5000

# Defina o comando padrão para executar o aplicativo
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "src.app:app"]
```

- Faça um Build da imagem Docker:

```bash
docker build -t api .
```

- Rode o Container:

```bash
docker run -p 5000:5000 api
```

***Dica:** adicione um arquivo `.dockerignore` para evitar que arquivos desnecessários sejam copiados para a imagem do Docker, se necessário.*

## 5. Test-Driven Development (TDD)

O Test-Driven Development (TDD) envolve escrever testes antes de implementar a funcionalidade. Em vez de testar manualmente, você pode escrever alguns testes para simular as principais ações.

### Passos:
- Instale o pytest:

```bash
pip install pytest

# or

poetry add pytest
```

- Crie um diretório de testes/
- Aqui está um exemplo de código:

```bash
import sys
import os
import pytest

# Add the 'src' directory to the Python path
sys.path.insert(0, os.path.abspath(os.path.join(os.path.dirname(__file__), '../src')))

from app import app

@pytest.fixture
def client():
    app.config['TESTING'] = True
    with app.test_client() as client:
        yield client

def test_api_is_up(client):
    # Send a GET request to the root endpoint
    response = client.get('/')

    # Assert that the response status code is 200 (OK)
    assert response.status_code == 200
```

- Execute os testes usando pytest:

```bash
pytest
```

- Retorno esperado:

```bash
=========================================== test session starts ===========================================
platform win32 -- Python 3.11.5, pytest-8.3.3, pluggy-1.5.0
rootdir: C:\wvs\api
configfile: pyproject.toml
plugins: flask-1.3.0
collected 1 item

tests\test_app.py .                                                                                  [100%]

============================================ 1 passed in 0.31s ============================================
```

## 6. CI/CD

CI/CD automatiza tarefas como testar e implantar seu código. Com certeza, o teste é bom, mas deixe os pipelines de CI/CD testarem e implantarem para você!

### Passos:
- Crie um arquivo de configuração: `.github/workflows/pipeline.yml`:

```bash
name: Python Only CI  

on: 
  push:
    branches:
      - main
  pull_request:
    branches:
      - main  

jobs:  
  build:  
    runs-on: ubuntu-latest  

    steps:  
    - uses: actions/checkout@v3  

    - name: Set up Python  
      uses: actions/setup-python@v4  
      with:  
        python-version: '3.12'  

    - name: Install dependencies  
      run: pip install -r requirements.txt  

    - name: Run tests  
      run: pytest
```

Isso garante que os testes sejam executados automaticamente sempre que o código for enviado ou uma solicitação pull for feita.

Seguindo essas práticas, você garante que seu projeto seja profissional.

Porém, não pare por aqui, existe assunto demais para explorar em cada tópico que mencionei.

Esses dois artigos foram apenas uma visão geral do que você é capaz de alcançar.

--

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)
