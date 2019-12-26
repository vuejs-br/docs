---
title: Rotas de autenticação
description: Exemplo de rotas autenticadas com o Nuxt.js
github: auth-routes
livedemo: https://nuxt-auth-routes.gomix.me
liveedit: https://gomix.com/#!/project/nuxt-auth-routes
---

# Documentação

> O Nuxt.js pode ser usado para criar rotas autenticadas facilmente.

## Usando Express e Sessions

Para adicionar o recurso de sessões em nossa aplicação, usaremos `express` e ` express-session`, para isso, precisamos usar o Nuxt.js programaticamente.

Primeiro, instalamos as dependências:

```bash
adicionar acréscimo de analisador de corpo de sessão expressa expresso whatwg-fetch
```

*Falaremos sobre `whatwg-fetch` mais tarde.*

Então criamos nosso `server.js`:

```js
const { Nuxt, Builder } = require('nuxt')
const bodyParser = require('body-parser')
const session = require('express-session')
const app = require('express')()

// Body parser, to access `req.body`
app.use(bodyParser.json())

// Sessions to create `req.session`
app.use(session({
  secret: 'super-secret-key',
  resave: false,
  saveUninitialized: false,
  cookie: { maxAge: 60000 }
}))

// POST `/api/login` to log in the user and add him to the `req.session.authUser`
app.post('/api/login', function (req, res) {
  if (req.body.username === 'demo' && req.body.password === 'demo') {
    req.session.authUser = { username: 'demo' }
    return res.json({ username: 'demo' })
  }
  res.status(401).json({ error: 'Bad credentials' })
})

// POST `/api/logout` to log out the user and remove it from the `req.session`
app.post('/api/logout', function (req, res) {
  delete req.session.authUser
  res.json({ ok: true })
})

// We instantiate Nuxt.js with the options
const isProd = process.env.NODE_ENV === 'production'
const nuxt = new Nuxt({ dev: !isProd })
// No build in production
if (!isProd) {
  const builder = new Builder(nuxt)
  builder.build()
}
app.use(nuxt.render)
app.listen(3000)
console.log('Server is listening on http://localhost:3000')
```

E nós atualizamos nossos scripts `package.json`:

```json
// ...
"scripts": {
  "dev": "node server.js",
  "build": "nuxt build",
  "start": "cross-env NODE_ENV=production node server.js"
}
// ...
```

Nota: Você precisará executar o `npm install --save-dev cross-env` para que o exemplo acima funcione. Se você *não* está desenvolvendo no Windows, você pode deixar o env-cross fora do seu script `start` e configurar o` NODE_ENV` diretamente.

## Usando o store

Precisamos de um estado global para informar nossa aplicação se o usuário está conectado **nas páginas**.

Para permitir que o Nuxt.js use o Vuex, criamos um arquivo `store/index.js`:

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

// Polyfill for `window.fetch()`
require('whatwg-fetch')

const store = () => new Vuex.Store({

  state: {
    authUser: null
  },

  mutations: {
    SET_USER (state, user) {
      state.authUser = user
    }
  },

  actions: {
    // ...
  }

})

export default store
```

1. Importamos `Vue` e` Vuex` (incluído no Nuxt.js) e pedimos ao Vue para usar o Vuex para nos permitir usar `$store` em nossos componentes.
2. Nós exigimos que ('whatwg-fetch') preencha o método `fetch ()` em todos os navegadores (consulte [fetch repo](https://github.com/github/fetch)).
3.Criamos nossa mutação `SET_USER`, que definirá o` state.authUser` para o usuário conectado.
4.Exportamos nossa instância da store para o Nuxt.js e podemos injetá-la em nossa aplicação principal.

### nuxtServerInit() action

O Nuxt.js chamará uma ação específica chamada `nuxtServerInit` com o contexto no argumento, portanto, quando o aplicativo for carregado, a store já estará preenchida com alguns dados que podemos obter do servidor.

Em nosso `store/index.js`, podemos adicionar a ação` nuxtServerInit`:

```js
nuxtServerInit ({ commit }, { req }) {
  if (req.session && req.session.authUser) {
    commit('SET_USER', req.session.authUser)
  }
}
```

Para tornar o método dos dados assíncrono, o Nuxt.js oferece maneiras diferentes, escolha a que você mais conhece:

1. returning a `Promise`, Nuxt.js will wait for the promise to be resolved before rendering the component.
2. Using the [`async`/`await` proposal](https://github.com/lukehoban/ecmascript-asyncawait) ([learn more about it](https://zeit.co/blog/async-and-await)).

### login() action

Nós adicionamos uma ação `login` que será chamada do componente de nossas páginas para efetuar login no usuário:

```js
login ({ commit }, { username, password }) {
  return fetch('/api/login', {
    // Send the client cookies to the server
    credentials: 'same-origin',
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      username,
      password
    })
  })
  .then((res) => {
    if (res.status === 401) {
      throw new Error('Bad credentials')
    } else {
      return res.json()
    }
  })
  .then((authUser) => {
    commit('SET_USER', authUser)
  })
}
```

### logout() method

```js
logout ({ commit }) {
  return fetch('/api/logout', {
    // Send the client cookies to the server
    credentials: 'same-origin',
    method: 'POST'
  })
  .then(() => {
    commit('SET_USER', null)
  })
}
```

## Componentes de páginas

Em seguida, podemos usar `$store.state.authUser` nos componentes de nossas páginas para verificar se o usuário está conectado em nossa aplicação ou não.

### Redirecionar usuário se não estiver conectado

Vamos adicionar uma rota `/secret` onde apenas o usuário conectado pode ver seu conteúdo:

```html
<template>
  <div>
    <h1>Página Super secreta</h1>
    <router-link to="/">Voltar para a página inicial</router-link>
  </div>
</template>

<script>
export default {
  // we use fetch() because we do not need to set data to this component
  fetch ({ store, redirect }) {
    if (!store.state.authUser) {
      return redirect('/')
    }
  }
}
</script>
```

Podemos ver no método `fetch` que chamamos de `redirect('/') `quando nosso usuário não está conectado.
