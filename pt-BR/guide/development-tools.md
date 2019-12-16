---
title: Ferramentas de desenvolvimento
description: O Nuxt.js ajuda você a tornar seu desenvolvimento web agradável.
---

> Testar seu aplicação faz parte do desenvolvimento web. O Nuxt.js ajuda você a tornar o mais fácil possível.

## Teste de ponta-a-ponta

[AVA](https://github.com/avajs/ava)
é uma poderosa estrutura de teste de JavaScript, combinada com [jsdom](https://github.com/tmpvar/jsdom), podemos usá-los para fazer testes de ponta a ponta com facilidade.

Primeiro, precisamos adicionar AVA e jsdom como dependências de desenvolvimento:
```bash
npm install --save-dev ava jsdom
```

Em seguida, adicione um script de teste ao nosso `package.json` e configure o AVA para compilar arquivos que importamos para os nossos testes.

```javascript
"scripts": {
  "test": "ava",
},
"ava": {
  "require": [
    "babel-register"
  ]
},
"babel": {
  "presets": [
    "es2015"
  ]
}
```

Vamos escrever nossos testes na pasta `test`:

```bash
mkdir test
```

Digamos que temos uma página em `pages/index.vue`:

```html
<template>
  <h1 class="red">Hello {{ name }}!</h1>
</template>

<script>
export default {
  data () {
    return { name: 'world' }
  }
}
</script>

<style>
.red {
  color: red;
}
</style>
```

Quando lançamos nosso aplicativo com `npm run dev` e abrirmos http://localhost:3000, podemos ver nosso título em vermelho` Hello world! `.

Adicionamos nosso arquivo de teste`test/index.test.js`:

```js
import { resolve } from 'path'
import test from 'ava'
import { Nuxt, Builder } from 'nuxt'

// We keep a reference to Nuxt so we can close
// the server at the end of the test
let nuxt = null

// Init Nuxt.js and start listening on localhost:4000
test.before('Init Nuxt.js', async (t) => {
  const rootDir = resolve(__dirname, '..')
  let config = {}
  try { config = require(resolve(rootDir, 'nuxt.config.js')) } catch (e) {}
  config.rootDir = rootDir // project folder
  config.dev = false // production build
  nuxt = new Nuxt(config)
  await new Builder(nuxt).build()
  nuxt.listen(4000, 'localhost')
})

// Example of testing only generated html
test('Route / exits and render HTML', async (t) => {
  const context = {}
  const { html } = await nuxt.renderRoute('/', context)
  t.true(html.includes('<h1 class="red">Hello world!</h1>'))
})

// Example of testing via DOM checking
test('Route / exits and render HTML with CSS applied', async (t) => {
  const window = await nuxt.renderAndGetWindow('http://localhost:4000/')
  const element = window.document.querySelector('.red')
  t.not(element, null)
  t.is(element.textContent, 'Hello world!')
  t.is(element.className, 'red')
  t.is(window.getComputedStyle(element).color, 'red')
})

// Close the Nuxt server
test.after('Closing server', (t) => {
  nuxt.close()
})
```

Agora podemos iniciar nossos testes:

```bash
npm test
```
O jsdom tem algumas limitações porque não usa um navegador. No entanto, ele cobrirá a maioria dos nossos testes. Se você deseja usar um navegador para testar seu aplicativo, consulte [Nightwatch.js](http://nightwatchjs.org).

## ESLint

> ESLint é uma ótima ferramenta para manter seu código limpo

Você pode adicionar [ESLint](http://eslint.org) facilmente com o nuxt.js; primeiro, você precisa adicionar as dependências do npm:

```bash
npm install --save-dev babel-eslint eslint eslint-config-standard eslint-plugin-html eslint-plugin-promise eslint-plugin-standard
```

Em seguida, você pode configurar o ESLint através de um arquivo `.eslintrc.js` no diretório raiz do seu projeto:
```js
module.exports = {
  root: true,
  parser: 'babel-eslint',
  env: {
    browser: true,
    node: true
  },
  extends: 'standard',
  // required to lint *.vue files
  plugins: [
    'html'
  ],
  // add your custom rules here
  rules: {},
  globals: {}
}
```

Então, você pode adicionar um script `lint` no seu` package.json`:

```js
"scripts": {
  "lint": "eslint --ext .js,.vue --ignore-path .gitignore ."
}
```

Agora você pode iniciar:
```bash
npm run lint
```
O ESLint lintará todos os seus arquivos JavaScript e Vue enquanto ignora os arquivos ignorados definidos no seu `.gitignore`.

<div class="Alert Alert--orange">

Uma prática recomendada é adicionar também `" precommit ":" npm run lint "` em seu package.json para lintar seu código automaticamente antes de confirmar seu código.

</div>
