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

Se quisermos usar [vue-Notifications](https://github.com/se-panfilov/vue-notifications) para exibir notificações em nosso aplicativo, precisamos configurar o plugin antes de iniciar o aplicação.
Arquivo `plugins/vue-notifications.js`:

```js
import Vue from 'vue'
import VueNotifications from 'vue-notifications'

Vue.use(VueNotifications)
```

Then, we add the file inside the `plugins` key of `nuxt.config.js`:

```js
module.exports = {
  plugins: ['~/plugins/vue-notifications']
}
```

To learn more about the `plugins` configuration key, check out the [plugins api](/api/configuration-plugins).

Actually, `vue-notifications` will be included in the app bundle, but because it's a library, we want to include it in the vendor bundle for better caching.

We can update our `nuxt.config.js` to add `vue-notifications` in the vendor bundle:

```js
module.exports = {
  build: {
    vendor: ['vue-notifications']
  },
  plugins: ['~/plugins/vue-notifications']
}
```

## Inject in $root & context

Some plugins need to be injected in the App root to be used, like [vue-18n](https://github.com/kazupon/vue-i18n). With Nuxt.js, you can use `app` available into the `context` when exporting a method:

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

Please take a look at the [i18n example](/examples/i18n) to see how we use it.

## Client-side only

Some plugins might work **only for the browser**, you can use the `ssr: false` option in `plugins` to run the file only on the client-side.

Example:

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

In case you need to require some libraries only for the server, you can use the `process.server` variable set to `true` when webpack is creating the `server.bundle.js` file.

Also, if you need to know if you are inside a generated app (via `nuxt generate`), you can check `process.static`, set to `true` during generation and after. To know the state when a page is being server-rendered by `nuxt generate` before being saved, you can use `process.static && process.server`.
