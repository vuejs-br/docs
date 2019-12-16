---
title: Comandos e implantação
description: O Nuxt.js vem com um conjunto de comandos úteis, tanto para fins de desenvolvimento quanto de produção.
---

> O Nuxt.js vem com um conjunto de comandos úteis, tanto para fins de desenvolvimento quanto de produção.

## Lista de Comandos

| Comando         | Descrição                                                                            |
|-----------------|------------------------------------------------------------------------------------------|
| nuxt            | Inicia o servidor de desenvolvimento no localhost:3000 hot-reloading.                        |
| nuxt build      | Crie sua aplicaçãoBuild com o webpack e reduza o JS & CSS (para produção).           |
| nuxt start      | Inicie o servidor no modo de produção (depois de executar o `nuxt build`).                        |
| nuxt generate   | Crie a aplicação e gere todas as rotas como um arquivo HTML (usado para hospedagem estática). |

#### Argumentos

Você pode usar `--help` com qualquer comando para obter um uso detalhado. Os argumentos comuns são:
- **`--config-file` ou` -c`: ** especifique o caminho para o arquivo `nuxt.config.js`.
- ** `--spa` ou` -s`: ** Executa o comando no modo SPA, desativando a renderização no servidor.
- **`--config-file` or `-c`:** especifique o caminho para o arquivo `nuxt.config.js` .
- **`--spa` or `-s`:** Executa o comando no modo SPA, desativando a renderização no servidor.

#### Usando o package.json

Você deve colocar esses comandos no `package.json`:

```json
"scripts": {
  "dev": "nuxt",
  "build": "nuxt build",
  "start": "nuxt start",
  "generate": "nuxt generate"
}
```

Em seguida, você pode iniciar seus comandos via`npm run <command>` (exemplo: `npm run dev`).

<div class="Alert Alert--nuxt-green">
<b> Dica profissional: </b> para passar argumentos para os comandos npm, você precisa de um <code> -- </code> nome de script extra (exemplo: <code> npm run dev -- --spa </code>)

</div>

## Ambiente de desenvolvimento

Para iniciar o Nuxt no modo de desenvolvimento com recarga a quente:

```bash
nuxt
// OU
npm run dev
```

## Implantação de produção
O Nuxt.js permite que você escolha entre três modos de implantar seu aplicativo: servidor renderizado, SPA ou estático gerado.

### Implantação renderizada pelo servidor (Universal)
Para implantar, em vez de executar o `nuxt`, você provavelmente deseja construir com antecedência. Portanto, construir e iniciar são comandos separados:

```bash
nuxt build
nuxt start
```

O `package.json` como a seguir é recomendado:

```json
{
  "name": "my-app",
  "dependencies": {
    "nuxt": "latest"
  },
  "scripts": {
    "dev": "nuxt",
    "build": "nuxt build",
    "start": "nuxt start"
  }
}
```

Nota: recomendamos colocar `.nuxt` em` .npmignore` ou `.gitignore`.

### Implantação gerada estática (pré-renderizada)

O Nuxt.js oferece a capacidade de hospedar sua Aplicação Web em qualquer hospedagem estática.

Para gerar sua aplicação web em arquivos estáticos:

```bash
npm run generate
```

Ele criará uma pasta `dist` com tudo pronto para ser implantado em um site estático de hospedagem.

Se você tem um projeto com [rotas dinâmicas](/guide/routing#dynamic-routes), dê uma olhada em[generate configuration](/api/configuration-generate) para informar o Nuxt.js como gerar essas rotas dinâmicas.

<div class="Alert">

Ao gerar sua aplicação com `nuxt generate`, [the context](/api/context) dado a [data()](/guide/async-data#the-data-method) e [fetch()](/guide/vuex-store#the-fetch-method) não terá `req` e `res`.

</div>

### Desenvolimento de Single Page Application (SPA)

O `nuxt generate` ainda precisa do mecanismo SSR durante o tempo de criação, além de ter a vantagem de ter todas as nossas páginas pré renderizadas e ter uma alta pontuação de SEO e de carregamento da página. O conteúdo é gerado no *build time*. Por exemplo, não podemos usá-lo para aplicações em que o conteúdo depende da autenticação do usuário ou de uma API em tempo real (pelo menos para o primeiro carregamento).

A ideia do SPA é simples! Quando o modo SPA é ativado usando o sinalizador `mode: 'spa'` ou` --spa`, e executamos a compilação,a criação inicia automaticamente após a compilação. Essa geração contém links comuns de meta e recurso, mas não o conteúdo da página.

Portanto, para uma implantação de SPA, você deve fazer o seguinte:

- Altere `mode` em` nuxt.config.js` para `spa`.
- Execute o `npm run build`.
- Implante a pasta `dist /` criada na sua hospedagem estática, como Surge, GitHub Pages ou nginx.

Outro método de implantação possível é usar o Nuxt como um middleware em estruturas enquanto estiver no modo `spa`. Isso ajuda a reduzir a carga do servidor e usa o Nuxt em projetos onde o SSR não é possível.

<div class="Alert">

Veja [Como implantar no Heroku?](/faq/heroku-deployment) para exemplos de implantação em hosts populares.

</div>
