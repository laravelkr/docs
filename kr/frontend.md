# JavaScript & CSS Scaffolding
# 자바스크립트 & CSS 스캐폴딩

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Writing CSS](#writing-css)
- [CSS 작성하기](#writing-css)
- [Writing JavaScript](#writing-javascript)
- [JavaScript 작성하기](#writing-javascript)
    - [Writing Vue Components](#writing-vue-components)
    - [Vue 컴포넌트 작성하기](#writing-vue-components)
    - [Using React](#using-react)
    - [React 사용하기](#using-react)

<a name="introduction"></a>
## Introduction
## 시작하기

While Laravel does not dictate which JavaScript or CSS pre-processors you use, it does provide a basic starting point using [Bootstrap](https://getbootstrap.com/) and [Vue](https://vuejs.org) that will be helpful for many applications. By default, Laravel uses [NPM](https://www.npmjs.org) to install both of these frontend packages.

라라벨이 자바스크립트 또는 CSS 전처리기 사용을 지시하지는 않지만, 많은 애플리케이션에서 유용할 수 있는 [Bootstrap](https://getbootstrap.com) 과 [Vue](https://vuejs.org)을 사용하여 기본적인 시작점을 제공합니다. 기본적으로 라라벨은 이 두개의 프론트 엔드 패키지를 설치하기 위해서 [NPM](https://www.npmjs.org)을 사용합니다.

The Bootstrap and Vue scaffolding provided by Laravel is located in the `laravel/ui` Composer package, which may be installed using Composer:

라라벨에서 제공하는 부트스트랩 및 Vue 스캐폴딩은 `laravel/ui` Composer 패키지에 있으며 Composer를 사용하여 설치할 수 있습니다.

    composer require laravel/ui

Once the `laravel/ui` package has been installed, you may install the frontend scaffolding using the `ui` Artisan command:

`laravel/ui` 패키지가 설치되면 `ui` Artisan 명령을 사용하여 프론트엔드 스캐폴딩을 설치할 수 있습니다.

    // Generate basic scaffolding...
    php artisan ui vue
    php artisan ui react

    // Generate login / registration scaffolding...
    php artisan ui vue --auth
    php artisan ui react --auth

#### CSS
#### CSS

[Laravel Mix](/docs/{{version}}/mix) provides a clean, expressive API over compiling SASS or Less, which are extensions of plain CSS that add variables, mixins, and other powerful features that make working with CSS much more enjoyable. In this document, we will briefly discuss CSS compilation in general; however, you should consult the full [Laravel Mix documentation](/docs/{{version}}/mix) for more information on compiling SASS or Less.

[라라벨 Mix](/docs/{{version}}/mix)는 순수 CSS에 변수를 추가하고 mixin 그리고 다른 강력한 기능을 추가하여 CSS를 보다 즐겁게 만들 수 있는 SASS 나 Less 를 컴파일 하는 깔끔하고, 풍부한 표현이 가능한 API를 제공합니다. 이 문서에서 CSS 컴파일 전반에 대해서 간단하게 다룰 것입니다; 하지만 SASS 나 Less 를 컴파일하는데에 대한 보다 자세한 내용을 [라라벨 Mix 문서](/docs/{{version}}/mix)에서 확인할 수 있습니다:

#### JavaScript
#### 자바스크립트

Laravel does not require you to use a specific JavaScript framework or library to build your applications. In fact, you don't have to use JavaScript at all. However, Laravel does include some basic scaffolding to make it easier to get started writing modern JavaScript using the [Vue](https://vuejs.org) library. Vue provides an expressive API for building robust JavaScript applications using components. As with CSS, we may use Laravel Mix to easily compile JavaScript components into a single, browser-ready JavaScript file.

라라벨은 애플리케이션을 구성하기 위해서 지정된 자바스크립트 프레임워크나 라이브러리를 사용하는 것을 요구하지 않습니다. 사실, 모든 곳에 자바스크립트를 사용할 필요는 없습니다. 하지만 라라벨은 [Vue](https://vuejs.org)라이브러리르 사용하여 현대적인 자바스크립트를 작성하는 일을 시작하는 것을 보다 쉽게 만들어 주는 몇몇 기본적인 스캐폴딩을 포함하고 있습니다. Vue는 컴포넌트를 사용하여 자바스크립트 애플리케이션을 구성하는는데 풍부한 표현이 가능한 API를 제공합니다. CSS와 마찬가지로, 라라벨 Mix를 사용하여 자바스크립트 컴포넌트를 브라우저에서 사용가능한 하나의 자바스크립트 파일로 컴파일 할 수 있습니다.

#### Removing The Frontend Scaffolding
#### Frontend 스캐폴딩 삭제하기

If you would like to remove the frontend scaffolding from your application, you may use the `preset` Artisan command. This command, when combined with the `none` option, will remove the Bootstrap and Vue scaffolding from your application, leaving only a blank SASS file and a few common JavaScript utility libraries:

애플리케이션에서 프론트엔드 스캐폴딩을 제거하려면, `preset` 아티즌 명령어를 사용하면 됩니다. 이 명령어가 `none` 옵션과 함께 사용되면, 애플리케이션에 비어 있는 SASS 파일과 몇몇 자바스크립트 유틸리티 라이브러리를 남겨 놓고, 부트스트랩과 Vue 스캐폴딩을 제거합니다.

    php artisan preset none

<a name="writing-css"></a>
## Writing CSS
## CSS 작성하기

Laravel's `package.json` file includes the `bootstrap` package to help you get started prototyping your application's frontend using Bootstrap. However, feel free to add or remove packages from the `package.json` file as needed for your own application. You are not required to use the Bootstrap framework to build your Laravel application - it is provided as a good starting point for those who choose to use it.

라라벨의 `package.json` 파일은 부트스트랩을 사용하여 애플리케이션의 프론트엔드 프로토타이핑을 시작하는 것을 돕는 `bootstrap` 패키지를 포함하고 있습니다. 그렇지만 애플리케이션에 필요한 경우 자유롭게 `package.json` 파일에서 패키지를 추가하거나, 삭제하면 됩니다. 라라벨 애플리케이션을 구성하는데 부트스트랩 프레임워크를 사용하는 것이 필요하지는 않습니다 - 부트스트랩을 사용하기로 선택한 사람들을 위해서 시작점을 제공하고 있을 뿐입니다.

Before compiling your CSS, install your project's frontend dependencies using the [Node package manager (NPM)](https://www.npmjs.org):

CSS 를 컴파일 하기 전에, [Node 패키지 매니저 (NPM)](https://www.npmjs.org)을 사용하여 여러분의 프로젝트의 프론트엔드 의존성을 설치하십시오:

    npm install

Once the dependencies have been installed using `npm install`, you can compile your SASS files to plain CSS using [Laravel Mix](/docs/{{version}}/mix#working-with-stylesheets). The `npm run dev` command will process the instructions in your `webpack.mix.js` file. Typically, your compiled CSS will be placed in the `public/css` directory:

`npm install` 을 사용하여 의존성을 설치하고 나면, [라라벨 Mix](/docs/{{version}}/mix#working-with-stylesheets)를 사용하여 SASS 파일을 순수 CSS 로 컴파일 할 수 있습니다. `npm run dev` 명령어는 `webpack.mix.js` 파일 안에 있는 명령들을 처리할 것입니다. 일반적으로 컴파일된 CSS 파일은 `public/css` 디렉토리에 위치할 것입니다:

    npm run dev

The default `webpack.mix.js` included with Laravel will compile the `resources/sass/app.scss` SASS file. This `app.scss` file imports a file of SASS variables and loads Bootstrap, which provides a good starting point for most applications. Feel free to customize the `app.scss` file however you wish or even use an entirely different pre-processor by [configuring Laravel Mix](/docs/{{version}}/mix).

라라벨에 기본적으로 포함된 `webpack.mix.js`는 `resources/sass/app.scss` SASS 파일을 컴파일 할 것입니다. 이 `app.scss` 파일은 SASS 변수들을 가져오고 대부분의 애플리케이션을 위한 좋은 시작점이 되는 제공하는부트스트랩을 로딩합니다. 이 `app.scss` 파일은 자유롭게 수정할 수 있지만, 원하는 경우 완전히 다른 전처리기를 [라라벨 Mix에서 설정하여](/docs/{{version}}/mix) 사용할 수도 있습니다.

<a name="writing-javascript"></a>
## Writing JavaScript
## 자바스크립트 작성하기

All of the JavaScript dependencies required by your application can be found in the `package.json` file in the project's root directory. This file is similar to a `composer.json` file except it specifies JavaScript dependencies instead of PHP dependencies. You can install these dependencies using the [Node package manager (NPM)](https://www.npmjs.org):

애플리케이션에 필요한 모든 자바스크립트 의존성들은 프로젝트 루트 디렉토리 안에 있는 `package.json` 파일 안에서 찾을 수 있습니다. 이 파일은 PHP 의존성 대신 자바스크립트 의존성이 지정되어 있다는 점을 제외하면 `composer.json` 파일과 비슷합니다. [Node 패키지 매니저 (NPM)](https://www.npmjs.org)을 사용하여 이 의존성들을 설치할 수 있습니다:

    npm install

> {tip} By default, the Laravel `package.json` file includes a few packages such as `vue` and `axios` to help you get started building your JavaScript application. Feel free to add or remove from the `package.json` file as needed for your own application.

> {tip} 기본적으로 라라벨의 `package.json` 파일은 자바스크립트 애플리케이션을 구성하는데 도움을 줄 수 있는`vue` 와 `axios` 와 같은 몇몇 패키지를 포함하고 있습니다. 자유롭게 `package.json` 파일에 애플리케이션에서 필요한 의존성들을 추가하거나 삭제할 수 있습니다.

Once the packages are installed, you can use the `npm run dev` command to [compile your assets](/docs/{{version}}/mix). Webpack is a module bundler for modern JavaScript applications. When you run the `npm run dev` command, Webpack will execute the instructions in your `webpack.mix.js` file:

패키지들이 설치되고 나면, `npm run dev` 명령어를 사용하여 [asset 을 컴파일](/docs/{{version}}/mix) 할 수 있습니다. Webpack은 현대적인 자바스크립트 애플리케이션을 위한 모듈 번들러 입니다. `npm run dev` 명령어를 실행하면, Webpack 는 `webpack.mix.js` 파일안에 있는 명령어들을 실행할 것입니다:

    npm run dev

By default, the Laravel `webpack.mix.js` file compiles your SASS and the `resources/js/app.js` file. Within the `app.js` file you may register your Vue components or, if you prefer a different framework, configure your own JavaScript application. Your compiled JavaScript will typically be placed in the `public/js` directory.

기본적으로 라라벨의 `webpack.mix.js` 파일은 SASS 와 `resources/js/app.js`파일을 컴파일 합니다. `app.js` 파일 안에서 Vue 컴포넌트나, 다른 프레임워크를 좋아한다면, 고유한 자바스크립트 애플리케이션을 구성할 수 있습니다. 컴파일된 자바스크립트는 일반적으로 `public/js` 디렉토리 안에 위치할 것입니다.

> {tip} The `app.js` file will load the `resources/js/bootstrap.js` file which bootstraps and configures Vue, Axios, jQuery, and all other JavaScript dependencies. If you have additional JavaScript dependencies to configure, you may do so in this file.

> {tip} `app.js` 파일은 `resources/js/bootstrap.js`을 로드하여 Vue, Axios, jQuery 그리고 다른 모든 자바스크립트 의존성들을 설정하고 구동할 것입니다. 만약 추가적인 자바스크립트 의존성을 설정해야 한다면, 이 파일안에서 할 수 있습니다.

<a name="writing-vue-components"></a>
### Writing Vue Components
### Vue 컴포넌트 작성하기

By default, fresh Laravel applications contain an `ExampleComponent.vue` Vue component located in the `resources/js/components` directory. The `ExampleComponent.vue` file is an example of a [single file Vue component](https://vuejs.org/guide/single-file-components) which defines its JavaScript and HTML template in the same file. Single file components provide a very convenient approach to building JavaScript driven applications. The example component is registered in your `app.js` file:

기본적으로 새로 설치한 라라벨 애플리케이션은 `resources/js/components` 디렉토리에 `ExampleComponent.vue` 뷰 컴포넌트를 포함하고 있습니다. `ExampleComponent.vue` 파일은 동일한 파일 안에서 자바스크립트와 HTML 템플릿을 정의한 [파일 하나로 된 Vue 컴포넌트](https://vuejs.org/guide/single-file-components)의 예제 입니다. 하나의 파일로된 컴포넌트는 자바스크립트 기반의 애플리케이션을 구성하는데 매우 편리한 방법을 제공합니다. 이 예제 컴포넌트는 `app.js` 에 등록되어 있습니다:

    Vue.component(
        'example-component',
        require('./components/ExampleComponent.vue').default
    );

To use the component in your application, you may drop it into one of your HTML templates. For example, after running the `make:auth` Artisan command to scaffold your application's authentication and registration screens, you could drop the component into the `home.blade.php` Blade template:

애플리케이션에서 이 컴포넌트를 사용하려면, HTML 템플린 안에 등록하면 됩니다. 예를 들어 애플리케이션의 인증과 회원 가입 화면을 스캐폴딩 하기 위해서 `make:auth` 아티즌 명령어를 실행 한 다음에 컴포넌트를 `home.blade.php` 블레이드 템플릿 안에 등록할 수 있습니다:

    @extends('layouts.app')

    @section('content')
        <example-component></example-component>
    @endsection

> {tip} Remember, you should run the `npm run dev` command each time you change a Vue component. Or, you may run the `npm run watch` command to monitor and automatically recompile your components each time they are modified.

> {tip} 주의할 것은, Vue 컴포넌트가 바뀔 때 마다 `npm run dev` 명령어를 실행해야 한다는 것입니다. 또는 `npm run watch` 명령어를 실행하여 파일들이 변경될 때 이를 모니터링 하고 있다가 자동으로 다시 컴파일 하도록 할 수 있습니다.

If you are interested in learning more about writing Vue components, you should read the [Vue documentation](https://vuejs.org/guide/), which provides a thorough, easy-to-read overview of the entire Vue framework.

Vue 컴포넌트를 작성하는데 관심이 있다면, 전체 Vue 프레임워크에 대해서 개념을 손쉽게 읽을 수 있는 [Vue 매뉴얼](https://kr.vuejs.org/v2/guide/index.html)을 확인하길 바랍니다.

<a name="using-react"></a>
### Using React
### React 사용하기

If you prefer to use React to build your JavaScript application, Laravel makes it a cinch to swap the Vue scaffolding with React scaffolding. On any fresh Laravel application, you may use the `preset` command with the `react` option:

자바스크립트 애플리케이션을 구성하는데 React를 선호한다면, 라라벨에서는 Vue 스캐폴딩을 React 스캐폴딩으로 바꾸는 것이 아주 쉽습니다. 새롭게 라라벨 애플리케이션을 설치했다면, `preset` 명령어에 `react` 옵션을 지정하여 사용할 수 있습니다:

    php artisan preset react

This single command will remove the Vue scaffolding and replace it with React scaffolding, including an example component.

이 한줄의 명령어는 Vue 스캐폴딩을 제거하고, 간단한 예제 컴포넌트가 들어 있는 React 스캐폴딩으로 교체합니다.
