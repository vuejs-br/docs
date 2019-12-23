---
title: Routing
description: O Nuxt.js usa o sistema de arquivos para gerar as rotas de suas aplicações web.
---

> O Nuxt.js gera automaticamente a configuração [vue-router](https://github.com/vuejs/vue-router) com base na sua árvore de arquivos do Vue dentro do diretório `pages`.

## Rotas básicas

Esta árvore de arquivos:

```bash
pages/
--| user/
-----| index.vue
-----| one.vue
--| index.vue
```

irá gerar automaticamente:

```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'user',
      path: '/user',
      component: 'pages/user/index.vue'
    },
    {
      name: 'user-one',
      path: '/user/one',
      component: 'pages/user/one.vue'
    }
  ]
}
```

## Rotas dinâmicas

Para definir uma rota dinâmica com um parâmetro, você precisa definir um arquivo .vue OU um diretório **prefixado por uma underline**.

Esta árvore de arquivos:

```bash
pages/
--| _slug/
-----| comments.vue
-----| index.vue
--| users/
-----| _id.vue
--| index.vue
```

irá gerar automaticamente:

```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'users-id',
      path: '/users/:id?',
      component: 'pages/users/_id.vue'
    },
    {
      name: 'slug',
      path: '/:slug',
      component: 'pages/_slug/index.vue'
    },
    {
      name: 'slug-comments',
      path: '/:slug/comments',
      component: 'pages/_slug/comments.vue'
    }
  ]
}
```

Como você pode ver, a rota chamada `users-id` possui o caminho`: id? `, O que o torna opcional, se você desejar, crie um arquivo` index.vue` no diretório `users / _id`.

<div class="Alert Alert--orange">

<b> Aviso: </b> rotas dinâmicas são ignoradas pelo comando `generate`: [API Configuration generate](/api/configuration-generate # routes)

</div>

### Validar parâmetros de rota

O Nuxt.js permite definir um método validador dentro do seu componente de rota dinâmica.

Neste exemplo: `pages/users/_id.vue`

```js
export default {
  validate ({ params }) {
    // Must be a number
    return /^\d+$/.test(params.id)
  }
}
```

Se o método validate não retornar `true`, o Nuxt.js carregará automaticamente a página de erro 404.

Mais informações sobre o método validate: [API Pages validate](/api/pages-validate)

## Rotas aninhadas

O Nuxt.js permite criar rotas aninhadas usando as rotas secundárias do vue-router.

Para definir o componente pai de uma rota aninhada, você precisa criar um arquivo Vue com o **mesmo nome que o diretório**, que contém as visualizações de seus filhos.

<div class="Alert Alert--orange">

<b> Aviso: </b> não esqueça de escrever `<nuxt-child />` dentro do componente pai (arquivo <code> .vue </code>).

</div>

Esta árvore de arquivos:

```bash
pages/
--| users/
-----| _id.vue
-----| index.vue
--| users.vue
```

irá gerar automaticamente:

```js
router: {
  routes: [
    {
      path: '/users',
      component: 'pages/users.vue',
      children: [
        {
          path: '',
          component: 'pages/users/index.vue',
          name: 'users'
        },
        {
          path: ':id',
          component: 'pages/users/_id.vue',
          name: 'users-id'
        }
      ]
    }
  ]
}
```

## Rotas aninhadas dinâmicas

Esse cenário não costuma acontecer, mas é possível com o Nuxt.js: ter filhos dinâmicos dentro de pais dinâmicos.

Esta árvore de arquivos:

```bash
pages/
--| _category/
-----| _subCategory/
--------| _id.vue
--------| index.vue
-----| _subCategory.vue
-----| index.vue
--| _category.vue
--| index.vue
```

irá gerar automaticamente:

```js
router: {
  routes: [
    {
      path: '/',
      component: 'pages/index.vue',
      name: 'index'
    },
    {
      path: '/:category',
      component: 'pages/_category.vue',
      children: [
        {
          path: '',
          component: 'pages/_category/index.vue',
          name: 'category'
        },
        {
          path: ':subCategory',
          component: 'pages/_category/_subCategory.vue',
          children: [
            {
              path: '',
              component: 'pages/_category/_subCategory/index.vue',
              name: 'category-subCategory'
            },
            {
              path: ':id',
              component: 'pages/_category/_subCategory/_id.vue',
              name: 'category-subCategory-id'
            }
          ]
        }
      ]
    }
  ]
}
```

## Transições

O Nuxt.js usa o componente [`<transition>`](http://vuejs.org/v2/guide/transitions.html#Transitioning-Single-Elements-Components) para permitir que você crie transições/animações incríveis entre suas rotas.

### Configurações globais

<div class="Alert Alert--nuxt-green">

<b>Info :</b> O nome da transição padrão do Nuxt.js é `"page"`.

</div>

Para adicionar uma transição fade a todas as páginas do seu aplicativo, precisamos de um arquivo CSS que seja compartilhado em todas as nossas rotas, então começamos criando um arquivo na pasta `assets`.

Nosso css global em `assets/main.css`:

```css
.page-enter-active, .page-leave-active {
  transition: opacity .5s;
}
.page-enter, .page-leave-to {
  opacity: 0;
}
```

Nós adicionamos seu caminho no nosso arquivo `nuxt.config.js`:

```js
module.exports = {
  css: [
    'assets/main.css'
  ]
}
```

Mais informações sobre a chave de transição: [Transição de configuração da API](/api/pages-transição)

### Configurações da página

Você também pode definir uma transição personalizada para apenas uma página com a propriedade `transition`.

Adicionamos uma nova classe em nosso css global em `assets/main.css`:

```css
.test-enter-active, .test-leave-active {
  transition: opacity .5s;
}
.test-enter, .test-leave-active {
  opacity: 0;
}
```

então, usamos a propriedade de transição para definir o nome da classe a ser usada para esta transição de página:

```js
export default {
  transition: 'test'
}
```

Mais informações sobre a propriedade de transição: [Transição de páginas da API](/api/pages-transição)

## Middleware

> O middleware permite definir funções personalizadas que podem ser executadas antes da renderização de uma página ou de um grupo de páginas.

**Todo middleware deve ser colocado no diretório `middleware /`.** O nome do arquivo será o nome do middleware (`middleware / auth.js` será o `auth`).

Um middleware recebe [o context](/api/context) como primeiro argumento:

```js
export default function (context) {
  context.userAgent = process.server ? context.req.headers['user-agent'] : navigator.userAgent
}
```

O middleware será executado em série nesta ordem:

1. `nuxt.config.js`
2. Layouts correspondentes
3. Páginas correspondentes

Um middleware pode ser assíncrono. Para fazer isso, basta retornar uma promessa ou usar o segundo argumento de `callback`:

`middleware/stats.js`

```js
import axios from 'axios'

export default function ({ route }) {
  return axios.post('http://my-stats-api.com', {
    url: route.fullPath
  })
}
```

Então, no seu layout ou página `nuxt.config.js`, use a chave` middleware`:

`nuxt.config.js`

```js
module.exports = {
  router: {
    middleware: 'stats'
  }
}
```

O middleware `stats` será chamado para todas as mudanças de rota.

Para ver um exemplo real usando o middleware, consulte [example-auth0](https://github.com/nuxt/example-auth0) no GitHub.
