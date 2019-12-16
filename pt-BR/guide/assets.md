---
title: Assets
description: Por padrão, o Nuxt usa os carregadores de webpack do vue-loader, do file-loader e do url-loader para fornecer serviços sólidos. Você também pode usar o diretório estático para componentes estáticos.
---

> Por padrão, o Nuxt usa os carregadores de webpack do vue-loader, do file-loader e do url-loader para fornecer serviços sólidos. Você também pode usar o diretório estático para componentes estáticos.

## webpacked
Por padrão, o [vue-loader] (http://vue-loader.vuejs.org/en/) processa automaticamente seus arquivos de estilo e modelo com o css-loader e o compilador de templates Vue. Neste processo de compilação, todas as URLs de componetes como `<img src =" ... ">`, `background: url (...)` e CSS `@ import` são transfomradas como dependências do módulo.

<!-- By default, [vue-loader](http://vue-loader.vuejs.org/en/) automatically processes your style and template files with css-loader and the Vue template compiler. In this compilation process, all asset URLs such as `<img src="...">`, `background: url(...)` and CSS `@import` are resolved as module dependencies. -->

Por exemplo, temos esta árvore de arquivos:

```bash
-| assets/
----| image.png
-| pages/
----| index.vue
```
No seu CSS, se você usar `url ('~ / assets / image.png')`, ele será traduzido para `require ('~ / assets / image.png')`.
<!-- In your CSS, if you use `url('~/assets/image.png')`, it will be translated into `require('~/assets/image.png')`. -->

Ou se em seu `pages / index.vue` você usa:

```html
<template>
  <img src="~/assets/image.png">
</template>
```

Será compilado em:

```js
createElement('img', { attrs: { src: require('~/assets/image.png') } })
```
Como `.png` não é um arquivo JavaScript, o Nuxt.js configura o webpack para usar o [file-loader] (https://github.com/webpack/file-loader) e o [url-loader] (https: // github .com / webpack / url-loader) para lidar com isso para você.
<!-- Because `.png` is not a JavaScript file, Nuxt.js configures webpack to use [file-loader](https://github.com/webpack/file-loader) and [url-loader](https://github.com/webpack/url-loader) to handle them for you. -->

Os benefícios do uso do file-loader e do url-loader são:

- file-loader permite designar onde copiar e colocar um arquivo de componentes e como nomeá-lo usando hashes de versão para melhor armazenamento em cache.
- url-loader permite alinhar condicionalmente um arquivo como URL de dados da base 64 se eles forem menores que um determinado limite. Isso pode reduzir um número de solicitações HTTP para arquivos triviais. Se o arquivo for maior que o limite, ele retornará automaticamente ao carregador de arquivos.

Na verdade, a configuração padrão dos carregadores de componentes do Nuxt.js é:


```js
[
  {
    test: /\.(png|jpe?g|gif|svg)$/,
    loader: 'url-loader',
    query: {
      limit: 1000, // 1KO
      name: 'img/[name].[hash:7].[ext]'
    }
  },
  {
    test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
    loader: 'url-loader',
    query: {
      limit: 1000, // 1kB
      name: 'fonts/[name].[hash:7].[ext]'
    }
  }
]
```
O que significa que todos os arquivos abaixo de 1 KO serão incorporados como URL de dados da base 64. Caso contrário, a imagem / fonte será copiada em sua pasta correspondente (no diretório `.nuxt`) com um nome que contenha hashes de versão para melhor armazenamento em cache.

Ao iniciar nosso aplicativo com `nuxt`, nosso modelo em` pages / index.vue`:
<!-- Which means that every file below 1 KO will be inlined as base-64 data URL. Otherwise, the image/font will be copied in its corresponding folder (under the `.nuxt` directory) with a name containing a version hashes for better caching.

When launching our application with `nuxt`, our template in `pages/index.vue`: -->

```html
<template>
  <img src="~/assets/image.png">
</template>
```

Será gerado em:

```html
<img src="/_nuxt/img/image.0c61159.png">
```

Se você deseja atualizar esses loaders ou desativá-los, use [build.extend] (/ api / configuration-build # extend).

## Static
Se você não quiser usar componentes compactados na web no diretório `assets`, poderá criar e usar o diretório` static` no diretório raiz do projeto.

Esses arquivos serão exibidos automaticamente pelo Nuxt e acessíveis no URL raiz do seu projeto.

Esta opção é útil para arquivos como `robots.txt`,` sitemap.xml` ou `CNAME` (para páginas do GitHub semelhantes).

No seu código, você pode fazer referência a esses arquivos com URLs `/`:
<!-- If you don't want to use webpacked Assets from the `assets` directory, you can create and use the `static` directory in your project root directory.

These files will be automatically serve by Nuxt and accessible in your project root URL.

This option is helpful for files like `robots.txt`, `sitemap.xml` or `CNAME` (for like GitHub Pages).

From your code you can then reference those files with `/` URLs: -->

```html
<!-- Static image from static directory -->
<img src="/my-image.png"/>

<!-- webpacked image from assets directory -->
<img src="~/assets/my-image-2.png"/>
```
