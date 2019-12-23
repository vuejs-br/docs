---
title: Plugins
description: O Nuxt.js permite definir plugins JavaScript a serem executados antes de instanciar o aplicativo Vue.js raiz. Isso é especialmente útil ao usar suas próprias bibliotecas ou módulos externos.
---

> O Nuxt.js permite definir plugins JavaScript a serem executados antes de instanciar o aplicativo raiz vue.js. Isso é especialmente útil ao usar suas próprias bibliotecas ou módulos externos.

<div class="Alert">

É importante saber que em qualquer Vue [ciclo de vida da instância](https://vuejs.org/v2/guide/instance.html#Lifecycle-Diagram), apenas os ganchos `beforeCreate` e` created` são chamados **ambos de do lado do cliente e do lado do servidor**. Todos os outros ganchos são chamados apenas no lado do cliente.

</div>

## Pacotes Externos

Podemos usar pacotes/módulos externos em nossa aplicação, um ótimo exemplo é o [axios](https://github.com/mzabriskie/axios) para fazer solicitações HTTP para servidor e cliente.

Nós o instalamos via npm:

```bash
npm install --save axios
```

Em seguida, podemos usá-lo diretamente em nossas páginas:

```html
<template>
  <h1>{{ title }}</h1>
</template>

<script>
import axios from 'axios'

export default {
  async asyncData ({ params }) {
    let { data } = await axios.get(`https://my-api/posts/${params.id}`)
    return { title: data.title }
  }
}
</script>
```

Mas há **um problema aqui**. Se importarmos axios em outra página, ela será incluída novamente no pacote de páginas. Queremos incluir `axios` apenas uma vez em nossa aplicação. Para fazer isso, usamos a chave `build.vendor` em nosso` nuxt.config.js`:

```js
module.exports = {
  build: {
    vendor: ['axios']
  }
}
```

Então, podemos importar `axios` em qualquer lugar sem ter que se preocupar em aumentar o pacote!

## Vue Plugins

Se quisermos usar [vue-Notifications](https://github.com/se-panfilov/vue-notifications) para exibir notificações em nosso aplicativo, precisamos configurar o plugin antes de iniciar a aplicação.
Arquivo `plugins/vue-notifications.js`:

```js
import Vue from 'vue'
import VueNotifications from 'vue-notifications'

Vue.use(VueNotifications)
```

Em seguida, adicionamos o arquivo dentro da chave `plugins` do
 `nuxt.config.js`:

```js
module.exports = {
  plugins: ['~/plugins/vue-notifications']
}
```

Para aprender mais sobre a chave de configuração `plugins`, consulte os [plugins api](/api/configuration-plugins).

Na verdade, `vue-Notifications` será incluído no pacote de aplicações, mas como é uma biblioteca, queremos incluí-lo no pacote de fornecedores para melhorar o armazenamento em cache.

Podemos atualizar nosso `nuxt.config.js` para adicionar` vue-Notifications` no pacote do fornecedor:

```js
module.exports = {
  build: {
    vendor: ['vue-notifications']
  },
  plugins: ['~/plugins/vue-notifications']
}
```

## Injetar em $root & context

Alguns plugins precisam ser injetados na raiz da aplicação a ser usada, como [vue-18n](https://github.com/kazupon/vue-i18n). Com o Nuxt.js, você pode usar o `app` disponível no` context` ao exportar um método:

`plugins/i18n.js`:

```js
import Vue from 'vue'
import VueI18n from 'vue-i18n'

Vue.use(VueI18n)

export default ({ app }, inject) => {
  // Set `i18n` instance on `app`
  // This way we can use it in middleware and pages `asyncData`/`fetch`
  app.i18n = new VueI18n({
    /* `VueI18n` options... */
  })
}
```

`nuxt.config.js`:

```js
module.exports = {
  build: {
    vendor: ['vue-i18n']
  },
  plugins: ['~/plugins/i18n.js']
}
```

Por favor, dê uma olhada no [exemplo do i18n](/examples/i18n) para ver como o usamos.

## Somente do lado do cliente (client-side)

Alguns plugins podem funcionar **apenas para o navegador**, você pode usar a opção `ssr: false` em` plugins` para executar o arquivo apenas no lado do cliente.

Exemplo:

`nuxt.config.js`:

```js
module.exports = {
  plugins: [
    { src: '~/plugins/vue-notifications', ssr: false }
  ]
}
```

`plugins/vue-notifications.js`:

```js
import Vue from 'vue'
import VueNotifications from 'vue-notifications'

Vue.use(VueNotifications)
```

Caso você precise exigir algumas bibliotecas apenas para o servidor, você pode usar a variável `process.server` definida como` true` quando o webpack estiver criando o arquivo `server.bundle.js`.

Além disso, se você precisa saber se está dentro de uma aplicação gerada (via `nuxt generate`), verifique` process.static`, definido como `true` durante a geração e depois. Para saber o estado em que uma página está sendo renderizada no servidor pelo `nuxt generate` antes de ser salva, você pode usar o` process.static && process.server`.
