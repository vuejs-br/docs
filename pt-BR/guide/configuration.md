---
title: Configuração
description: Por padrão, o Nuxt.js está configurado para cobrir a maioria dos casos de uso. Esta configuração padrão pode ser substituída usando o arquivo `nuxt.config.js`.
---

> Por padrão, o Nuxt.js está configurado para cobrir a maioria dos casos de uso. Esta configuração padrão pode ser substituída modificando o arquivo `nuxt.config.js`.

### build

Esta opção permite adicionar módulos dentro do arquivo `vendor.bundle.js` para reduzir o tamanho do pacote de aplicativos. Isso é especialmente útil ao usar módulos externos.
[Documentação sobre integração de `build`](/api/configuration-build)

### css

Esta opção permite definir os arquivos / módulos / bibliotecas CSS que você deseja definir como globais (incluídos em todas as páginas).

[Documentação sobre integração de `css`](/api/configuration-css)

### dev

Esta opção permite definir o modo `development` ou` production` do Nuxt.js

[Documentação sobre integração de `dev`](/api/configuration-dev)

### env

Esta opção permite definir variáveis ​​de ambiente disponíveis, tanto o cliente quanto o servidor.

[Documentação sobre integração `env`](/api/configuration-env)

### generate

Esta opção permite definir valores de parâmetros para cada rota dinâmica em sua aplicação que será transformada em arquivos HTML pelo Nuxt.js.

[Documentação sobre gerar integração](/api/configuration-generate)

### head

Essa opção permite definir todas as metatags padrão para sua aplicação.

[Documentação sobre integração de heads](/api/configuration-head)

### loading

Essa opção permite personalizar o componente de carregamento que o Nuxt.js carrega por padrão.

[Documentação sobre integração de `loading`](/api/configuration-loading)

### modules

Esta opção permite adicionar módulos Nuxt ao seu projeto.

[Documentação sobre integração de `modules`](/api/configuration-modules)

### plugins

Esta opção permite definir plugins JavaScript para serem executados antes de instanciar o Vue.js raiz.

[Documentação sobre integração de `plugins`](/api/configuration-plugins)

### rootDir

Esta opção permite definir o espaço de trabalho do Nuxt.js.

[Documentação sobre integração com `rootDir`](/api/configuration-rootdir)

### router

Esta opção permite substituir a configuração padrão do Nuxt.js. do Vue Router.

[Documentação sobre integração de `router`](/api/configuration-router)

### srcDir

Esta opção permite definir o diretório de origem do seu aplicativo Nuxt.js.

[Documentação sobre integração de srcDir](/api/configuration-srcdir)

### transition

Esta opção permite definir as propriedades padrão das transições de página.

[Documentação sobre integração de `transition`](/api/configuration-transition)
