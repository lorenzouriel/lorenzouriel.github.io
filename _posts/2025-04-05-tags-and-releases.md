---
title: "Criando uma Tag e um Release no Git & GitHub"
date: 2025-04-05 00:00:00:0000
categories: [Git & GitHub]
tags: [git & github]
---

Git não se trata apenas de branches. Às vezes, precisamos lançar versões.

Existem dois conceitos-chave para isso: **tags** e **releases**.

Usar esses recursos de forma eficiente pode melhorar tanto a qualidade do código quanto a organização do repositório.

Como isso pode te ajudar?
- Tags marcam pontos específicos na história do repositório, servindo como referência para versões importantes do projeto.
- Releases são construídas a partir das tags, adicionando metadados como notas de lançamento (release notes), binários e documentação sobre a nova versão.

Isso facilita o controle e o lançamento de atualizações.

## Criando uma Tag
O Git suporta dois principais tipos de tags:
- **Lightweight Tags:** apenas uma referência a um commit específico.
- **Annotated Tags:** contêm metadados como autor, data e uma mensagem.

Para criar uma **annotated tag**, use o seguinte comando:
```sh
git tag -a v1.0.0 -m "First Release"
```

Para criar uma **lightweight tag**, use:
```sh
git tag v1.0.0
```

Cada número na versão tem seu próprio significado e importância. Aqui vai uma explicação simples e prática:
- **Major**: Mudanças incompatíveis com versões anteriores (Reestruturou tudo? Some +1 — `v2.0.0`)  
- **Minor**: Mudanças compatíveis com a versão anterior, mas com novas funcionalidades (Adicionou uma nova feature? Some +1 — `v2.1.0`)  
- **Correction (Patch)**: Correções de bugs sem impacto na compatibilidade (Corrigiu um erro? Some +1 — `v2.1.1`)  
- **Build**: Controle interno de versionamento, nem sempre visível na versão final.

![tags](/assets/images/2025-04-05-tags-and-releases/tags.webp)

Listando todas as tags do repositório:
```bash
git tag
```

Por padrão, o `git push` não envia tags. Para enviar uma tag específica:
```bash
git push origin v1.0.0
```

Para enviar todas as tags:
```bash
git push origin --tags
```

### Criando uma Release no GitHub

1. Vá até o repositório no GitHub.  

2. Clique na aba **Releases**.  
![release-tab](/assets/images/2025-04-05-tags-and-releases/release-tab.webp)

3. Clique em **“Create a new release”**.  
![create-new-release](/assets/images/2025-04-05-tags-and-releases/create-new-release.webp)

4. Selecione a *tag* que foi enviada para o repositório, adicione as *release notes* descrevendo as mudanças e o título da versão. Para finalizar, clique em **“Publish release”**.  
![select-tag](/assets/images/2025-04-05-tags-and-releases/select-tag.webp)

5. O GitHub irá gerar os arquivos binários e compilados automaticamente.  
![git-files](/assets/images/2025-04-05-tags-and-releases/git-files.webp)

6. Todas as *releases* ficarão disponíveis no seu projeto.
![releases-tab-versions](/assets/images/2025-04-05-tags-and-releases/releases-tab-versions.webp)

E pronto! Sua nova **tag** e **release** do projeto estão criadas e publicadas.

![congrats](/assets/images/2025-04-05-tags-and-releases/congrats.webp)

---

Criei um guia completo para te ajudar a entender **Git & GitHub** — [confere lá!](https://lorenzouriel.gumroad.com/l/giqht)

---

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)