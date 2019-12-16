---
title: Instalação
description: Nuxt.js é realmente fácil de começar. Um projeto simples precisa apenas da dependência do `nuxt`.
---

> Nuxt.js é realmente fácil de começar. Um projeto simples precisa apenas da dependência do `nuxt`.

<div>
  <a href="https://vueschool.io/courses/nuxtjs-fundamentals/?friend=nuxt" target="_blank" class="Promote">
    <img src="/nuxt-fundamentals.png" srcset="/nuxt-fundamentals-2x.png 2x" alt="Nuxt Fundamentals by vueschool"/>
    <div class="Promote__Content">
      <h4 class="Promote__Content__Title">Fundamentos do Nuxt.js</h4>
      <p class="Promote__Content__Description">Aprenda a começar rapidamente com o Nuxt.js nos vídeos.</p>
      <p class="Promote__Content__Signature">Vídeo aulas feitas pela VueSchool para apoiar o desenvolvimento do Nuxt.js.</p>
    </div>
  </a>
</div>

## Usando o modelo inicial do Nuxt.js.

Para começar rapidamente, a equipe do Nuxt.js criou um [modelo inicial](https://github.com/nuxt-community/starter-template).

[Download the .zip](https://github.com/nuxt-community/starter-template/archive/master.zip) modelo inicial ou instale-o com o Vue CLI:

```bash
$ vue init nuxt-community/starter-template <project-name>
```

> Se [Vue CLI](https://github.com/vuejs/vue-cli) não estiver instalado, instale-o com `npm install -g @vue/cli @vue/cli-init`

instale as dependências:

```bash
$ cd <project-name>
$ npm install
```

e inicie o projeto com:

```bash
$ npm run dev
```
O aplicação agora está sendo executado em http://localhost:3000.

<div class="Alert">

O Nuxt.js escutará as alterações do arquivo dentro do diretório <code> pages </code>, portanto, não há necessidade de reiniciar a aplicação ao adicionar novas páginas.

</div>

Para descobrir mais sobre a estrutura de diretórios do projeto: [Documentação da estrutura de diretórios](/guide/directory-structure).

## Começando do zero

Criar um projeto Nuxt.js a partir do zero também é muito fácil, ele precisa apenas de *1 arquivo e 1 diretório*. Vamos criar um diretório vazio para começar a trabalhar no projeto:

```bash
$ mkdir <project-name>
$ cd <project-name>
```

<div class="Alert Alert--nuxt-green">

<b>Info:</b> substituir <code>&lt;project-name&gt;</nom-du-projet></code> pelo nome do projeto.

</div>

###  package.json

O projeto precisa de um arquivo `package.json` para especificar como iniciar o` nuxt`:

```json
{
  "name": "my-app",
  "scripts": {
    "dev": "nuxt"
  }
}
```

`scripts` iniciará o Nuxt.js através do` npm run dev`.

### Instalando o `nuxt`

Depois que o `package.json` for criado, adicione` nuxt` ao projeto via npm:

```bash
$ npm install --save nuxt
```

### O diretório `pages`

O Nuxt.js transformará todos os arquivos `* .vue` dentro do diretório` pages` como uma rota para a aplicação.

Crie o diretório `pages`:

```bash
$ mkdir pages
```

então crie a primeira página em `pages/index.vue`:

```html
<template>
  <h1>Hello world!</h1>
</template>
```

e inicie o projeto com:

```bash
$ npm run dev
```

O aplicativo agora está sendo executado emhttp://localhost:3000.

<div class="Alert">

O Nuxt.js escutará as alterações do arquivo dentro do diretório <code> pages </code>, portanto, não há necessidade de reiniciar o aplicativo ao adicionar novas páginas.

</div>

Para descobrir mais sobre a estrutura de diretórios do projeto: [Documentação da estrutura de diretórios](/guide/directory-structure).
