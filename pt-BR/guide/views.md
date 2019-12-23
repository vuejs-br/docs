---
title: Views
description: A seção Views descreve tudo o que você precisa para configurar dados e exibições para uma rota específica no seu aplicativo Nuxt.js (documento, layouts, páginas e cabeçalho HTML).
---

> A seção Views descreve tudo o que você precisa para configurar dados e exibições para uma rota específica no seu aplicativo Nuxt.js (documento, layouts, páginas e cabeçalho HTML).

![nuxt-views-schema](/nuxt-views-schema.svg)

## Documento

> Você pode personalizar o documento principal com o Nuxt.js.

Para estender o modelo HTML, crie um `app.html` na raiz do seu projeto.

O modelo padrão é:

```html
<!DOCTYPE html>
<html {{ HTML_ATTRS }}>
  <head {{ HEAD_ATTRS }}>
    {{ HEAD }}
  </head>
  <body {{ BODY_ATTRS }}>
    {{ APP }}
  </body>
</html>
```

Um exemplo se adicionar classes CSS condicionais para o IE:

```html
<!DOCTYPE html>
<!--[if IE 9]><html lang="en-US" class="lt-ie9 ie9" {{ HTML_ATTRS }}><![endif]-->
<!--[if (gt IE 9)|!(IE)]><!--><html {{ HTML_ATTRS }}><!--<![endif]-->
  <head {{ HEAD_ATTRS }}>
    {{ HEAD }}
  </head>
  <body {{ BODY_ATTRS }}>
    {{ APP }}
  </body>
</html>
```

## Layouts

O Nuxt.js permite estender o layout principal ou criar layouts personalizados, adicionando-os no diretório `layouts`.

### Layout padrão

Você pode estender o layout principal adicionando um arquivo `layouts/default.vue`.

*Certifique-se de adicionar o componente `<nuxt />` ao criar um layout para exibir o componente da página.*

O código-fonte do layout padrão é:

```html
<template>
  <nuxt/>
</template>
```

### Página de erro

Você pode personalizar a página de erro adicionando um arquivo `layouts/error.vue`.

Esse layout é especial, pois você não deve incluir `<nuxt/>` dentro de seu template. Você deve ver esse layout como um componente exibido quando ocorrer um erro (404, 500, etc).

O código-fonte da página de erro padrão está [disponível no GitHub](https://github.com/nuxt/nuxt.js/blob/dev/packages/vue-app/template/components/nuxt-error.vue).

Exemplo de uma página de erro personalizada em `layouts/error.vue`:

```html
<template>
  <div class="container">
    <h1 v-if="error.statusCode === 404">Page not found</h1>
    <h1 v-else>An error occurred</h1>
    <nuxt-link to="/">Home page</nuxt-link>
  </div>
</template>

<script>
export default {
  props: ['error'],
  layout: 'blog' // you can set a custom layout for the error page
}
</script>
```

### Layout personalizado

Todo arquivo (*primeiro nível*) no diretório `layouts` criará um layout personalizado acessível com a propriedade` layout` no componente da página.

*Certifique-se de adicionar o componente `<nuxt/>` ao criar um layout para exibir o componente da página.*

Exemplo de `layouts/blog.vue`:
```html
<template>
  <div>
    <div>Minha barra de navegação está aqui.</div>
    <nuxt/>
  </div>
</template>
```

E então, em `pages / posts.vue`, você pode dizer ao Nuxt.js para usar seu layout personalizado:

```html
<script>
export default {
  layout: 'blog'
}
</script>
```

Mais informações sobre a propriedade `layout`: [Páginas da API` layout`](/api/pages-layout)

Confira o [vídeo de demonstração](https://www.youtube.com/watch?v=YOKnSTp7d38) para ver em ação.

## Páginas

Cada componente da página é um componente do Vue, mas o Nuxt.js adiciona chaves especiais para tornar o desenvolvimento do seu aplicativo universal da maneira mais fácil possível.
```html
<template>
  <h1 class="red">Hello {{ name }}!</h1>
</template>

<script>
export default {
  asyncData (context) {
    // called every time before loading the component
    return { name: 'World' }
  },
  fetch () {
    // The `fetch` method is used to fill the store before rendering the page
  },
  head () {
    // Set Meta Tags for this Page
  },
  // and more functionality to discover
  ...
}
</script>

<style>
.red {
  color: red;
}
</style>
```

| Atributo | Descrição |
|-----------|-------------|
| asyncData | A chava mais importante, ela pode ser assíncrona e receber o context como argumento, Por favor, leia [documentação dos dados assíncronos](/guide/async-data) para aprender como funciona. |
| fetch | Usado para preencher a loja antes de renderizar a página, é como método de dados exceto que não define os dados do componente. Veja [Documentação `fetch` das páginas da API](/api/pages-fetch). | 
| head | Define meta tags específicos para a página atual, consulte [Documentação `head` das Páginas da API](/api/pages-head). |
| layout | Especifica um layout definido no diretório `layouts`, veja [Documentação do `layout` das páginas da API](/api/pages-layout).|
| transition | Define uma transição específica para a página, consulte[Páginas de API `transition`](/api/pages-transition). |
| scrollToTop | Boolean, por padrão: `false`. Especifica se você deseja que a página role para o topo antes de renderizá-la, ela é usada para [rotas aninhadas](/guide/routing#nested-routes). |
| validate | Função Validator para [rotas dinâmicas](/guide/routing#dynamic-routes). |
| middleware | Defina um middleware para esta página, o middleware será chamado antes de renderizar a página, consulte as rotas [middleware](/guide/routing#middleware). |

Mais informações sobre o uso das propriedades das páginas: [Páginas da API](/api)

## HTML Head

O Nuxt.js usa o [vue-meta](https://github.com/nuxt/vue-meta) para atualizar os `headers` e` html attribute` da sua aplicação.

O Nuxt.js configura o `vue-meta` com estas opções:
```js
{
  keyName: 'head', // the component option name that vue-meta looks for meta info on.
  attribute: 'data-n-head', // the attribute name vue-meta adds to the tags it observes
  ssrAttribute: 'data-n-head-ssr', // the attribute name that lets vue-meta know that meta info has already been server-rendered
  tagIDKeyName: 'hid' // the property name that vue-meta uses to determine whether to overwrite or append a tag
}
```

### Meta Tags padrão

O Nuxt.js permite definir toda a meta padrão para sua aplicação dentro do `nuxt.config.js`, use a mesma propriedade` head`:

Exemplo de uma viewport personalizada com uma fonte personalizada do Google:

```js
head: {
  meta: [
    { charset: 'utf-8' },
    { name: 'viewport', content: 'width=device-width, initial-scale=1' }
  ],
  link: [
    { rel: 'stylesheet', href: 'https://fonts.googleapis.com/css?family=Roboto' }
  ]
}
```

Para conhecer a lista de opções que você pode dar ao `head`, dê uma olhada em [documentação vue-meta](https://vue-meta.nuxtjs.org/api/#metainfo-properties).

Mais informações sobre o método `head`: [Configuração da API` head`](/api/configuration-head).

### Meta Tags personalizadas para uma página

Mais informações sobre o método head: [Páginas da API `head`](/api/pages-head).

<div class="Alert">

Para evitar duplicação quando usado no componente filho, forneça um identificador exclusivo com a chave `hid`, por favor [leia mais sobre isso](https://vue-meta.nuxtjs.org/api/#tagidkeyname).

</div>
