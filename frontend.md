# 자바스크립트 & CSS 스캐폴딩

- [시작하기](#introduction)
- [CSS 작성하기](#writing-css)
- [JavaScript 작성하기](#writing-javascript)
    - [Vue 컴포넌트 작성하기](#writing-vue-components)
    - [React 사용하기](#using-react)
- [프리셋-Presets 추가하기](#adding-presets)

<a name="introduction"></a>
## 시작하기

Laravel은 사용하는 JavaScript 또는 CSS 전처리기를 제한하지 않지만 [Bootstrap](https://getbootstrap.com/), [React](https://reactjs.org/) 또는 [Vue](https://vuejs.org/)는 사용하여 기본적인 시작점을 제공합니다. 이것은 많은 애플리케이션에 도움이됩니다. 기본적으로 Laravel은 [NPM](https://www.npmjs.org)을 사용하여 이러한 프런트 엔드 패키지를 모두 설치합니다.

라라벨에서 제공하는 부트스트랩 및 Vue 스캐폴딩은 `laravel/ui` Composer 패키지에 있으며 Composer를 사용하여 설치할 수 있습니다.

    composer require laravel/ui

`laravel/ui` 패키지가 설치되면 `ui` Artisan 명령을 사용하여 프론트엔드 스캐폴딩을 설치할 수 있습니다.

    // Generate basic scaffolding...
    php artisan ui bootstrap
    php artisan ui vue
    php artisan ui react

    // Generate login / registration scaffolding...
    php artisan ui bootstrap --auth
    php artisan ui vue --auth
    php artisan ui react --auth

#### CSS

[라라벨 Mix](/docs/{{version}}/mix)는 순수 CSS에 변수를 추가하고 mixin 그리고 다른 강력한 기능을 추가하여 CSS를 보다 즐겁게 만들 수 있는 SASS 나 Less 를 컴파일 하는 깔끔하고, 풍부한 표현이 가능한 API를 제공합니다. 이 문서에서 CSS 컴파일 전반에 대해서 간단하게 다룰 것입니다; 하지만 SASS 나 Less 를 컴파일하는데에 대한 보다 자세한 내용을 [라라벨 Mix 문서](/docs/{{version}}/mix)에서 확인할 수 있습니다.

#### 자바스크립트

라라벨은 애플리케이션을 구성하기 위해서 지정된 자바스크립트 프레임워크나 라이브러리를 사용하는 것을 요구하지 않습니다. 사실, 모든 곳에 자바스크립트를 사용할 필요는 없습니다. 하지만 라라벨은 [Vue](https://vuejs.org)라이브러리르 사용하여 현대적인 자바스크립트를 작성하는 일을 시작하는 것을 보다 쉽게 만들어 주는 몇몇 기본적인 스캐폴딩을 포함하고 있습니다. Vue는 컴포넌트를 사용하여 자바스크립트 애플리케이션을 구성하는는데 풍부한 표현이 가능한 API를 제공합니다. CSS와 마찬가지로, 라라벨 Mix를 사용하여 자바스크립트 컴포넌트를 브라우저에서 사용가능한 하나의 자바스크립트 파일로 컴파일 할 수 있습니다.

#### Frontend 스캐폴딩 삭제하기

애플리케이션에서 프론트엔드 스캐폴딩을 제거하려면, `preset` 아티즌 명령어를 사용하면 됩니다. 이 명령어가 `none` 옵션과 함께 사용되면, 애플리케이션에 비어 있는 SASS 파일과 몇몇 자바스크립트 유틸리티 라이브러리를 남겨 놓고, 부트스트랩과 Vue 스캐폴딩을 제거합니다.

    php artisan preset none

<a name="writing-css"></a>
## CSS 작성하기

`laravel/ui` Composer 패키지와 [프론트 엔드 스캐 폴딩 생성](#introduction)을 설치 한 후, 라라벨의 `package.json` 파일은 부트스트랩을 사용하여 애플리케이션의 프론트엔드 프로토타이핑을 시작하는 것을 돕는 `bootstrap` 패키지를 포함하고 있습니다. 그렇지만 애플리케이션에 필요한 경우 자유롭게 `package.json` 파일에서 패키지를 추가하거나, 삭제하면 됩니다. 라라벨 애플리케이션을 구성하는데 부트스트랩 프레임워크를 사용하는 것이 필요하지는 않습니다 - 부트스트랩을 사용하기로 선택한 사람들을 위해서 시작점을 제공하고 있을 뿐입니다.

CSS 를 컴파일 하기 전에, [Node 패키지 매니저 (NPM)](https://www.npmjs.org)을 사용하여 여러분의 프로젝트의 프론트엔드 의존성을 설치하십시오:

    npm install

`npm install` 을 사용하여 의존성을 설치하고 나면, [라라벨 Mix](/docs/{{version}}/mix#working-with-stylesheets)를 사용하여 SASS 파일을 순수 CSS 로 컴파일 할 수 있습니다. `npm run dev` 명령어는 `webpack.mix.js` 파일 안에 있는 명령들을 처리할 것입니다. 일반적으로 컴파일된 CSS 파일은 `public/css` 디렉토리에 위치할 것입니다.

    npm run dev

라라벨에 프론트엔드 스캐폴딩에 포함된 `webpack.mix.js`는 `resources/sass/app.scss` SASS 파일을 컴파일 할 것입니다. 이 `app.scss` 파일은 SASS 변수들을 가져오고 대부분의 애플리케이션을 위한 좋은 시작점이 되는 제공하는부트스트랩을 로딩합니다. 이 `app.scss` 파일은 자유롭게 수정할 수 있지만, 원하는 경우 완전히 다른 전처리기를 [라라벨 Mix에서 설정하여](/docs/{{version}}/mix) 사용할 수도 있습니다.

<a name="writing-javascript"></a>
## 자바스크립트 작성하기

애플리케이션에 필요한 모든 자바스크립트 의존성들은 프로젝트 루트 디렉토리 안에 있는 `package.json` 파일 안에서 찾을 수 있습니다. 이 파일은 PHP 의존성 대신 자바스크립트 의존성이 지정되어 있다는 점을 제외하면 `composer.json` 파일과 비슷합니다. [Node 패키지 매니저 (NPM)](https://www.npmjs.org)을 사용하여 이 의존성들을 설치할 수 있습니다.

    npm install

> {tip} 기본적으로 라라벨의 `package.json` 파일은 자바스크립트 애플리케이션을 구성하는데 도움을 줄 수 있는`lodash` 와 `axios` 와 같은 몇몇 패키지를 포함하고 있습니다. 자유롭게 `package.json` 파일에 애플리케이션에서 필요한 의존성들을 추가하거나 삭제할 수 있습니다.

패키지들이 설치되고 나면, `npm run dev` 명령어를 사용하여 [asset 을 컴파일](/docs/{{version}}/mix) 할 수 있습니다. Webpack은 현대적인 자바스크립트 애플리케이션을 위한 모듈 번들러 입니다. `npm run dev` 명령어를 실행하면, Webpack 는 `webpack.mix.js` 파일안에 있는 명령어들을 실행할 것입니다.

    npm run dev

기본적으로 라라벨의 `webpack.mix.js` 파일은 SASS 와 `resources/js/app.js`파일을 컴파일 합니다. `app.js` 파일 안에서 Vue 컴포넌트나, 다른 프레임워크를 좋아한다면, 고유한 자바스크립트 애플리케이션을 구성할 수 있습니다. 컴파일된 자바스크립트는 일반적으로 `public/js` 디렉토리 안에 위치할 것입니다.

> {tip} `app.js` 파일은 `resources/js/bootstrap.js`을 로드하여 Vue, Axios, jQuery 그리고 다른 모든 자바스크립트 의존성들을 설정하고 구동할 것입니다. 만약 추가적인 자바스크립트 의존성을 설정해야 한다면, 이 파일안에서 할 수 있습니다.

<a name="writing-vue-components"></a>
### Vue 컴포넌트 작성하기

`laravel/ui` 패키지를 사용하여 프론트엔드를 스캐폴딩하면 `ExampleComponent.vue` Vue 컴포넌트가 `resources/js/components` 디렉토리에 배치됩니다. `ExampleComponent.vue` 파일은 동일한 파일 안에서 자바스크립트와 HTML 템플릿을 정의한 [파일 하나로 된 Vue 컴포넌트](https://vuejs.org/guide/single-file-components)의 예제 입니다. 하나의 파일로된 컴포넌트는 자바스크립트 기반의 애플리케이션을 구성하는데 매우 편리한 방법을 제공합니다. 이 예제 컴포넌트는 `app.js` 에 등록되어 있습니다.

    Vue.component(
        'example-component',
        require('./components/ExampleComponent.vue').default
    );

애플리케이션에서 이 컴포넌트를 사용하려면, HTML 템플릿 안에 등록하면 됩니다. 예를 들어 애플리케이션의 인증과 회원 가입 화면을 스캐폴딩 하기 위해서 `php artisan ui vue --auth` 아티즌 명령어를 실행 한 다음에 컴포넌트를 `home.blade.php` 블레이드 템플릿 안에 등록할 수 있습니다.

    @extends('layouts.app')

    @section('content')
        <example-component></example-component>
    @endsection

> {tip} 주의할 것은, Vue 컴포넌트가 바뀔 때 마다 `npm run dev` 명령어를 실행해야 한다는 것입니다. 또는 `npm run watch` 명령어를 실행하여 파일들이 변경될 때 이를 모니터링 하고 있다가 자동으로 다시 컴파일 하도록 할 수 있습니다.

Vue 컴포넌트를 작성하는데 관심이 있다면, 전체 Vue 프레임워크에 대해서 개념을 손쉽게 읽을 수 있는 [Vue 매뉴얼](https://kr.vuejs.org/v2/guide/index.html)을 확인하길 바랍니다.

<a name="using-react"></a>
### React 사용하기

React를 사용하여 JavaScript 애플리케이션을 빌드하려는 경우 Laravel은 Vue 스캐폴딩을 React 스캐폴딩으로 교체 할 수 있습니다.

    composer require laravel/ui
    
    // Generate basic scaffolding...
    php artisan ui react

    // Generate login / registration scaffolding...
    php artisan ui react --auth

<a name="adding-presets"></a>
## 프리셋-Presets 추가하기

프리셋-Presets은 "macroable"하여 런타임에 `UiCommand` 클래스에 메소드를 추가 할 수 있습니다. 예를 들어, 다음 코드는 `nextjs` 메소드를 `UiCommand` 클래스에 추가합니다. 일반적으로 [서비스 프로바이더](/docs/{{version}}/providers)에서 프리셋 매크로를 선언해야합니다.

    use Laravel\Ui\UiCommand;

    UiCommand::macro('nextjs', function (UiCommand $command) {
        // Scaffold your frontend...
    });

그런 다음 `ui` 명령을 통해 새로운 프리셋을 호출 할 수 있습니다.

    php artisan ui nextjs
