---
title: Módulos
description: Módulos são extensões do Nuxt.js que podem ampliar sua funcionalidade principal e adicionar integrações infinitas.
---

> Módulos são extensões do Nuxt.js que podem ampliar sua funcionalidade principal e adicionar integrações infinitas.

## Introdução

Ao desenvolver aplicações de nível de produção com o Nuxt, você logo descobre que a principal funcionalidade da estrutura não é suficiente. O Nuxt pode ser estendido com opções de configuração e plugins, mas manter essas personalizações em vários projetos é entediante, repetitivo e demorado. Por outro lado, dar suporte imediato às necessidades de todos os projetos tornaria o Nuxt muito complexo e difícil de usar.

É por isso que o Nuxt fornece um sistema de módulo de ordem superior que facilita a extensão do núcleo. Módulos são simplesmente **funções** que são chamadas sequêncialmente ao inicializar o Nuxt. A estrutura aguarda a conclusão de cada módulo antes de continuar. Dessa forma, os módulos podem personalizar praticamente qualquer aspecto do Nuxt. Graças ao design modular do Nuxt (baseado no webpack do [Tapable](https://github.com/webpack/tapable)), os módulos podem registrar facilmente ganchos para determinados pontos de entrada, como a inicialização do construtor. Os módulos também podem substituir modelos, configurar carregadores de webpack, adicionar bibliotecas CSS e executar várias outras tarefas úteis.

O melhor de tudo é que os módulos Nuxt podem ser incorporados aos pacotes npm. Isso facilita a reutilização em projetos e o compartilhamento com a comunidade Nuxt, ajudando a criar um ecossistema de complementos Nuxt de alta qualidade.

Os módulos são ótimos se você:

- É membro de uma **equipe ágil** que precisa iniciar rapidamente novos projetos.
- Está cansado de **inventar moda** para tarefas comuns como a integração do Google Analytics.
- É um adorável entusiasta do **open source** que gostaria de compartilhar facilmente seu trabalho com a comunidade.
- É membro de uma **empresa** que valoriza **qualidade** e **reutilização**.
- Frequentemente enfrentamprazos curtos e não têm tempo para se aprofundar nos detalhes de cada nova biblioteca ou integração.
- Está cansado de lidar com as mudanças recentes nas interfaces de baixo nível e precisa de coisas que **simplesmente funcionem**.

## Escreva um módulo básico

Como os módulos já mencionados, são apenas funções simples. Eles podem ser empacotados como módulos npm ou incluídos diretamente no código-fonte do projeto.
**modules/simple.js**

```js
module.exports = function SimpleModule (moduleOptions) {
  // Write your code here
}

// REQUIRED if publishing as an npm package
// module.exports.meta = require('./package.json')
```

**`moduleOptions`**

Este é o objeto passado usando o array `modules` pelo usuário, podemos usá-lo para personalizar seu comportamento.

**`this.options`**

Você pode acessar diretamente as opções do Nuxt usando esta referência. Este é o `nuxt.config.js` com todas as opções padrão atribuídas e podem ser usadas para opções compartilhadas entre os módulos.

**`this.nuxt`**

Esta é uma referência à instância atual do Nuxt. Referir-se[Documentos da classe Nuxt para métodos disponíveis](/api/internals-nuxt).

**`this`**
Contexto dos módulos. Consulte os documentos da classe [ModuleContainer](/api/internals-module-container) para obter os métodos disponíveis.

**`module.exports.meta`**

Essa linha é **necessária** se você estiver publicando o módulo como um pacote npm. O Nuxt usa internamente o meta para funcionar melhor com seu pacote.

**nuxt.config.js**

```js
module.exports = {
  modules: [
    // Simple usage
    '~/modules/simple'

    // Passing options
      ['~/modules/simple', { token: '123' }]
  ]
}
```

Em seguida, dizemos ao Nuxt para carregar alguns módulos específicos para um projeto com parâmetros opcionais como opções. Por favor, consulte a documentação [modules configuration](/api/configuration-modules) para obter mais informações!

## Módulos assíncronos

Nem todos os módulos farão tudo de forma síncrona. Por exemplo, você pode desenvolver um módulo que precise buscar alguma API ou executar E/S assíncrona. Para isso, o Nuxt suporta módulos assíncronos que podem retornar uma promessa ou chamar um retorno de chamada.

### Usando async/await

<div class="Alert Alert--orange">

Esteja ciente de que `async`/`await` é suportado apenas no Node.js.> 7.2. Portanto, se você é um desenvolvedor de módulos, pelo menos avise os usuários sobre isso se os estiver usando. Para módulos altamente assíncronos ou melhor suporte a legados, você pode usar um bundler para transformá-lo para uma comparação mais antiga do Node.js. ou usando o método promise.

</div>

```js
const fse = require('fs-extra')

module.exports = async function asyncModule () {
  // You can do async works here using `async`/`await`
  const pages = await fse.readJson('./pages.json')
}
```

### Retornando um Promise

```js
const axios = require('axios')

module.exports = function asyncModule () {
  return axios.get('https://jsonplaceholder.typicode.com/users')
    .then(res => res.data.map(user => '/users/' + user.username))
    .then((routes) => {
      // Do something by extending Nuxt routes
    })
}
```

### Usando callbacks

```js
const axios = require('axios')

module.exports = function asyncModule (callback) {
  axios.get('https://jsonplaceholder.typicode.com/users')
    .then(res => res.data.map(user => '/users/' + user.username))
    .then((routes) => {
      callback()
    })
}
```


## Snippets comuns

### Opções de nível superior

Às vezes é mais conveniente se podemos usar as opções de nível superior enquanto registramos os módulos no `nuxt.config.js`. Assim, podemos combinar fontes de opções de multiplicação.

**nuxt.config.js**

```js
module.exports = {
  modules: [
    '@nuxtjs/axios'
  ],

  // axios module is aware of this by using `this.options.axios`
  axios: {
    option1,
    option2
  }
}
```

**module.js**

```js
module.exports = function (moduleOptions) {
  const options = Object.assign({}, this.options.axios, moduleOptions)
  // ...
}
```

### Fornecer plugins

É comum que os módulos forneçam um ou mais plugins quando adicionados. Por exemplo, o módulo [bootstrap-vue](https://bootstrap-vue.js.org) exigiria o registro no Vue. Para isso, podemos usar o auxiliar `this.addPlugin`.

**plugin.js**

```js
import Vue from 'vue'
import BootstrapVue from 'bootstrap-vue/dist/bootstrap-vue.esm'

Vue.use(BootstrapVue)
```

**module.js**

```js
const path = require('path')

module.exports = function nuxtBootstrapVue (moduleOptions) {
  // Register `plugin.js` template
  this.addPlugin(path.resolve(__dirname, 'plugin.js'))
}
```

### Plugins de templates

Templates e plugins registrados podem aproveitar os [templates de lodash](https://lodash.com/docs/4.17.4#template) para alterar condicionalmente a saída de plugins registrados.

**plugin.js**

```js
// Set Google Analytics UA
ga('create', '<%= options.ua %>', 'auto')

<% if (options.debug) { %>
// Dev only code
<% } %>
```

**module.js**

```js
const path = require('path')

module.exports = function nuxtBootstrapVue (moduleOptions) {
  // Register `plugin.js` template
  this.addPlugin({
    src: path.resolve(__dirname, 'plugin.js'),
    options: {
      // Nuxt will replace `options.ua` with `123` when copying plugin to project
      ua: 123,

      // conditional parts with dev will be stripped from plugin code on production builds
      debug: this.options.dev
    }
  })
}
```

### Adicionar uma biblioteca CSS

É recomendável verificar se o usuário já não forneceu a mesma biblioteca para evitar a adição de duplicatas. Também sempre considere ter **uma opção para desativar** adicionar arquivos css por módulo.

**module.js**

```js
module.exports = function (moduleOptions) {
  if (moduleOptions.fontAwesome !== false) {
    // Add Font Awesome
    this.options.css.push('font-awesome/css/font-awesome.css')
  }
}
```

### Emitir assets

Podemos registrar plugins do webpack para emitir ativos durante a compilação.

**module.js**

```js
module.exports = function (moduleOptions) {
  const info = 'Built by awesome module - 1.3 alpha on ' + Date.now()

  this.options.build.plugins.push({
    apply (compiler) {
      compiler.plugin('emit', (compilation, cb) => {
        // This will generate `.nuxt/dist/info.txt' with contents of info variable.
        // Source can be buffer too
        compilation.assets['info.txt'] = { source: () => info, size: () => info.length }

        cb()
      })
    }
  })
}
```

### Registrar loaders personalizados

Podemos fazer o mesmo que `build.extend` em` nuxt.config.js` usando `this.extendBuild`.

**module.js**

```js
module.exports = function (moduleOptions) {
    this.extendBuild((config, { isClient, isServer }) => {
      // `.foo` Loader
      config.module.rules.push({
        test: /\.foo$/,
        use: [...]
      })

      // Customize existing loaders
      // Refer to source code for Nuxt internals:
      // https://github.com/nuxt/nuxt.js/tree/dev/packages/builder/src/webpack/base.js
      const barLoader = config.module.rules.find(rule => rule.loader === 'bar-loader')
  })
}
```

## Executar tarefas em ganchos específicos

Seu módulo pode precisar fazer as coisas apenas em condições específicas, não apenas durante a inicialização do Nuxt. Podemos usar o poderoso sistema de plugins [Tapable](https://github.com/webpack/tapable) para executar tarefas em eventos específicos. O Nuxt nos aguardará se os ganchos retornarem uma promessa ou forem definidos como `assíncronos`.

```js
module.exports = function () {
  // Add hook for modules
  this.nuxt.plugin('module', (moduleContainer) => {
    // This will be called when all modules finished loading
  })

  // Add hook for renderer
  this.nuxt.plugin('renderer', (renderer) => {
    // This will be called when renderer was created
  })

  // Add hook for build
  this.nuxt.plugin('build', async (builder) => {
    // This will be called once when builder created

    // We can even register internal hooks here
    builder.plugin('compile', ({ compiler }) => {
      // This will be run just before webpack compiler starts
    })
  })

  // Add hook for generate
  this.nuxt.plugin('generate', async (generator) => {
    // This will be called when a Nuxt generate starts
  })
}
```

<div class="Alert">

Existem muitos outros ganchos e possibilidades para módulos. Por favor, consulte [Nuxt Internals](/api/internals) para saber mais sobre a API interna do Nuxt.

</div>
