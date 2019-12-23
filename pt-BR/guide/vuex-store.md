---
title: Vuex Store
description: Usar uma store para gerenciar o estado é importante para todos os grandes aplicativos; é por isso que o Nuxt.js implementa o Vuex em seu núcleo.
---

> O uso de uma store para gerenciar o estado é importante para todos os grandes aplicativos; é por isso que o nuxt.js implementa o [Vuex] (https://vuex.vuejs.org/en/) em seu núcleo.

## Ativar a Store

O Nuxt.js procurará o diretório `store`, se existir, irá:

1. Importar o Vuex,
2. Adicione o módulo `vuex` ao pacote de fornecedores,
3. Adicione a opção `store` à instância raiz do Vue.


O Nuxt.js permite que você tenha **2 modos de store**, escolha o que preferir:

- **Clássico:** `store / index.js` retorna uma instância da loja.
- **Módulos:** todo arquivo `.js` dentro do diretório` store` é transformado como um [módulo namespaced](http://vuex.vuejs.org/en/modules.html) (`index` sendo a raiz módulo).

## Modo clássico

Para ativar a store no modo clássico, criamos o arquivo `store / index.js` que deve exportar um método que retorna uma instância do Vuex:

```js
import Vuex from 'vuex'

const createStore = () => {
  return new Vuex.Store({
    state: {
      counter: 0
    },
    mutations: {
      increment (state) {
        state.counter++
      }
    }
  })
}

export default createStore
```

> Não precisamos instalar o `vuex`, pois ele é enviado com o Nuxt.js.

Agora podemos usar `this. $store` dentro de nossos componentes:

```html
<template>
  <button @click="$store.commit('increment')">{{ $store.state.counter }}</button>
</template>
```

## Modo módulos

> O Nuxt.js permite que você tenha um diretório `store` com todos os arquivos correspondentes a um módulo.

Se você quiser esta opção, exporte o estado como uma função e as mutações e ações como objetos em `store / index.js` em vez de uma instância da store:

```js
export const state = () => ({
  counter: 0
})

export const mutations = {
  increment (state) {
    state.counter++
  }
}
```

Então, você pode ter um arquivo `store / todos.js`:

```js
export const state = () => ({
  list: []
})

export const mutations = {
  add (state, text) {
    state.list.push({
      text,
      done: false
    })
  },
  remove (state, { todo }) {
    state.list.splice(state.list.indexOf(todo), 1)
  },
  toggle (state, todo) {
    todo.done = !todo.done
  }
}
```

A store será assim:

```js
new Vuex.Store({
  state: { counter: 0 },
  mutations: {
    increment (state) {
      state.counter++
    }
  },
  modules: {
    todos: {
      state: {
        list: []
      },
      mutations: {
        add (state, { text }) {
          state.list.push({
            text,
            done: false
          })
        },
        remove (state, { todo }) {
          state.list.splice(state.list.indexOf(todo), 1)
        },
        toggle (state, { todo }) {
          todo.done = !todo.done
        }
      }
    }
  }
})
```

E no seu `pages / todos.vue`, usando o módulo` todos`:

```html
<template>
  <ul>
    <li v-for="todo in todos">
      <input type="checkbox" :checked="todo.done" @change="toggle(todo)">
      <span :class="{ done: todo.done }">{{ todo.text }}</span>
    </li>
    <li><input placeholder="O que precisa ser feito?" @keyup.enter="addTodo"></li>
  </ul>
</template>

<script>
import { mapMutations } from 'vuex'

export default {
  computed: {
    todos () { return this.$store.state.todos.list }
  },
  methods: {
    addTodo (e) {
      this.$store.commit('todos/add', e.target.value)
      e.target.value = ''
    },
    ...mapMutations({
      toggle: 'todos/toggle'
    })
  }
}
</script>

<style>
.done {
  text-decoration: line-through;
}
</style>
```

<div class="Alert">

Você também pode ter módulos exportando uma instância da store, precisará adicioná-los manualmente em sua store.

</div>

### Plugins

Você pode adicionar plugins adicionais à store (no Modo de módulos), colocando-o no arquivo `store/index.js`:

```js
import myPlugin from 'myPlugin'

export const plugins = [ myPlugin ]

export const state = () => ({
  counter: 0
})

export const mutations = {
  increment (state) {
    state.counter++
  }
}
```

Mais informações sobre os plugins: [documentação da Vuex](https://vuex.vuejs.org/en/plugins.html).

## O método de busca

> O método `fetch` é usado para preencher a loja antes de renderizar a página, é como o método `data`, exceto que não define os dados do componente.

Mais informações sobre o método de busca: [Busca de páginas da API](/api/pages-fetch).

## A ação nuxtServerInit

Se a ação `nuxtServerInit` for definida na store, o Nuxt.js a chamará com o contexto (somente no lado do servidor). É útil quando temos alguns dados no servidor que queremos fornecer diretamente ao lado do cliente.

Por exemplo, digamos que temos sessões no lado do servidor e podemos acessar o usuário conectado através de `req.session.user`. Para dar o usuário autenticado à nossa loja, atualizamos nosso `store/index.js` para o seguinte:

```js
actions: {
  nuxtServerInit ({ commit }, { req }) {
    if (req.session.user) {
      commit('user', req.session.user)
    }
  }
}
```

> Se você estiver usando o modo _Modules_ da store Vuex, somente o módulo principal (em `store/index.js`) receberá esta ação. Você precisará encadear suas ações do módulo a partir daí.

O contexto (context) é dado ao `nuxtServerInit` como o segundo argumento, é o mesmo que o método` data` ou `fetch`, exceto pelo fato de que` context.redirect () ` e `context.error ()` são omitidos.

> Nota: As ações assíncronas do `nuxtServerInit` devem retornar uma promessa (promise) para permitir que o servidor do` nuxt` os aguarde.
