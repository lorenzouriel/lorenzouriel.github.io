---
title: "Um Guia para Git Revert, Reset e Checkout"
date: 2025-03-11 00:00:00:0000
categories: [Git & GitHub]
tags: [git & github]
---


Se algo der errado, você precisa saber como reagir. Git não é apenas sobre o caminho feliz:
- Você trabalha
- Você faz um commit
- Faz o merge da branch
- Faz o deploy

Adoramos o caminho feliz, é por isso que ele é feliz!

Mas, com frequência, as coisas não saem como planejado e precisamos saber como reagir nesses casos.

Como você recupera alterações perdidas? Como você reverte erros?

É mais simples do que parece:

- `git reset`: volta no tempo, você pode excluir commits (cuidado!).
- `git revert`: desfaça um commit criando um novo (histórico seguro).
- `git checkout` / `restore`: recupere arquivos para o último estado salvo.

Vamos mergulhar neles!

## `git revert`

`git revert` cria um novo commit que desfaz as alterações introduzidas por um commit específico sem modificar o histórico do repositório.

Isso é útil quando você deseja manter o histórico limpo sem remover commits.

### Uso:
```bash
git revert commit-hash
```

### Exemplo:
#### 1. Verifique o commit que deseja reverter:
```bash
git log --oneline 
```
**Retorno:**
```bash
PS C:\ebooks\git-github-ebook-test> git log --oneline 
56c9845 revert
3d87987 merge tag 'v1.0' into dev Showw show
d207431 (tag: v1.0, main) Merge branch 'release/v1.0'
28b146b Yes
0a91ea2 Updated commit message
ca382f6 (origin/main) filename3
db14708 new file
5a26f39 (tag: v1.0.0) first commit
```

#### 2. Copie o hash e execute `git revert`:
```bash
git revert 56c9845
```
**Retorno:**
```bash
PS C:\ebooks\git-github-ebook-test> git log --oneline 
4134ad2 (HEAD -> dev) Revert "revert"
56c9845 revert
3d87987 merge tag 'v1.0' into dev Showw show
d207431 (tag: v1.0, main) Merge branch 'release/v1.0'
28b146b Yes
0a91ea2 Updated commit message
ca382f6 (origin/main) filename3
db14708 new file
5a26f39 (tag: v1.0.0) first commit
```
- Isso cria um novo commit que reverte as alterações do commit `56c9845`.

## `git reset`

`git reset` move o ponteiro HEAD para um commit anterior e é usado para modificar o histórico de commits. Ele possui três modos principais:

### Modos do `git reset`:
- Soft (`--soft`)
- Mixed (`--mixed`)
- Hard (`--hard`)

### Exemplo `--soft`:
A opção `--soft` desfaz o último commit, mas mantém as alterações na staging area. Isso é útil se você quiser refazer um commit sem perder o trabalho realizado.

Volte para a etapa do `git add .` e então você pode simplesmente fazer o commit novamente.
```bash
git reset --soft HEAD~1 
```
- Neste exemplo, `HEAD~1` move o HEAD um commit para trás, mantendo todas as alterações na staging area.

### Exemplo `--mixed`:
A opção `--mixed` reseta o HEAD para o commit especificado, remove as alterações da staging area, mas as mantém no diretório de trabalho.

É semelhante ao `--soft`, mas aqui você volta antes do `git add .`.
```bash
git reset --mixed HEAD~1
```
- Isso desfaz o último commit e desestagia as alterações, mas elas ainda permanecem no diretório de trabalho.

### Exemplo `--hard`:
A opção `--hard` reseta tanto a staging area quanto o diretório de trabalho para o commit especificado. Isso significa que todas as alterações serão perdidas.

Pode ser destrutivo, cuidado!!!
```bash
git reset --hard HEAD~1
```
- Isso removerá o último commit e todas as alterações associadas a ele.

## `git checkout`

`git checkout` pode ser usado para desfazer alterações em arquivos individuais ou para mudar de branch. Até agora, falamos sobre ele apenas para branches.

`git checkout` também era usado para desfazer commits, mas atualmente `git switch` e `git restore` são recomendados para essas tarefas.

### Uso para restaurar arquivos específicos:
`git checkout` pode ser usado para desfazer alterações em arquivos individuais e restaurá-los para o último estado commitado.
```bash
git checkout -- filename.txt
```
- Este comando descarta quaisquer alterações não commitadas no arquivo `filename.txt`, restaurando-o para seu estado mais recente no commit anterior.

O `--` é usado para indicar ao Git que você está se referindo a um arquivo (não a uma branch) e deseja descartar as alterações no diretório de trabalho.

### Exemplo `git restore`:
`git restore` é o comando recomendado para restaurar arquivos e é mais intuitivo do que `git checkout` para essa finalidade.
```bash
git restore filename.txt
```
- Isso descarta quaisquer alterações não commitadas no arquivo `filename.txt`, restaurando-o para a versão do último commit, assim como `git checkout -- filename.txt`.

Esses são apenas alguns exemplos de solução de problemas e casos de uso. Claro, há outros comandos úteis para ajudar a diagnosticar problemas, que abordaremos em outro artigo.

--

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)