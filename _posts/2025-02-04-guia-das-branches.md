---
title: "Um Guia para Git Branches"
date: 2025-02-04 00:00:00:0000
categories: [Git & GitHub]
tags: [git & github]
---

Sim, você provavelmente já ouviu falar de branches antes, por isso quis fazer um guia para você.

Se você está cansado de ficar sempre verificando na internet, este artigo é ideal para você.

Mas…

Se é a sua primeira vez, não tem problema, este artigo também é ideal para você.


## O que é uma Branch no Git?
Uma branch no Git é uma linha independente de desenvolvimento dentro de um repositório.

Pense nisso como uma pasta de trabalho separada onde você pode fazer alterações sem afetar a pasta principal. As branches permitem que os desenvolvedores trabalhem em novos recursos, correções de bugs ou experimentos sem alterar a versão real do projeto.

É algo como `final_version_89.xlsx` da maneira mais inteligente e controlada.

## Por que Usar Branches?
As branches oferecem vários benefícios no fluxo de desenvolvimento:
- **Isolamento:** Mantêm tarefas diferentes (novos recursos, correções de bugs, experimentos) separadas.
- **Colaboração:** Vários desenvolvedores podem trabalhar em branches distintas sem interferir uns nos outros.
- **Experimentação Segura:** Permitem testar mudanças sem afetar o código em produção.
- **Controle de Versão:** Facilita a reversão ou troca entre diferentes versões do projeto.

No Git, a branch padrão geralmente se chama `main`. No entanto, novas branches podem ser criadas para diferentes propósitos, como:
- **Feature branches (`feature/new-ui`):** Usadas para desenvolver novas funcionalidades.
- **Bugfix branches (`hotfix/login-fix`):** Usadas para corrigir problemas em produção.
- **Release branches (`release/v1.2.0`):** Usadas para preparar versões estáveis para implantação.

Agora, para ajudá-lo a gerenciar suas branches, abordarei quatro tópicos:
- Criando e Gerenciando Branches Locais
- Mesclando Branches
- Branches Remotas e Rastreamento de Branches
- Fluxo de Trabalho com Branches (Feature Branch, Hotfix e Release)

![point-of-no-return](/assets/images/2025-02-04-guia-das-branches/point-of-no-return.png)

## Criando e Gerenciando Branches Locais

Como mencionei anteriormente, as branches no Git permitem que você trabalhe em novos recursos, correções de bugs ou testes sem afetar a base de código principal.

Aqui está como criar, alternar, renomear e excluir branches de forma eficiente.

### 1. Criar uma Nova Branch
```bash
git branch feature/create-article
```

- Isso cria uma nova branch chamada `feature/create-article`, mas não alterna para ela.
- Use quando você precisa preparar várias branches, mas não quer mudar imediatamente.

### 2. Alternar para uma Branch
```bash
git checkout feature/create-article
```

- Isso move você para a branch especificada.

**Alternativa moderna:** Desde o Git 2.23+, use `git switch`:
```bash
git switch feature/create-article
```

- É uma alternativa mais limpa e segura ao `checkout`.

### 3. Criar e Alternar para uma Nova Branch
```bash
git checkout -b feature/create-article-2
```

- Cria uma nova branch e muda para ela imediatamente.
- Isso economiza tempo ao combinar dois comandos em um.

**Alternativa moderna:**
```bash
git switch -c feature/create-article-2
```

- Mais intuitivo e recomendado para versões mais recentes do Git.

### 4. Listar Todas as Branches
```bash
git branch
```

- Exibe todas as branches locais. A branch atual é marcada com `*`.

```bash
  feature/create-article  
* feature/create-article-2  
  main  
```

Para listar também as branches remotas:
```bash
git branch -a
```

- *P.S: Branches remotas serão o próximo assunto…*

### 5. Renomear uma Branch
```bash
git branch -m feature/create-article-2 feature/create-article-1
```

- Renomeia a branch `feature/create-article-2` para `feature/create-article-1`.

Se você já estiver na branch que deseja renomear:
```bash
git branch -m feature/create-article-1
```

### 6. Excluir uma Branch Local (Com Segurança rs)
```bash
git branch -d feature/create-article-1
```

- Remove a branch somente se ela já estiver mesclada em outra branch.
- Se houver alterações não mescladas, o Git impedirá a exclusão para evitar perda de dados.

### 7. Forçar a Exclusão de uma Branch (Cuidado!)
```bash
git branch -D feature/create-article
```

- Isso exclui a branch mesmo que tenha alterações não mescladas.
- Use com cautela para evitar perda de trabalho importante.

## Mesclando Branches
No Git, **merge é o processo de integrar as alterações de uma branch em outra.** Ele é comumente usado para combinar atualizações de diferentes branches de desenvolvimento em uma branch principal.

![meme-merge](/assets/images/2025-02-04-guia-das-branches/meme-merge.png)

### 1. Mesclar uma Branch na Branch Atual
```bash
git merge feature/create-article
```

- Isso integra as mudanças de `feature/create-article` na branch em que você está atualmente.
- Se não houver conflitos, o Git concluirá o merge automaticamente.

Certifique-se de estar na branch correta antes de mesclar:
```bash
git checkout main  # ou git switch main
git merge feature/create-article
```

### 2. Resolvendo Conflitos de Merge
Durante uma mesclagem, se houver conflitos, o Git pausará o processo e informará os arquivos conflitantes.

Os conflitos são indicados no dessa maneira:
```bash
<<<<<<< HEAD
(alterações na branch atual)
=======
(alterações na branch mesclada)
>>>>>>> main
```

- A seção `HEAD` representa as alterações da sua branch atual.
- A seção abaixo de `=======` vem da branch que está sendo mesclada.

**Passos para Resolver Conflitos:**
1. Abra os arquivos conflitantes em um editor de texto ou VS Code.
2. Edite manualmente e mantenha a versão correta do código.
3. Marque os arquivos como resolvidos:

```bash
git add .
```

4. Complete a mesclagem com um commit
```bash
git commit -m "Resolvendo conflitos de merge."
```

5. Se quiser cancelar o merge e voltar ao estado anterior:
```bash
git merge --abort
```

### Tipos de Merge no Git
O Git suporta diferentes estratégias de mesclagem, dependendo se as branches divergiram ou não.

#### 1. Fast-Forward Merge (Sem Divergência)
O **fast-forward merge ocorre quando a branch sendo mesclada está à frente da branch atual sem modificações.**

O Git simplesmente move o ponteiro da branch em vez de criar um commit de merge.

**Exemplo:**
```bash
git checkout main
git merge feature/create-article
```

Se a `main` não teve alterações desde que `feature/create-article` foi criada, o resultado será:
```bash
Updating 5a26f39..db14708  
Fast-forward  
 filename.txt | 1 +  
 1 file changed, 1 insertion(+)  
 create mode 100644 filename.txt  
```

#### 2. Three-Way Merge (Branches Divergentes)
O **three-way merge acontece quando as duas branches possuem históricos diferentes e não podem ser mescladas via fast-forward.**

Nesse caso, o Git cria um novo commit de merge para combinar as mudanças.

**Exemplo:**
```bash
git checkout main
git merge feature/create-article
```

Isso gerará uma mensagem de commit automática:
```bash
Merge branch 'feature/create-article' into main
```

Agora, um novo commit de merge é criado para unir as duas branches.

## Branches Remotas e Rastreamento de Branches
Branches remotas são **versões das suas branches armazenadas em um repositório remoto (GitHub, GitLab, Bitbucket).**

Elas permitem colaboração entre vários desenvolvedores, mantendo os repositórios locais sincronizados com o repositório remoto.

This is where the magic happens…

### Por que usar branches remotas?
- Para colaborar com outros desenvolvedores, enviando (`push`) e recebendo (`pull`) mudanças.
- Para manter um backup do seu trabalho em um repositório central.
- Para gerenciar diferentes ambientes, como `main`, `dev` e `staging`.

### 1. Listar Branches Remotas
Para ver todas as branches armazenadas no repositório remoto:
```bash
git branch -r
```

- Isso lista apenas branches remotas, prefixadas por `origin/`.

Para listar todas as branches (locais e remotas):
```bash
git branch -a
```

- Isso ajuda a verificar quais branches existem localmente e remotamente.

### 2. Criar uma Branch que Rastreia uma Branch Remota
Se precisar trabalhar em uma branch remota localmente, use:
```bash
git checkout --track origin/dev
```

- Isso cria uma branch local chamada `dev` e a configura para rastrear `origin/dev`.

Resultado:
```bash
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
Switched to a new branch 'dev'.
```

- Isso é útil quando o Git não configura automaticamente o rastreamento da branch.

### 3. Atualizar Branches Remotas
Para buscar atualizações do repositório remoto e garantir que você tem a última lista de branches:
```bash
git fetch
```

- Isso baixa as mudanças remotas mas não as aplica ao seu diretório de trabalho.

### 4. Mesclar Mudanças de uma Branch Remota na Branch Atual

Para puxar as últimas atualizações de uma branch remota para sua branch local:
```bash
git pull origin main
```

Isso equivale a rodar manualmente:
```bash
git fetch
git merge origin/main
```

Se a sua branch já está rastreando `origin/<nome-da-branch>`, basta rodar:
```bash
git pull
```

### 5. Enviar uma Nova Branch Local para o Repositório Remoto
Após criar uma nova branch localmente, envie-a para o repositório remoto:

```bash
git push --set-upstream origin dev
```

- O `--set-upstream` define um rastreamento, permitindo que futuros `git pull` e `git push` sejam executados sem precisar especificar o nome da branch.

### 6. Excluir uma Branch Remota
Se uma branch remota não for mais necessária, exclua-a com:

```bash
git push origin --delete dev
```

- Isso remove `dev` do repositório remoto.

Para excluir uma referência local à branch remota já deletada:
```bash
git remote prune origin
```

- Isso limpa as referências de branches remotas que não existem mais.

## Workflow com Branches: Feature, Hotfix e Release

Este guia apresenta o fluxo de trabalho padrão usando branches do Git para desenvolvimento de funcionalidades (`features`), correções de bugs (`hotfix`) e lançamentos de versões (`release`).

O objetivo é mostrar um processo simples para implantar uma versão, corrigir um bug e lançar uma funcionalidade.

### 1. Feature Branch
Branches de feature permitem desenvolver novas funcionalidades de forma independente, sem afetar o código principal.

**Criar uma branch de feature:**
```bash
git checkout -b feature/create-article
```
- Faça as alterações necessárias e realize commit no progresso.

**Mesclar a branch de feature na branch `dev`:**
```bash
git checkout dev
git merge feature/create-article
```

- Isso garante que as novas funcionalidades sejam integradas ao fluxo de desenvolvimento sem afetar o código de produção.

### 2. Hotfix Branch
Branches de hotfix são usadas para correções urgentes em ambientes de produção.

**Criar uma branch de hotfix baseada na branch `dev`:**
```bash
git checkout -b hotfix/main-article dev
```
- Faça as correções necessárias e comite as mudanças.

**Mesclar a branch de hotfix na `dev`:**
```bash
git checkout dev
git merge hotfix/main-article
```

- **Recomendação:** Use sempre a branch `dev`, nunca diretamente a `main`, especialmente se `main` estiver em produção. Resolver um bug pode gerar outros!

### 3. Release Branch
Branches de release são criadas para preparar uma nova versão do software antes do deploy em produção. Isso permite testes e ajustes finais.

**Criar uma branch de release baseada na `dev`:**
```bash
git checkout -b release/1.0.0 dev
```

- Realize testes e ajustes finais, comitando as mudanças conforme necessário.

**Mesclar a release branch na `main` e criar um tag da versão:**
```bash
git checkout main
git merge release/1.0.0
git tag -a v1.0.0 -m "Release 1.0.0"
```

**Garantir que as mudanças da release também voltem para `dev`:**
```bash
git checkout dev
git merge release/1.0.0
```

- Isso garante que eventuais correções feitas durante a release não sejam perdidas no fluxo de desenvolvimento.

--

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)