# docker-laravel-nuxt-template

This repository is a template for easily creating a project that separates the frontend and backend environments of Laravel and Nuxt in the docker environment.

## Dependency

- docker
- Laravel
- Nuxt

## Setup

1. Run setup.sh Then modify the .env file accordingly.
   ```
   sh setup.sh
   ```
1. Create a Nuxt project
   Change options as appropriate for your project

   ```
   $ yarn create nuxt-app nuxt
   yarn create v1.22.18
   [1/4] 🔍  Resolving packages...
   [2/4] 🚚  Fetching packages...
   [3/4] 🔗  Linking dependencies...
   [4/4] 🔨  Building fresh packages...
   [##################################################################################################################################] 343/343
   create-nuxt-app v4.0.0
   ✨  Generating Nuxt.js project in nuxt
   ? Project name: nuxt
   ? Programming language: JavaScript
   ? Package manager: Yarn
   ? UI framework: Vuetify.js
   ? Nuxt.js modules: Axios - Promise based HTTP client
   ? Linting tools: ESLint, Prettier
   ? Testing framework: None
   ? Rendering mode: Universal (SSR / SSG)
   ? Deployment target: Server (Node.js hosting)
   ? Development tools: jsconfig.json (Recommended for VS Code if you're not using typescript)
   ? Continuous integration: None
   ? Version control system: Git
   warning nuxt > @nuxt/babel-preset-app > core-js@2.6.12: core-js@<3.4 is no longer maintained and not recommended for usage due to the number
   of issues. Because of the V8 engin
   ```

1. Modified package.json for container startup

   ```diff_javascript:nuxt/package.json
   "scripts": {
   -   "dev": "nuxt",
   +   "dev": "HOST=0.0.0.0 PORT=3000 nuxt",
       "build": "nuxt build",
   -   "start": "nuxt start",
   +   "start": "HOST=0.0.0.0 PORT=3000 nuxt start",
       "generate": "nuxt generate"
   },
   ```

1. Create a Laravel project under the laravel directory

   ```
   docker-compose run app composer create-project laravel/laravel:^8.0 .
   ```

1. Docker start up

   ```
   docker-compose up -d --build
   ```

1. Clear cache of Laravel config file

   ```
   $ docker-compose exec app ash
   $ php artisan -V   // バージョンが表示されればOK
   $ php artisan config:cache
   $ php artisan migrate
   $ chmod -R a+rw storage/ bootstrap/cache
   $ exit
   ```

1. Start Nuxt
   If you want to start the container when Docker starts,change the entry point of Docker file of front_app

   ```
   docker-compose exec front_app yarn dev
   ```

## Usage

Since Laravel is running To start Nuxt, execute the following command.

    docker-compose exec front_app yarn dev

### Communication settings between Nuxt and Laravel

You can set the communication settings between Nuxt and Laravel by making the following settings.

1. Install proxy on Nuxt

   ```
   cd nuxt
   yarn add @nuxtjs/proxy
   ```

1. nuxt.config.js to settings

   ```vue:nuxt/nuxt.config.js
   axios: {
       credentials: true,
       proxy: true,
   },

   proxy: {
       '/api/': { target: 'http://web' },
       '/auth/': { target: 'http://web', pathRewrite: { '^/auth/': '' } },
   },
   ```

## reference

- [nuxt/axios](https://axios.nuxtjs.org/options/)
- [【備忘録】ゼロから Docker で Laravel+Nuxt TypeScript+MySQL の環境構築をしてトップページを表示するまで](https://qiita.com/kaba_farm/items/8d8b8faab808a55d3232)
