# Asset Bundling (Vite)
# 에셋 번들링 (Vite)

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation & Setup](#installation)
- [설치 & 설정](#installation)
    - [Installing Node](#installing-node)
    - [Node 설치](#installing-node)
    - [Installing Vite And The Laravel Plugin](#installing-vite-and-laravel-plugin)
    - [Vite와 라라벨 플러그인 설치](#installing-vite-and-laravel-plugin)
    - [Configuring Vite](#configuring-vite)
    - [Vite 설정](#configuring-vite)
    - [Loading Your Scripts And Styles](#loading-your-scripts-and-styles)
    - [스크립트와 스타일 불러오기](#loading-your-scripts-and-styles)
- [Running Vite](#running-vite)
- [Vite 실행](#running-vite)
- [Working With JavaScript](#working-with-scripts)
- [자바스크립트 작업](#working-with-scripts)
    - [Aliases](#aliases)
    - [별칭](#aliases)
    - [Vue](#vue)
    - [Vue](#vue)
    - [React](#react)
    - [React](#react)
    - [Inertia](#inertia)
    - [Inertia](#inertia)
    - [URL Processing](#url-processing)
    - [URL Processing](#url-processing)
- [Working With Stylesheets](#working-with-stylesheets)
- [스타일시트 작업](#working-with-stylesheets)
- [Working With Blade & Routes](#working-with-blade-and-routes)
- [블레이드 및 라우트 작업](#working-with-blade-and-routes)
    - [Processing Static Assets With Vite](#blade-processing-static-assets)
    - [Vite로 정적 에셋 처리](#blade-processing-static-assets)
    - [Refreshing On Save](#blade-refreshing-on-save)
    - [저장시 새로고침](#blade-refreshing-on-save)
    - [Aliases](#blade-aliases)
    - [별칭](#blade-aliases)
- [Custom Base URLs](#custom-base-urls)
- [커스텀 정의 기본 URL](#custom-base-urls)
- [Environment Variables](#environment-variables)
- [환경 변수](#environment-variables)
- [Disabling Vite In Tests](#disabling-vite-in-tests)
- [테스트에서 Vite 비활성화하기](#disabling-vite-in-tests)
- [Server-Side Rendering (SSR)](#ssr)
- [서버 사이드 렌더링(SSR)](#ssr)
- [Script & Style Tag Attributes](#script-and-style-attributes)
- [스크립트 및 스타일 태그 속성](#script-and-style-attributes)
    - [Content Security Policy (CSP) Nonce](#content-security-policy-csp-nonce)
    - [컨텐트 보안 정책 (CSP) 논스](#content-security-policy-csp-nonce)
    - [Subresource Integrity (SRI)](#subresource-integrity-sri)
    - [하위 리소스 무결성 (SRI)](#subresource-integrity-sri)
    - [Arbitrary Attributes](#arbitrary-attributes)
    - [임의 속성](#arbitrary-attributes)
- [Advanced Customization](#advanced-customization)
- [고급 사용자 정의](#advanced-customization)
  - [Correcting Dev Server URLs](#correcting-dev-server-urls)
  - [Dev Server URL 수정](#correcting-dev-server-urls)

<a name="introduction"></a>
## Introduction
## 소개

[Vite](https://vitejs.dev) is a modern frontend build tool that provides an extremely fast development environment and bundles your code for production. When building applications with Laravel, you will typically use Vite to bundle your application's CSS and JavaScript files into production ready assets.

[Vite](https://vitejs.dev)는 매우 빠른 개발 환경을 제공하고 프로덕션용 코드를 번들로 제공하는 최신 프론트엔드 빌드 도구입니다. 라라벨로 애플리케이션을 빌드할 때 일반적으로 Vite를 사용하여 어플리케이션의 CSS 및 자바스크립트 파일을 프로덕션에 나갈 준비된 자산으로 묶습니다.

Laravel integrates seamlessly with Vite by providing an official plugin and Blade directive to load your assets for development and production.

라라벨은 개발 및 생산을 위해 자산을 로드하기 위한 공식 플러그인 및 Blade 지시문을 제공하여 Vite와 매끄럽게 통합됩니다.

> **Note**  
> Are you running Laravel Mix? Vite has replaced Laravel Mix in new Laravel installations. For Mix documentation, please visit the [Laravel Mix](https://laravel-mix.com/) website. If you would like to switch to Vite, please see our [migration guide](https://github.com/laravel/vite-plugin/blob/main/UPGRADE.md#migrating-from-laravel-mix-to-vite).

> **Note**  
> Laravel Mix를 실행 중이신가요? 최신 라라벨에서 Vite가  Laravel Mix를 대체했습니다. Mix 문서는 [Laravel Mix](https://laravel-mix.com/) 웹사이트를 방문하십시오. Vite로 전환하려면 [마이그레이션 가이드](https://github.com/laravel/vite-plugin/blob/main/UPGRADE.md#migrating-from-laravel-mix-to-vite)를 참조하세요 .

<a name="vite-or-mix"></a>
#### Choosing Between Vite And Laravel Mix
#### Vite와 Laravel 믹스 중에서 선택하기

Before transitioning to Vite, new Laravel applications utilized [Mix](https://laravel-mix.com/), which is powered by [webpack](https://webpack.js.org/), when bundling assets. Vite focuses on providing a faster and more productive experience when building rich JavaScript applications. If you are developing a Single Page Application (SPA), including those developed with tools like [Inertia](https://inertiajs.com), Vite will be the perfect fit.

Vite로 전환하기 전에 새로운 라라벨 애플리케이션은 자산을 번들링할 때 [webpack](https://webpack.js.org/) 으로 구동되는 [Mix](https://laravel-mix.com/) 를 활용했습니다. Vite는 리치 자바스크립트 애플리케이션을 구축할 때 더 빠르고 생산적인 경험을 제공하는 데 중점을 둡니다. [Inertia](https://inertiajs.com) 와 같은 도구로 개발된 애플리케이션을 포함하여 SPA(단일 페이지 애플리케이션)를 개발하는 경우 Vite가 가장 적합합니다.

Vite also works well with traditional server-side rendered applications with JavaScript "sprinkles", including those using [Livewire](https://livewire.laravel.com). However, it lacks some features that Laravel Mix supports, such as the ability to copy arbitrary assets into the build that are not referenced directly in your JavaScript application.

Vite는 또한 [Livewire](https://livewire.laravel.com) 를 사용하는 애플리케이션을 포함하여 자바스크립트 "sprinkles"가 있는 기존 서버 사이드 렌더링 애플리케이션과도 잘 작동합니다. 그러나 자바스크립트 애플리케이션에서 직접 참조되지 않는 임의의 자산을 빌드에 복사하는 기능과 같이 Laravel Mix가 지원하는 일부 기능이 부족합니다.

<a name="migrating-back-to-mix"></a>
#### Migrating Back To Mix
#### Mix로 돌아가기

Have you started a new Laravel application using our Vite scaffolding but need to move back to Laravel Mix and webpack? No problem. Please consult our [official guide on migrating from Vite to Mix](https://github.com/laravel/vite-plugin/blob/main/UPGRADE.md#migrating-from-vite-to-laravel-mix).

Vite 스캐폴딩을 사용하여 새로운 Laravel 애플리케이션을 시작했지만 Laravel Mix와 Webpack으로 다시 돌아갈 필요가 있나요? 문제 없어요. [Vite에서 Mix로의 마이그레이션에 대한 공식 가이드](https://github.com/laravel/vite-plugin/blob/main/UPGRADE.md#migrating-from-vite-to-laravel-mix)를 참조하세요.

<a name="installation"></a>
## Installation & Setup
## 설치 & 설정

> **Note**  
> The following documentation discusses how to manually install and configure the Laravel Vite plugin. However, Laravel's [starter kits](/docs/{{version}}/starter-kits) already include all of this scaffolding and are the fastest way to get started with Laravel and Vite.

> **Note**  
> 다음 문서에서는 Laravel Vite 플러그인을 수동으로 설치하고 구성하는 방법에 대해 설명합니다. 그러나 Laravel의 [스타터 키트](/docs/{{version}}/starter-kits)에는 이미 이 모든 스캐폴딩이 포함되어 있으며 Laravel과 Vite를 시작하는 가장 빠른 방법입니다.

<a name="installing-node"></a>
### Installing Node
### Node 설치

You must ensure that Node.js (16+) and NPM are installed before running Vite and the Laravel plugin:

Vite 및 Laravel 플러그인을 실행하기 전에 Node.js(16+) 및 NPM이 설치되어 있는지 확인해야 합니다.

```sh
node -v
npm -v
```

You can easily install the latest version of Node and NPM using simple graphical installers from [the official Node website](https://nodejs.org/en/download/). Or, if you are using [Laravel Sail](https://laravel.com/docs/{{version}}/sail), you may invoke Node and NPM through Sail:

[Node 공식 웹사이트](https://nodejs.org/en/download/) 에서 간단한 그래픽 설치 프로그램을 사용하여 최신 버전의 Node 및 NPM을 쉽게 설치할 수 있습니다. [Laravel Sail](https://laravel.com/docs/{{version}}/sail) 을 사용하는 경우 Sail 을 통해 Node 및 NPM을 호출할 수 있습니다.

```sh
./vendor/bin/sail node -v
./vendor/bin/sail npm -v
```

<a name="installing-vite-and-laravel-plugin"></a>
### Installing Vite And The Laravel Plugin
### Vite 와 라라벨 플러그인 설치

Within a fresh installation of Laravel, you will find a `package.json` file in the root of your application's directory structure. The default `package.json` file already includes everything you need to get started using Vite and the Laravel plugin. You may install your application's frontend dependencies via NPM:

라라벨을 새로 설치하면 애플리케이션 루트에서 `package.json` 파일을 찾을 수 있습니다. 기본 `package.json` 파일에는 Vite 및 Laravel 플러그인 사용을 시작하는 데 필요한 모든 것이 이미 포함되어 있습니다. NPM을 통해 애플리케이션의 프론트엔드 종속성을 설치할 수 있습니다.

```sh
npm install
```

<a name="configuring-vite"></a>
### Configuring Vite
### Vite 설정

Vite is configured via a `vite.config.js` file in the root of your project. You are free to customize this file based on your needs, and you may also install any other plugins your application requires, such as `@vitejs/plugin-vue` or `@vitejs/plugin-react`.

Vite는 프로젝트 루트에 있는 `vite.config.js` 파일을 통해 설정됩니다. 필요에 따라 이 파일을 자유롭게 사용자 정의할 수 있으며 또는 `@vitejs/plugin-vue` 나 `@vitejs/plugin-react` 와 같이 애플리케이션에 필요한 다른 플러그인을 설치할 수도 있습니다.

The Laravel Vite plugin requires you to specify the entry points for your application. These may be JavaScript or CSS files, and include preprocessed languages such as TypeScript, JSX, TSX, and Sass.

Laravel Vite 플러그인을 사용하려면 애플리케이션의 진입점을 지정해야 합니다. 자바스크립트 또는 CSS 파일일 수 있으며 TypeScript, JSX, TSX 및 Sass와 같은 사전 처리된 언어를 포함합니다.

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel([
            'resources/css/app.css',
            'resources/js/app.js',
        ]),
    ],
});
```

If you are building an SPA, including applications built using Inertia, Vite works best without CSS entry points:

Inertia를 사용하여 구축된 애플리케이션을 포함하여 SPA를 구축하는 경우 Vite는 CSS 진입점 없이 가장 잘 작동합니다.

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel([
            'resources/css/app.css', // [tl! remove]
            'resources/js/app.js',
        ]),
    ],
});
```

Instead, you should import your CSS via JavaScript. Typically, this would be done in your application's `resources/js/app.js` file:

대신 자바스크립트를 통해 CSS를 가져와야 합니다. 일반적으로 이 작업은 애플리케이션의 `resources/js/app.js` 파일에서 수행됩니다.

```js
import './bootstrap';
import '../css/app.css'; // [tl! add]
```

The Laravel plugin also supports multiple entry points and advanced configuration options such as [SSR entry points](#ssr).

Laravel 플러그인은 또한 여러 진입점과 [SSR 진입점](#ssr) 과 같은 고급 구성 옵션을 지원합니다 .

<a name="working-with-a-secure-development-server"></a>
#### Working With A Secure Development Server
#### 보안 개발 서버에서 작업하기

If your local development web server is serving your application via HTTPS, you may run into issues connecting to the Vite development server.

로컬 개발 웹 서버가 HTTPS를 통해 애플리케이션을 제공하는 경우 Vite 개발 서버에 연결하는 데 문제가 발생할 수 있습니다.

If you are using [Laravel Valet](/docs/{{version}}/valet) for local development and have run the [secure command](/docs/{{version}}/valet#securing-sites) against your application, you may configure the Vite development server to automatically use Valet's generated TLS certificates:

로컬 개발을 위해 [Laravel Valet](/docs/{{version}}/valet) 을 사용 하고 애플리케이션에 대해 [secure 명령](/docs/{{version}}/valet#securing-sites) 을 실행했다면 Valet에서 생성된 TLS 인증서를 자동으로 사용하도록 Vite 개발 서버를 구성할 수 있습니다.

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            // ...
            valetTls: 'my-app.test', // [tl! add]
        }),
    ],
});
```

When using another web server, you should generate a trusted certificate and manually configure Vite to use the generated certificates:

다른 웹 서버를 사용할 때는 신뢰할 수 있는 인증서를 생성하고 생성된 인증서를 사용하도록 Vite를 수동으로 구성해야 합니다.

```js
// ...
import fs from 'fs'; // [tl! add]

const host = 'my-app.test'; // [tl! add]

export default defineConfig({
    // ...
    server: { // [tl! add]
        host, // [tl! add]
        hmr: { host }, // [tl! add]
        https: { // [tl! add]
            key: fs.readFileSync(`/path/to/${host}.key`), // [tl! add]
            cert: fs.readFileSync(`/path/to/${host}.crt`), // [tl! add]
        }, // [tl! add]
    }, // [tl! add]
});
```

If you are unable to generate a trusted certificate for your system, you may install and configure the [`@vitejs/plugin-basic-ssl` plugin](https://github.com/vitejs/vite-plugin-basic-ssl). When using untrusted certificates, you will need to accept the certificate warning for Vite's development server in your browser by following the "Local" link in your console when running the `npm run dev` command.

시스템에 대한 신뢰할 수 있는 인증서를 생성할 수 없는 경우 [`@vitejs/plugin-basic-ssl` 플러그인](https://github.com/vitejs/vite-plugin-basic-ssl) 을 설치하고 구성할 수 있습니다. 신뢰할 수 없는 인증서를 사용하는 경우 `npm run dev` 명령을 실행할 때 콘솔의 "로컬" 링크를 따라 브라우저에서 Vite의 개발 서버에 대한 인증서 경고를 수락해야 합니다 .

<a name="loading-your-scripts-and-styles"></a>
### Loading Your Scripts And Styles
### 스크립트와 스타일 불러오기

With your Vite entry points configured, you only need reference them in a `@vite()` Blade directive that you add to the `<head>` of your application's root template:

Vite 진입점을 구성하면 애플리케이션의 루트 템플릿의 `<head>`에 추가하는 `@vite()` Blade 지시문에서만 참조하면 됩니다.

```blade
<!doctype html>
<head>
    {{-- ... --}}

    @vite(['resources/css/app.css', 'resources/js/app.js'])
</head>
```

If you're importing your CSS via JavaScript, you only need to include the JavaScript entry point:

자바스크립트를 통해 CSS를 가져오는 경우 자바스크립트 진입점만 포함하면 됩니다.

```blade
<!doctype html>
<head>
    {{-- ... --}}

    @vite('resources/js/app.js')
</head>
```

The `@vite` directive will automatically detect the Vite development server and inject the Vite client to enable Hot Module Replacement. In build mode, the directive will load your compiled and versioned assets, including any imported CSS.

이 `@vite` 지시문은 자동으로 Vite 개발 서버를 감지하고 Vite 클라이언트를 주입하여 핫 모듈 교체를 활성화합니다. 빌드 모드에서 이 지시문은 가져온 CSS를 포함하여 컴파일되고 버전이 지정된 자산을 로드합니다.

If needed, you may also specify the build path of your compiled assets when invoking the `@vite` directive:

필요한 경우 `@vite` 지시문을 호출할 때 컴파일된 자산의 빌드 경로를 지정할 수도 있습니다.

```blade
<!doctype html>
<head>
    {{-- Given build path is relative to public path. --}}

    @vite('resources/js/app.js', 'vendor/courier/build')
</head>
```

<a name="running-vite"></a>
## Running Vite
## Vite 실행하기

There are two ways you can run Vite. You may run the development server via the `dev` command, which is useful while developing locally. The development server will automatically detect changes to your files and instantly reflect them in any open browser windows.

Vite를 실행할 수 있는 두 가지 방법이 있습니다. 로컬에서 개발할 때 유용한 `dev` 명령을 통해 개발 서버를 실행할 수 있습니다. 개발 서버는 파일의 변경 사항을 자동으로 감지하고 열려 있는 모든 브라우저 창에 즉시 반영합니다.

Or, running the `build` command will version and bundle your application's assets and get them ready for you to deploy to production:

또는 `build` 명령을 실행하면 애플리케이션 자산의 버전을 지정하고 번들로 제공하여 프로덕션에 배포할 수 있습니다.

```shell
# Run the Vite development server...
# Vite 개발 서버 실행...
npm run dev

# Build and version the assets for production...
# 프로덕션용 자산을 빌드하고 버저닝...
npm run build
```

<a name="working-with-scripts"></a>
## Working With JavaScript
## 자바스크립트 작업

<a name="aliases"></a>
### Aliases
### 별칭

By default, The Laravel plugin provides a common alias to help you hit the ground running and conveniently import your application's assets:

기본적으로 라라벨 플러그인은 애플리케이션의 자산을 편리하게 가져오는 데 도움이 되는 공통 별칭을 제공합니다.

```js
{
    '@' => '/resources/js'
}
```

You may overwrite the `'@'` alias by adding your own to the `vite.config.js` configuration file:

`vite.config.js` 구성 파일에 추가하여 `'@'` 별칭을 덮어쓸 수 있습니다.

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel(['resources/ts/app.tsx']),
    ],
    resolve: {
        alias: {
            '@': '/resources/ts',
        },
    },
});
```

<a name="vue"></a>
### Vue
### Vue

If you would like to build your front-end using the [Vue](https://vuejs.org/) framework, then you will also need to install the `@vitejs/plugin-vue` plugin:
[Vue](https:vuejs.org) 프레임워크를 사용하여 프런트 엔드를 구축하려면 `@vitejsplugin-vue` 플러그인도 설치해야 합니다.:

```sh
npm install --save-dev @vitejs/plugin-vue
```

You may then include the plugin in your `vite.config.js` configuration file. There are a few additional options you will need when using the Vue plugin with Laravel:
그런 다음 `vite.config.js` 구성 파일에 플러그인을 포함할 수 있습니다. Laravel과 함께 Vue 플러그인을 사용할 때 필요한 몇 가지 추가 옵션이 있습니다.:

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import vue from '@vitejs/plugin-vue';

export default defineConfig({
    plugins: [
        laravel(['resources/js/app.js']),
        vue({
            template: {
                transformAssetUrls: {
                    // The Vue plugin will re-write asset URLs, when referenced
                    // in Single File Components, to point to the Laravel web
                    // server. Setting this to `null` allows the Laravel plugin
                    // to instead re-write asset URLs to point to the Vite
                    // server instead.
                    base: null,

                    // The Vue plugin will parse absolute URLs and treat them
                    // as absolute paths to files on disk. Setting this to
                    // `false` will leave absolute URLs un-touched so they can
                    // reference assets in the public directory as expected.
                    includeAbsolute: false,
                },
            },
        }),
    ],
});
```

> **Note**  
> Laravel's [starter kits](/docs/{{version}}/starter-kits) already include the proper Laravel, Vue, and Vite configuration. Check out [Laravel Breeze](/docs/{{version}}/starter-kits#breeze-and-inertia) for the fastest way to get started with Laravel, Vue, and Vite.

> **Note**  
> 라라벨 [스타터 키트](/docs/{{version}}/starter-kits) 에는 이미 적절한 라라벨, Vue 및 Vite 구성이 포함되어 있습니다. 라라벨, Vue 및 Vite를 시작하는 가장 빠른 방법은 [Laravel Breeze](/docs/{{version}}/starter-kits#breeze-and-inertia) 를 확인하십시오 .

<a name="react"></a>
### React
### React

If you would like to build your front-end using the [React](https://reactjs.org/) framework, then you will also need to install the `@vitejs/plugin-react` plugin:
[React](https://reactjs.org/) 프레임워크를 사용하여 프런트 엔드를 구축하려면 `@vitejsplugin-react` 플러그인도 설치해야 합니다.:

```sh
npm install --save-dev @vitejs/plugin-react
```

You may then include the plugin in your `vite.config.js` configuration file:
그런 다음 `vite.config.js` 구성 파일에 플러그인을 포함할 수 있습니다.:

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import react from '@vitejs/plugin-react';

export default defineConfig({
    plugins: [
        laravel(['resources/js/app.jsx']),
        react(),
    ],
});
```

You will need to ensure that any files containing JSX have a `.jsx` or `.tsx` extension, remembering to update your entry point, if required, as [shown above](#configuring-vite).
JSX를 포함하는 모든 파일에 `.jsx` 또는 `.tsx` 확장자가 있는지 확인하고 필요한 경우 [위에 표시된](#configuring-vite)와 같이 진입점을 업데이트해야 합니다.

You will also need to include the additional `@viteReactRefresh` Blade directive alongside your existing `@vite` directive.
또한 기존 `@vite` 디렉티브와 함께 추가 `@viteReactRefresh` 블레이드 디렉티브를 포함해야 합니다.

```blade
@viteReactRefresh
@vite('resources/js/app.jsx')
```

The `@viteReactRefresh` directive must be called before the `@vite` directive.

`@viteReactRefresh` 지시문은 `@vite` 보다 먼저 호출되어야 합니다.

> **Note**  
> Laravel's [starter kits](/docs/{{version}}/starter-kits) already include the proper Laravel, React, and Vite configuration. Check out [Laravel Breeze](/docs/{{version}}/starter-kits#breeze-and-inertia) for the fastest way to get started with Laravel, React, and Vite.

> **Note**  
> Laravel의 [스타터 키트](/docs/{{version}}/starter-kits) 에는 이미 적절한 Laravel, React 및 Vite 구성이 포함되어 있습니다. Laravel, React 및 Vite를 시작하는 가장 빠른 방법은 [Laravel Breeze](/docs/{{version}}/starter-kits#breeze-and-inertia) 를 확인하십시오.

<a name="inertia"></a>
### Inertia
### Inertia

The Laravel Vite plugin provides a convenient `resolvePageComponent` function to help you resolve your Inertia page components. Below is an example of the helper in use with Vue 3; however, you may also utilize the function in other frameworks such as React:

Laravel Vite 플러그인은 Inertia 페이지 구성 요소를 해결하는 데 도움이 되는 편리한 `resolvePageComponent` 기능을 제공합니다. 다음은 Vue 3에서 사용하는 헬퍼의 예입니다만 React와 같은 다른 프레임워크에서도 이 기능을 활용할 수 있습니다.

```js
import { createApp, h } from 'vue';
import { createInertiaApp } from '@inertiajs/vue3';
import { resolvePageComponent } from 'laravel-vite-plugin/inertia-helpers';

createInertiaApp({
  resolve: (name) => resolvePageComponent(`./Pages/${name}.vue`, import.meta.glob('./Pages/**/*.vue')),
  setup({ el, App, props, plugin }) {
    return createApp({ render: () => h(App, props) })
      .use(plugin)
      .mount(el)
  },
});
```

> **Note**  
> Laravel's [starter kits](/docs/{{version}}/starter-kits) already include the proper Laravel, Inertia, and Vite configuration. Check out [Laravel Breeze](/docs/{{version}}/starter-kits#breeze-and-inertia) for the fastest way to get started with Laravel, Inertia, and Vite.

> **Note**  
> Laravel의 [스타터 키트](/docs/{{version}}/starter-kits) 에는 이미 적절한 Laravel, Inertia 및 Vite 구성이 포함되어 있습니다. Laravel, Inertia 및 Vite를 시작하는 가장 빠른 방법은 [Laravel Breeze](/docs/{{version}}/starter-kits#breeze-and-inertia) 를 확인하십시오.

<a name="url-processing"></a>
### URL Processing
### URL Processing

When using Vite and referencing assets in your application's HTML, CSS, or JS, there are a couple of caveats to consider. First, if you reference assets with an absolute path, Vite will not include the asset in the build; therefore, you should ensure that the asset is available in your public directory.

Vite를 사용하고 애플리케이션의 HTML, CSS 또는 JS에서 자산을 참조할 때 고려해야 할 몇 가지 주의 사항이 있습니다. 첫째, 절대 경로가 있는 자산을 참조하는 경우 Vite는 빌드에 자산을 포함하지 않습니다. 따라서 자산이 public 디렉토리에서 사용 가능한지 확인해야 합니다.

When referencing relative asset paths, you should remember that the paths are relative to the file where they are referenced. Any assets referenced via a relative path will be re-written, versioned, and bundled by Vite.

상대 자산 경로를 참조할 때 경로는 참조되는 파일에 상대적이라는 것을 기억해야 합니다. 상대 경로를 통해 참조되는 모든 자산은 Vite에서 다시 작성, 버전 관리 및 번들링됩니다.

Consider the following project structure:

다음 프로젝트 구조를 고려하십시오.

```nothing
public/
  taylor.png
resources/
  js/
    Pages/
      Welcome.vue
  images/
    abigail.png
```

The following example demonstrates how Vite will treat relative and absolute URLs:

다음 예는 Vite가 상대 및 절대 URL을 처리하는 방법을 보여줍니다.

```html
<!-- This asset is not handled by Vite and will not be included in the build -->
<!-- 이 자산은 Vite에 의해 처리되지 않고 빌드에 포함되지 않을 것입니다. -->
<img src="/taylor.png">

<!-- This asset will be re-written, versioned, and bundled by Vite -->
<!-- 이 자산은 Vite에 의해 재작성되고, 버저닝되고, 번들링 됩니다. -->
<img src="../../images/abigail.png">
```

<a name="working-with-stylesheets"></a>
## Working With Stylesheets
## 스타일시트 작업

You can learn more about Vite's CSS support within the [Vite documentation](https://vitejs.dev/guide/features.html#css). If you are using PostCSS plugins such as [Tailwind](https://tailwindcss.com), you may create a `postcss.config.js` file in the root of your project and Vite will automatically apply it:

[Vite 문서](https://vitejs.dev/guide/features.html#css) 에서 Vite의 CSS 지원에 대해 자세히 알아볼 수 있습니다. [Tailwind](https://tailwindcss.com) 와 같은 PostCSS 플러그인을 사용하는 경우 프로젝트 루트에 `postcss.config.js` 파일을 생성하면 Vite가 자동으로 적용합니다.

```js
module.exports = {
    plugins: {
        tailwindcss: {},
        autoprefixer: {},
    },
};
```

> **Note**
> Laravel's [starter kits](/docs/{{version}}/starter-kits) already include the proper Tailwind, PostCSS, and Vite configuration. Or, if you would like to use Tailwind and Laravel without using one of our starter kits, check out [Tailwind's installation guide for Laravel](https://tailwindcss.com/docs/guides/laravel).
> **Note**
> Laravel의 [스타터 키트](/docs/{{version}}/starter-kits)에는 이미 적절한 Tailwind, PostCSS 및 Vite 구성이 포함되어 있습니다. 또는 스타터 키트 중 하나를 사용하지 않고 Tailwind와 Laravel을 사용하려면 [Tailwind의 Laravel 설치 가이드](https://tailwindcss.com/docs/guides/laravel)를 확인하세요.

<a name="working-with-blade-and-routes"></a>
## Working With Blade & Routes
## 블레이드 및 경로 작업

<a name="blade-processing-static-assets"></a>
### Processing Static Assets With Vite
### Vite로 정적 Assets 처리

When referencing assets in your JavaScript or CSS, Vite automatically processes and versions them. In addition, when building Blade based applications, Vite can also process and version static assets that you reference solely in Blade templates.

JavaScript 또는 CSS에서 자산을 참조할 때 Vite는 자동으로 자산을 처리하고 버전을 지정합니다. 또한 블레이드 기반 애플리케이션을 구축할 때 Vite는 블레이드 템플릿에서만 참조하는 정적 자산을 처리하고 버전을 지정할 수도 있습니다.

However, in order to accomplish this, you need to make Vite aware of your assets by importing the static assets into the application's entry point. For example, if you want to process and version all images stored in `resources/images` and all fonts stored in `resources/fonts`, you should add the following in your application's `resources/js/app.js` entry point:

그러나 이를 수행하려면 정적 자산을 애플리케이션의 진입점으로 가져와 Vite가 자산을 인식하도록 해야 합니다. 예를 들어 `resources/images`에 저장된 모든 이미지와 `resources/fonts`에 저장된 모든 글꼴을 처리하고 버전을 지정하려면 애플리케이션의 `.resources/js/app.js` 진입점에 뒤에 다음 내용을 추가해야 합니다.

```js
import.meta.glob([
  '../images/**',
  '../fonts/**',
]);
```

These assets will now be processed by Vite when running `npm run build`. You can then reference these assets in Blade templates using the `Vite::asset` method, which will return the versioned URL for a given asset:

이러한 자산은 이제 `npm run build` 실행 시 Vite에서 처리됩니다. 그런 다음 주어진 자산에 대해 버전이 지정된 URL을 반환하는 `Vite::asset` 메서드를 사용하여 블레이드 템플릿에서 이러한 자산을 참조할 수 있습니다 .

```blade
<img src="{{ Vite::asset('resources/images/logo.png') }}">
```

<a name="blade-refreshing-on-save"></a>
### Refreshing On Save
### 저장시 새로 고침

When your application is built using traditional server-side rendering with Blade, Vite can improve your development workflow by automatically refreshing the browser when you make changes to view files in your application. To get started, you can simply specify the `refresh` option as `true`.

여러분의 애플리케이션이 Blade와 함께 전통적인 서버 사이드 렌더링을 사용하여 만들어졌다면, Vite는 애플리케이션에서 뷰 파일을 변경할 때 브라우저를 자동으로 새로 고침 해줌으로써 개발 워크플로를 개선할 수 있습니다. 이 기능을 사용하려면 `refresh` 옵션을 `true`로 지정하면 됩니다.

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            // ...
            refresh: true,
        }),
    ],
});
```

When the `refresh` option is `true`, saving files in the following directories will trigger the browser to perform a full page refresh while you are running `npm run dev`:

`refresh` 옵션이 `true` 이면, `npm run dev`가 실행 중인 상황에서 아래 디렉터리 내에 있는 파일이 저장되면 브라우저가 전체 페이지 새로 고침 됩니다.

- `app/View/Components/**`
- `lang/**`
- `resources/lang/**`
- `resources/views/**`
- `routes/**`

Watching the `routes/**` directory is useful if you are utilizing [Ziggy](https://github.com/tighten/ziggy) to generate route links within your application's frontend.

애플리케이션의 프런트엔드 내에서 경로 링크를 생성하기 위해 [Ziggy](https://github.com/tighten/ziggy) 를 사용하는 경우 `routes/**` 디렉토리를 보는 것이 유용합니다 .

If these default paths do not suit your needs, you can specify your own list of paths to watch:

이러한 기본 경로가 필요에 맞지 않는 경우 감시할 경로 목록을 직접 지정할 수 있습니다.

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            // ...
            refresh: ['resources/views/**'],
        }),
    ],
});
```

Under the hood, the Laravel Vite plugin uses the [`vite-plugin-full-reload`](https://github.com/ElMassimo/vite-plugin-full-reload) package, which offers some advanced configuration options to fine-tune this feature's behavior. If you need this level of customization, you may provide a `config` definition:

내부적으로 Laravel Vite 플러그인은 이 기능의 동작을 미세 조정할 수 있는 몇 가지 고급 구성 옵션을 제공하는 [`vite-plugin-full-reload`](https://github.com/ElMassimo/vite-plugin-full-reload) 패키지를 사용합니다. 이 수준의 사용자 정의가 필요한 경우 `config` 정의를 제공할 수 있습니다 .

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            // ...
            refresh: [{
                paths: ['path/to/watch/**'],
                config: { delay: 300 }
            }],
        }),
    ],
});
```

<a name="blade-aliases"></a>
### Aliases
### 별칭

It is common in JavaScript applications to [create aliases](#aliases) to regularly referenced directories. But, you may also create aliases to use in Blade by using the `macro` method on the `Illuminate\Support\Facades\Vite` class. Typically, "macros" should be defined within the `boot` method of a [service provider](/docs/{{version}}/providers):
JavaScript 응용 프로그램에서는 정기적으로 참조되는 디렉토리에 대한 [별칭 생성](#aliases)이 일반적입니다. 그러나 `Illuminate\Support\Facades\Vite` 클래스에서 `macro` 메서드를 사용하여 블레이드에서 사용할 별칭을 만들 수도 있습니다. 일반적으로 "매크로"는 [서비스 공급자](/docs/{{version}}/providers)의 `boot` 메서드 내에서 정의되어야 합니다.:

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Vite::macro('image', fn (string $asset) => $this->asset("resources/images/{$asset}"));
    }

Once a macro has been defined, it can be invoked within your templates. For example, we can use the `image` macro defined above to reference an asset located at `resources/images/logo.png`:

매크로가 정의되면 템플릿 내에서 호출할 수 있습니다. 예를 들어 위에 정의된 `image` 매크로를 사용하여 `resources/images/logo.png` 위치에 있는 자산을 참조할 수 있습니다.

```blade
<img src="{{ Vite::image('logo.png') }}" alt="Laravel Logo">
```

<a name="custom-base-urls"></a>
## Custom Base URLs
## 사용자 정의 기본 URL

If your Vite compiled assets are deployed to a domain separate from your application, such as via a CDN, you must specify the `ASSET_URL` environment variable within your application's `.env` file:

Vite 컴파일된 자산이 CDN 등을 통해 애플리케이션과 다른 도메인에 배포되는 경우 애플리케이션 `.env` 파일 내에서 `ASSET_URL` 환경 변수를 지정해야 합니다.

```env
ASSET_URL=https://cdn.example.com
```

After configuring the asset URL, all re-written URLs to your assets will be prefixed with the configured value:

자산 URL을 구성하고 나면 자산에 대해 다시 작성된 모든 URL에는 구성된 값이 접두사로 붙을 겁니다.


```nothing
https://cdn.example.com/build/assets/app.9dce8d17.js
```

Remember that [absolute URLs are not re-written by Vite](#url-processing), so they will not be prefixed.

[절대 URL은 Vite에 의해 다시 작성 되지 않으므로](#url-processing) 접두사가 붙지 않는다는 점을 기억하세요.

<a name="environment-variables"></a>
## Environment Variables
## 환경 변수

You may inject environment variables into your JavaScript by prefixing them with `VITE_` in your application's `.env` file:

애플리케이션 `.env` 파일에서 `VITE_` 접두사를 붙여주면 환경 변수를 자바스크립트로 주입할 수 있습니다.

```env
VITE_SENTRY_DSN_PUBLIC=http://example.com
```

You may access injected environment variables via the `import.meta.env` object:

`import.meta.env` 개체를 통해 주입된 환경 변수에 접근할 수 있습니다 .

```js
import.meta.env.VITE_SENTRY_DSN_PUBLIC
```

<a name="disabling-vite-in-tests"></a>
## Disabling Vite In Tests
## 테스트 시 Vite 비활성화

Laravel's Vite integration will attempt to resolve your assets while running your tests, which requires you to either run the Vite development server or build your assets.

Laravel의 Vite 통합은 테스트를 실행하는 동안 자산을 리졸브하려고 시도할 것인데, 이는 Vite 개발 서버를 실행하거나 자산을 빌드하는 것을 필요로 합니다.

If you would prefer to mock Vite during testing, you may call the `withoutVite` method, which is is available for any tests that extend Laravel's `TestCase` class:

테스트 중에 Vite를 mock하고 싶다면 `withoutVite` 메서드를 호출하면 됩니다. 이 메서드는 Laravel의 `TestCase` 클래스를 확장하는 모든 테스트에 사용할 수 있습니다.

```php
use Tests\TestCase;

class ExampleTest extends TestCase
{
    public function test_without_vite_example(): void
    {
        $this->withoutVite();

        // ...
    }
}
```

If you would like to disable Vite for all tests, you may call the `withoutVite` method from the `setUp` method on your base `TestCase` class:

모든 테스트에서 Vite를 비활성화하려면 기본 `TestCase` 클래스의 `setUp` 메서드에서 `withoutVite` 메서드를 호출하면 됩니다.

```php
<?php

namespace Tests;

use Illuminate\Foundation\Testing\TestCase as BaseTestCase;

abstract class TestCase extends BaseTestCase
{
    use CreatesApplication;

    protected function setUp(): void// [tl! add:start]
    {
        parent::setUp();

        $this->withoutVite();
    }// [tl! add:end]
}
```

<a name="ssr"></a>
## Server-Side Rendering (SSR)
## 서버 사이드 렌더링 (SSR)

The Laravel Vite plugin makes it painless to set up server-side rendering with Vite. To get started, create an SSR entry point at `resources/js/ssr.js` and specify the entry point by passing a configuration option to the Laravel plugin:

Laravel Vite 플러그인을 사용하면 Vite로 서버 사이드 렌더링을 쉽게 설정할 수 있습니다. 시작하려면 `resources/js/ssr.js` 에서 SSR 진입점을 만들고 구성 옵션을 Laravel 플러그인에 전달하여 진입점을 지정하십시오.

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            input: 'resources/js/app.js',
            ssr: 'resources/js/ssr.js',
        }),
    ],
});
```

To ensure you don't forget to rebuild the SSR entry point, we recommend augmenting the "build" script in your application's `package.json` to create your SSR build:

SSR 진입점을 다시 빌드하는 것을 잊지 않도록 `package.json`에 있는 "build" 스크립트를 보강하여 SSR 빌드를 생성하도록 하길 권합니다.

```json
"scripts": {
     "dev": "vite",
     "build": "vite build" // [tl! remove]
     "build": "vite build && vite build --ssr" // [tl! add]
}
```

Then, to build and start the SSR server, you may run the following commands:

그런 다음 SSR 서버를 빌드하고 시작하려면 다음 명령을 실행하세요.

```sh
npm run build
node bootstrap/ssr/ssr.mjs
```

> **Note**  
> Laravel's [starter kits](/docs/{{version}}/starter-kits) already include the proper Laravel, Inertia SSR, and Vite configuration. Check out [Laravel Breeze](/docs/{{version}}/starter-kits#breeze-and-inertia) for the fastest way to get started with Laravel, Inertia SSR, and Vite.

> **Note**  
> Laravel의 [스타터 키트](/docs/{{version}}/starter-kits) 에는 이미 적절한 Laravel, Inertia SSR 및 Vite 구성이 포함되어 있습니다. Laravel, Inertia SSR 및 Vite를 시작하는 가장 빠른 방법은 [Laravel Breeze](/docs/{{version}}/starter-kits#breeze-and-inertia) 를 확인하십시오 .

<a name="script-and-style-attributes"></a>
## Script & Style Tag Attributes
## 스크립트 및 스타일 태그 속성

<a name="content-security-policy-csp-nonce"></a>
### Content Security Policy (CSP) Nonce
### 컨텐트 보안 정책 (CSP) 논스

If you wish to include a [`nonce` attribute](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/nonce) on your script and style tags as part of your [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP), you may generate or specify a nonce using the `useCspNonce` method within a custom [middleware](/docs/{{version}}/middleware):

[콘텐츠 보안 정책](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) 의 일부로 스크립트 및 스타일 태그에 [`nonce` 속성](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/nonce) 을 포함 하려면 사용자 지정 [미들웨어](/docs/{{version}}/middleware) 내에서 `useCspNonce` 메서드 를 사용하여 nonce를 생성하거나 지정할 수 있습니다 .

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Vite;
use Symfony\Component\HttpFoundation\Response;

class AddContentSecurityPolicyHeaders
{
    /**
     * Handle an incoming request.
     *
     * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
     */
    public function handle(Request $request, Closure $next, string $role): Response
    {
        Vite::useCspNonce();

        return $next($request)->withHeaders([
            'Content-Security-Policy' => "script-src 'nonce-".Vite::cspNonce()."'",
        ]);
    }
}
```

After invoking the `useCspNonce` method, Laravel will automatically include the `nonce` attributes on all generated script and style tags.

`useCspNonce` 메소드를 호출한 후 Laravel은 생성된 모든 스크립트 및 스타일 태그에 `.nonce` 속성을 자동으로 포함합니다.

If you need to specify the nonce elsewhere, including the [Ziggy `@route` directive](https://github.com/tighten/ziggy#using-routes-with-a-content-security-policy) included with Laravel's [starter kits](/docs/{{version}}/starter-kits), you may retrieve it using the `cspNonce` method:

Laravel [스타터 키트](/docs/{{version}}/starter-kits) 에 포함된 [Ziggy `@route` 지시문](https://github.com/tighten/ziggy#using-routes-with-a-content-security-policy) 을 포함하여 다른 곳에 논스를 지정해야 하는 경우 `.cspNonce` 메서드를 사용하여 조회할 수 있습니다.

```blade
@routes(nonce: Vite::cspNonce())
```

If you already have a nonce that you would like to instruct Laravel to use, you may pass the nonce to the `useCspNonce` method:

Laravel에 사용하도록 지시하려는 논스가 이미 있는 경우 `useCspNonce` 메서드에 논스를 전달할 수 있습니다 .

```php
Vite::useCspNonce($nonce);
```

<a name="subresource-integrity-sri"></a>
### Subresource Integrity (SRI)
### 하위 리소스 무결성 (SRI)

If your Vite manifest includes `integrity` hashes for your assets, Laravel will automatically add the `integrity` attribute on any script and style tags it generates in order to enforce [Subresource Integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity). By default, Vite does not include the `integrity` hash in its manifest, but you may enable it by installing the [`vite-plugin-manifest-sri`](https://www.npmjs.com/package/vite-plugin-manifest-sri) NPM plugin:
Vite 매니페스트가 자산에 대한 `integrity` 해시를 포함하는 경우 Laravel은 [Subresource Integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity)를 적용하기 위해 생성하는 모든 스크립트 및 스타일 태그에 `integrity` 속성을 자동으로 추가합니다. 기본적으로 Vite는 매니페스트에 `integrity` 해시를 포함하지 않지만 [`vite-plugin-manifest-sri`](https://www.npmjs.com/package/vite-plugin-manifest-sri)를 설치하여 활성화할 수 있습니다. NPM 플러그인:

```shell
npm install --save-dev vite-plugin-manifest-sri
```

You may then enable this plugin in your `vite.config.js` file:

그런 다음 `vite.config.js` 파일에서 플러그인을 활성화 할 수 있습니다.

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import manifestSRI from 'vite-plugin-manifest-sri';// [tl! add]

export default defineConfig({
    plugins: [
        laravel({
            // ...
        }),
        manifestSRI(),// [tl! add]
    ],
});
```

If required, you may also customize the manifest key where the integrity hash can be found:

필요한 경우 무결성 해시를 찾을 수 있는 매니페스트 키를 사용자 지정할 수도 있습니다.

```php
use Illuminate\Support\Facades\Vite;

Vite::useIntegrityKey('custom-integrity-key');
```

If you would like to disable this auto-detection completely, you may pass `false` to the `useIntegrityKey` method:

이 자동 감지를 완전히 비활성화 하려면 `useIntegrityKey` 메서드에 `false`를 전달할 수 있습니다.

```php
Vite::useIntegrityKey(false);
```

<a name="arbitrary-attributes"></a>
### Arbitrary Attributes
### 임의 속성

If you need to include additional attributes on your script and style tags, such as the [`data-turbo-track`](https://turbo.hotwired.dev/handbook/drive#reloading-when-assets-change) attribute, you may specify them via the `useScriptTagAttributes` and `useStyleTagAttributes` methods. Typically, this methods should be invoked from a [service provider](/docs/{{version}}/providers):

[`data-turbo-track`](https://turbo.hotwired.dev/handbook/drive#reloading-when-assets-change) 속성과 같이 스크립트 및 스타일 태그에 추가 속성을 포함해야 하는 경우 `useScriptTagAttributes` 및 `useStyleTagAttributes` 메서드를 통해 지정할 수 있습니다. 일반적으로 이 메서드는 [서비스 공급자](/docs/{{version}}/providers) 에서 호출해야 합니다.

```php
use Illuminate\Support\Facades\Vite;

Vite::useScriptTagAttributes([
    'data-turbo-track' => 'reload', // Specify a value for the attribute...
    'async' => true, // Specify an attribute without a value...
    'integrity' => false, // Exclude an attribute that would otherwise be included...
]);

Vite::useStyleTagAttributes([
    'data-turbo-track' => 'reload',
]);
```

If you need to conditionally add attributes, you may pass a callback that will receive the asset source path, its URL, its manifest chunk, and the entire manifest:

조건부로 속성을 추가해야 하는 경우 자산 소스 경로, URL, 매니페스트 청크 및 전체 매니페스트를 수신하는 콜백을 전달할 수 있습니다.

```php
use Illuminate\Support\Facades\Vite;

Vite::useScriptTagAttributes(fn (string $src, string $url, array|null $chunk, array|null $manifest) => [
    'data-turbo-track' => $src === 'resources/js/app.js' ? 'reload' : false,
]);

Vite::useStyleTagAttributes(fn (string $src, string $url, array|null $chunk, array|null $manifest) => [
    'data-turbo-track' => $chunk && $chunk['isEntry'] ? 'reload' : false,
]);
```

> **Warning**  
> The `$chunk` and `$manifest` arguments will be `null` while the Vite development server is running.

> **Warning**  
> Vite 개발 서버가 동작중일 때는 `$chunk` 와 `$manifest` 인수는 `null` 이 될 것입니다.

<a name="advanced-customization"></a>
## Advanced Customization
## 고급 사용자 정의

Out of the box, Laravel's Vite plugin uses sensible conventions that should work for the majority of applications; however, sometimes you may need to customize Vite's behavior. To enable additional customization options, we offer the following methods and options which can be used in place of the `@vite` Blade directive:

기본적으로 Laravel의 Vite 플러그인은 대부분의 애플리케이션에서 작동해야 하는 합리적인 규칙을 사용합니다. 그러나 때로는 Vite의 동작을 사용자 정의해야 할 수도 있습니다. 추가 사용자 지정 옵션을 활성화하기 위해 `@vite` Blade 지시문 대신 사용할 수 있는 다음 메서드와 옵션을 제공합니다 .

```blade
<!doctype html>
<head>
    {{-- ... --}}

    {{
        Vite::useHotFile(storage_path('vite.hot')) // Customize the "hot" file...
            ->useBuildDirectory('bundle') // Customize the build directory...
            ->useManifestFilename('assets.json') // Customize the manifest filename...
            ->withEntryPoints(['resources/js/app.js']) // Specify the entry points...
    }}
</head>
```

Within the `vite.config.js` file, you should then specify the same configuration:

그런 다음 `vite.config.js` 파일 내에서 동일한 구성을 지정해야 합니다. 

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            hotFile: 'storage/vite.hot', // Customize the "hot" file...
            buildDirectory: 'bundle', // Customize the build directory...
            input: ['resources/js/app.js'], // Specify the entry points...
        }),
    ],
    build: {
      manifest: 'assets.json', // Customize the manifest filename...
    },
});
```

<a name="correcting-dev-server-urls"></a>
### Correcting Dev Server URLs
### Dev Server URL 수정

Some plugins within the Vite ecosystem assume that URLs which begin with a forward-slash will always point to the Vite dev server. However, due to the nature of the Laravel integration, this is not the case.
Vite 생태계 내의 일부 플러그인은 슬래시로 시작하는 URL이 항상 Vite 개발 서버를 가리킨다고 가정합니다. 그러나 Laravel 통합의 특성상 그렇지 않습니다.

For example, the `vite-imagetools` plugin outputs URLs like the following while Vite is serving your assets:
예를 들어, `vite-imagetools` 플러그인은 Vite가 자산을 제공하는 동안 다음과 같은 URL을 출력합니다.:

```html
<img src="/@imagetools/f0b2f404b13f052c604e632f2fb60381bf61a520">
```

The `vite-imagetools` plugin is expecting that the output URL will be intercepted by Vite and the plugin may then handle all URLs that start with `/@imagetools`. If you are using plugins that are expecting this behaviour, you will need to manually correct the URLs. You can do this in your `vite.config.js` file by using the `transformOnServe` option.
`vite-imagetools` 플러그인은 출력 URL이 Vite에 의해 차단될 것으로 예상하고 플러그인은 `@imagetools`로 시작하는 모든 URL을 처리할 수 있습니다. 이 동작이 예상되는 플러그인을 사용하는 경우 URL을 수동으로 수정해야 합니다. `transformOnServe` 옵션을 사용하여 `vite.config.js` 파일에서 이 작업을 수행할 수 있습니다.

In this particular example, we will append the dev server URL to all occurrences of `/@imagetools` within the generated code:
이 특정 예에서는 생성된 코드 내 모든 `@imagetools` 항목에 개발 서버 URL을 추가합니다.:

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import { imagetools } from 'vite-imagetools';

export default defineConfig({
    plugins: [
        laravel({
            // ...
            transformOnServe: (code, devServerUrl) => code.replaceAll('/@imagetools', devServerUrl+'/@imagetools'),
        }),
        imagetools(),
    ],
});
```

Now, while Vite is serving Assets, it will output URLs that point to the Vite dev server:
이제 Vite가 Assets을 제공하는 동안 Vite 개발 서버를 가리키는 URL을 출력합니다.:

```html
- <img src="/@imagetools/f0b2f404b13f052c604e632f2fb60381bf61a520"><!-- [tl! remove] -->
+ <img src="http://[::1]:5173/@imagetools/f0b2f404b13f052c604e632f2fb60381bf61a520"><!-- [tl! add] -->
```

