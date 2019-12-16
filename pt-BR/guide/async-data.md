---
title: Async Data
description: Você pode buscar dados e renderizá-los no server-side. O Nuxt.js adiciona um método `asyncData` para permitir que você lide com operações assíncronas antes de definir os dados do componente.
---

> Convém buscar dados e renderizá-los no server-side.
O Nuxt.js adiciona um método `asyncData` para permitir que você lide com operações assíncronas antes de definir os dados do componente.

<div>
  <a href="http://vueschool.io/?friend=nuxt" target="_blank" class="Promote">
    <img src="/async-data-with-nuxtjs.png" srcset="/async-data-with-nuxtjs-2x.png 2x" alt="AsyncData by vueschool"/>
    <div class="Promote__Content">
      <h4 class="Promote__Content__Title">Async Data com Nuxt.js</h4>
      <p class="Promote__Content__Description">Aprenda a gerenciar dados assíncronos com o Nuxt.js.</p>
      <p class="Promote__Content__Signature">Vídeo aulas feitas pela VueSchool para apoiar o desenvolvimento do Nuxt.js.</p>
    </div>
  </a>
</div>

## O método asyncData

Às vezes, você apenas deseja buscar dados e renderizá-los previamente no servidor sem usar um store. `asyncData` é chamadoo todas as vezes antes de carregar o componente (**apenas para componentes de páginas**).Pode ser chamado do servidor ou antes de navegar para a rota correspondente. Esse método recebe[the context](/api/context) as como primeiro argumento, você pode usá-lo para buscar alguns dados e o Nuxt.js os mesclará com os dados do componente. 

<div class="Alert Alert--orange">

Você **NÃO** tem acesso à instância do componente através de `this` dentro de` asyncData` porque é chamado **antes de iniciar** o componente.

</div>

O Nuxt.js oferece diferentes maneiras de usar o `asyncData`. Escolha o que você está mais familiarizado:

1. Retornando uma `Promise`. O Nuxt.js aguardará a promise ser resolvida antes de renderizar o componente.
2. Usando [async/await proposal](https://github.com/lukehoban/ecmascript-asyncawait) ([saiba mais sobre isso em](https://zeit.co/blog/async-and-await))
3. Defina um callback (retorno) de chamada como o segundo argumento. Ele deve ser chamado assim: `callback(err, data)`

### Retornando um Promise

```js
export default {
  asyncData ({ params }) {
    return axios.get(`https://my-api/posts/${params.id}`)
      .then((res) => {
        return { title: res.data.title }
      })
  }
}
```

### Usando async/await

```js
export default {
  async asyncData ({ params }) {
    const { data } = await axios.get(`https://my-api/posts/${params.id}`)
    return { title: data.title }
  }
}
```

### Usando o callback

```js
export default {
  asyncData ({ params }, callback) {
    axios.get(`https://my-api/posts/${params.id}`)
      .then((res) => {
        callback(null, { title: res.data.title })
      })
  }
}
```

### Exibindo os dados
O resultado de asyncData será **mesclado** com os dados.
Você pode exibir os dados dentro do seu modelo como você está acostumado a fazer:

```html
<template>
  <h1>{{ title }}</h1>
</template>
```

## O Context

Para ver a lista de chaves disponíveis no `context`, dê uma olhada no [API Essential `context`](/api/context).

### Acessando dados de rota dinâmica
Você pode usar o objeto de contexto injetado na propriedade `asyncData` para acessar dados de rota dinâmica. Por exemplo, os parâmetros de rota dinâmica podem ser acessados ​​usando o nome do arquivo ou pasta que o configurou. Portanto, se você definir um arquivo chamado `_slug.vue`, poderá acessá-lo via` context.params.slug`.

## Tratamento de erros
O Nuxt.js adiciona o método `error (params)` no `context`, você pode chamá-lo para exibir a página de erro. O `params.statusCode` também será usado para renderizar o código de status apropriado no server-side.

Exemplo com um `Promise`:

```js
export default {
  asyncData ({ params, error }) {
    return axios.get(`https://my-api/posts/${params.id}`)
      .then((res) => {
        return { title: res.data.title }
      })
      .catch((e) => {
        error({ statusCode: 404, message: 'Post not found' })
      })
  }
}
```
Se você estiver usando o argumento `callback`, poderá chamá-lo diretamente com o erro e o Nuxt.js chamará o método` error` para você:
```js
export default {
  asyncData ({ params }, callback) {
    axios.get(`https://my-api/posts/${params.id}`)
      .then((res) => {
        callback(null, { title: res.data.title })
      })
      .catch((e) => {
        callback({ statusCode: 404, message: 'Post not found' })
      })
  }
}
```
Para personalizar a página de erro, dê uma olhada no [Guide Views layouts](/guide/views#layouts).
