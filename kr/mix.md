# Compiling Assets (Mix)
# Assets 컴파일 하기 (Mix)

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation & Setup](#installation)
- [설치하기 & 설정하기](#installation)
- [Running Mix](#running-mix)
- [Mix 실행하기](#running-mix)
- [Working With Stylesheets](#working-with-stylesheets)
- [스타일시트 작업하기](#working-with-stylesheets)
  - [Tailwind CSS](#tailwindcss)
  - [테일윈드 CSS](#tailwindcss)
  - [PostCSS](#postcss)
  - [PostCSS](#postcss)
  - [Sass](#sass)
  - [Sass](#sass)
  - [URL Processing](#url-processing)
  - [URL Processing](#url-processing)
  - [Source Maps](#css-source-maps)
  - [소스 맵](#css-source-maps)
- [Working With JavaScript](#working-with-scripts)
- [자바스크립트 작업하기](#working-with-scripts)
    - [Vue](#vue)
    - [Vue](#vue)
    - [React](#react)
    - [React](#react)
    - [Vendor Extraction](#vendor-extraction)
    - [Vendor 분할](#vendor-extraction)
    - [Custom Webpack Configuration](#custom-webpack-configuration)
    - [커스텀 Webpack 설정](#custom-webpack-configuration)
- [Versioning / Cache Busting](#versioning-and-cache-busting)
- [버전 관리 / 캐시 갱신](#versioning-and-cache-busting)
- [Browsersync Reloading](#browsersync-reloading)
- [Browsersync 리로딩](#browsersync-reloading)
- [Environment Variables](#environment-variables)
- [환경변수](#environment-variables)
- [Notifications](#notifications)
- [Notifications-알림](#notifications)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Mix](https://github.com/JeffreyWay/laravel-mix), a package developed by [Laracasts](https://laracasts.com) creator Jeffrey Way, provides a fluent API for defining [webpack](https://webpack.js.org) build steps for your Laravel application using several common CSS and JavaScript pre-processors.

[라라벨 믹스](https://github.com/JeffreyWay/laravel-mix)는 [Laracasts](https://laracasts.com)를 만든 제프리 웨이(Jeffrey Way)에 의해 개발된 패키지로 웹펙을 정의하고 빌드하는데 빼어난 API를 제공합니다. 라라벨 믹스를 통해 애플리케이션에서 common CSS와 자바스크립트 전처리기(pre-processors)를 사용할 수 있습니다.

In other words, Mix makes it a cinch to compile and minify your application's CSS and JavaScript files. Through simple method chaining, you can fluently define your asset pipeline. For example:

이 말은 라라벨 믹스를 사용하면 라라벨 애플리케이션의 CSS와 자바스크립트 파일을 손쉽게 컴파일하고 압축할 수 있다는 말입니다. 간단한 메서드 체이닝을 통해서 에셋(asset) 파이프라인을 전문가처럼(fluently) 정의할 수 있습니다. 다음의 예를 보세요.

    mix.js('resources/js/app.js', 'public/js')
        .sass('resources/sass/app.scss', 'public/css');

If you've ever been confused and overwhelmed about getting started with Webpack and asset compilation, you will love Laravel Mix. However, you are not required to use it while developing your application; you are free to use any asset pipeline tool you wish, or even none at all.

혹시라도 Webpack 과 asset 컴파일을 혼란스럽고 부담을 느끼고 있다면, 라라벨 Mix를 좋아하게 될 것입니다. 하지만 애플리케이션을 개발할 때 라라벨 Mix가 꼭 필요한 건 아닙니다. 여러분이 원하는 어느 asset 파이프라인 툴을 사용해됩니다. 또는 이러한 기능을 사용하지 않아도 괜찮습니다.

> {tip} If you need a head start building your application with Laravel and [Tailwind CSS](https://tailwindcss.com), check out one of our [application starter kits](/docs/{{version}}/starter-kits).

> {tip} 여러분이 라라벨로 애플리케이션 빌드를 시작해야 하는 경우 [테일윈드 CSS](https://tailwindcss.com), [애플리케이션 스타터 킷](/docs/{{version}}/starter-kits)에 포함된 것들 중 하나를 확인해 보세요.

<a name="installation"></a>
## Installation & Setup
## 설치하기 & 설정하기

<a name="installing-node"></a>
#### Installing Node
#### Node 설치하기

Before triggering Mix, you must first ensure that Node.js and NPM are installed on your machine.

Mix를 사용하기전 작업환경에 Node.js와 NPM이 설치되어있는지 확인하세요.

    node -v
    npm -v

You can easily install the latest version of Node and NPM using simple graphical installers from [the official Node website](https://nodejs.org/en/download/). Or, if you are using [Laravel Sail](/docs/{{version}}/sail), you may invoke Node and NPM through Sail:

[공식 노드JS 웹 사이트](https://nodejs.org/en/download/)에서 최신 버전의 노드JS와 NPM을 심플한 그래픽의 인스톨러로 간단히 설치할 수 있습니다. 또는 [Laravel Sail](/docs/{{version}}/sail)을 사용한다면 Sail 가상환경에서 노드JS와 NPM을 호출할 수 있습니다.

    ./sail node -v
    ./sail npm -v

<a name="installing-laravel-mix"></a>
#### Installing Laravel Mix
#### 라라벨 Mix 설치하기

The only remaining step is to install Laravel Mix. Within a fresh installation of Laravel, you'll find a `package.json` file in the root of your directory structure. The default `package.json` file already includes everything you need to get started using Laravel Mix. Think of this file like your `composer.json` file, except it defines Node dependencies instead of PHP dependencies. You may install the dependencies it references by running:

이제 남은 과정은 라라벨 Mix를 설치하는 것 뿐입니다. 라라벨을 새로 설치하면 폴더 구조의 루트 폴더에 위치한 `package.json` 파일을 볼 수 있습니다. 이 디폴트 `package.json`파일에는 애플리케이션에서 웹펙을 빌드하기 위해 필요한 기본적인 설정 및 패키지 리스트들이 이미 포함되어 있습니다. 이 파일은 PHP 대신 Node 의존 패키지를 정의 한다는 것 빼고는 `composer.json`과 동일합니다. 아래의 명령어로 의존 패키지들을 설치할 수 있습니다.

    npm install

<a name="running-mix"></a>
## Running Mix
## Mix 실행하기

Mix is a configuration layer on top of [webpack](https://webpack.js.org), so to run your Mix tasks you only need to execute one of the NPM scripts that are included in the default Laravel `package.json` file. When you run the `dev` or `production` scripts, all of your application's CSS and JavaScript assets will be compiled and placed in your application's `public` directory:

Mix는 [webpack](https://webpack.js.org)를 레핑하고 있는 설정 레이어입니다. Mix 작업을 실행하려면 기본 Laravel `package.json` 파일 에 포함된 NPM 스크립트 중 하나를 실행하면 됩니다. `dev` 또는 `production` 스크립트를 실행할 때, 애플리케이션의 모든 CSS와 자바스크립트 에셋(assets)은 컴파일 되고 애플리케이션의 `public` 폴더에 위치하게 됩니다.

    // Run all Mix tasks...
    npm run dev

    // Run all Mix tasks and minify output...
    npm run prod

<a name="watching-assets-for-changes"></a>
#### Watching Assets For Changes
#### Assets의 변경사항 감시하기

The `npm run watch` command will continue running in your terminal and watch all relevant CSS and JavaScript files for changes. Webpack will automatically recompile your assets when it detects a change to one of these files:

`npm run watch` 명령어는 터미널에서 종료되지 않고 계속 실행되며 연결되어 있는(relevant) CSS 파일과 자바스크립트 파일의 변화를 감지(watch)합니다. 웹펙은 감시되고 있는 파일에서 하나의 변경 사항이라도 발생하면 에셋(assets)을 자동적으로 다시 컴파일(recompile) 합니다.

    npm run watch

Webpack may not be able to detect your file changes in certain local development environments. If this is the case on your system, consider using the `watch-poll` command:

특정한 상황의 로컬 개발 환경에서는 웹펙이 파일의 변경을 감지하지 않도록 설정할 수 있습니다. 파일 변경이 아닌 일정 시간 주기로 빌드하도록 하려면 `watch-poll` 명령어 사용을 고려하십시오.

    npm run watch-poll

<a name="working-with-stylesheets"></a>
## Working With Stylesheets
## 스타일시트 작업하기

Your application's `webpack.mix.js` file is your entry point for all asset compilation. Think of it as a light configuration wrapper around [webpack](https://webpack.js.org). Mix tasks can be chained together to define exactly how your assets should be compiled.

`webpack.mix.js` 파일은 모든 에셋 컴파일의 시작점(entry point) 입니다. 이 파일은 [webpack](https://webpack.js.org)을 래핑한 가벼운 설정이라고 여기면 됩니다. 믹스 작업(tasks)은 어떻게 컴파일 되어야 하는지에 관해 명확히 정의하기 위해서 서로 체이닝 될 수 있도록 만들어져 있습니다.

<a name="tailwindcss"></a>
### Tailwind CSS
### 테일윈드 CSS

[Tailwind CSS](https://tailwindcss.com) is a modern, utility-first framework for building amazing sites without ever leaving your HTML. Let's dig into how to start using it in a Laravel project with Laravel Mix. First, we should install Tailwind using NPM and generate our Tailwind configuration file:

테일윈드는 HTML 작업을 어려워하거나 싫어하는 사람들에게 한 줄기 희망이 되어주는 프레임워크로 편의성을 최우선적으로 생각합니다. 라라벨 믹스를 통해 라라벨 프로젝트에서 테일윈드를 어떻게 사용하는지 알아 봅시다. 처음에는 테일윈드 패키지를 NPM으로 설치하고 테일윈드 설정 파일을 생성해야 합니다.

    npm install

    npm install -D tailwindcss

    npx tailwindcss init

The `init` command will generate a `tailwind.config.js` file. The `content` section of this file allows you to configure the paths to all of your HTML templates, JavaScript components, and any other source files that contain Tailwind class names so that any CSS classes that are not used within these files will be purged from your production CSS build:

`init` 명령어는 `tailwind.config.js` 파일을 생성합니다. 이 파일의 `content` 섹션에는 애플리케이션의 모든 HTML 템플릿, 자바스크립트, 컴포넌트, 다른 여러 소스 파일들의 경로를 설정합니다. 각각의 파일에는 테일윈드 클레스 이름들이 포함되어 있습니다. 각 파일에서 테일윈드 클래스가 선언 되어 있더라도 테일윈드 클래스를 사용하지 않는다면 프로덕션 빌드 시 선언된 테일윈드 클래스는 제거됩니다.

```js
content: [
    './storage/framework/views/*.php',
    './resources/**/*.blade.php',
    './resources/**/*.js',
    './resources/**/*.vue',
],
```

Next, you should add each of Tailwind's "layers" to your application's `resources/css/app.css` file:

다음으로 애플리케이션의 `resources/css/app.css` 파일에 테일윈드 레이어를 추가합니다.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Once you have configured Tailwind's layers, you are ready to update your application's `webpack.mix.js` file to compile your Tailwind powered CSS:

테일윈드의 레어어를 설정했다면, 애플리케이션의 `webpack.mix.js` 파일에서 테일윈드 CSS를 컴파일 할 수 있도록 설정합니다.

```js
mix.js('resources/js/app.js', 'public/js')
    .postCss('resources/css/app.css', 'public/css', [
        require('tailwindcss'),
    ]);
```

Finally, you should reference your stylesheet in your application's primary layout template. Many applications choose to store this template at `resources/views/layouts/app.blade.php`. In addition, ensure you add the responsive viewport `meta` tag if it's not already present:

마지막으로 애플리케이션에서 선행 분기점이 되는 레이아웃 템플릿(primary layout template)에서 스타일시트를 참조해야 합니다. 많은 애플리케이션에서 이 템플릿을 저장하기 위해 `resources/views/layouts/app.blade.php`템플릿을 선택했습니다. 또한 반응형 뷰포트를 지정하는 `meta` 태그가 없다면 추가해 주어야 합니다.

```html
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link href="/css/app.css" rel="stylesheet">
</head>
```

<a name="postcss"></a>
### PostCSS
### PostCSS

[PostCSS](https://postcss.org/), a powerful tool for transforming your CSS, is included with Laravel Mix out of the box. By default, Mix leverages the popular [Autoprefixer](https://github.com/postcss/autoprefixer) plug-in to automatically apply all necessary CSS3 vendor prefixes. However, you're free to add any additional plug-ins that are appropriate for your application.

[PostCSS](https://postcss.org/)는 추가적인 설치 없이도, 라라벨 Mix에 기본적으로 포함되어 사용할 수 있는 CSS 변환툴입니다. 기본적으로 Mix는 널리 사용되는 [Autoprefixer](https://github.com/postcss/autoprefixer) 플러그인을 사용하여 필요한 모든 CSS3 vendor 접미어(prefix)를 자동으로 적용합니다. 그러나 PostCSS를 사용하지 않고도 애플리케이션에 적합한 추가 플러그인을 자유롭게 추가할 수 있습니다.

First, install the desired plugin through NPM and include it in your array of plugins when calling Mix's `postCss` method. The `postCss` method accepts the path to your CSS file as its first argument and the directory where the compiled file should be placed as its second argument:

먼저 NPM 명령어를 사용해 원하는 PostCSS 플러그인을 설치합니다. 라라벨 Mix에서 `postCss` 메소드를 호출할 때 PostCSS가 사용할 플러그인을 지정하려면 `postCss` 메소드의 세 번째 인자에 배열로 지정합니다. `postCss` 메소드의 첫 번째 인자는 CSS 파일의 경로를 지정하고, 두 번째 인자는 컴파일 된 파일이 저장되어야 할 폴더를 지정합니다.

    mix.postCss('resources/css/app.css', 'public/css', [
        require('postcss-custom-properties')
    ]);

Or, you may execute `postCss` with no additional plugins in order to achieve simple CSS compilation and minification:

또는 추가 플러그인 없이 CSS 컴파일 및 압축만을 간단하게 적용하기 위해 `postCss` 메소드를 사용할 수 있습니다.

    mix.postCss('resources/css/app.css', 'public/css');


<a name="sass"></a>
### Sass
### Sass

The `sass` method allows you to compile [Sass](https://sass-lang.com/) into CSS that can be understood by web browsers. The `sass` method accepts the path to your Sass file as its first argument and the directory where the compiled file should be placed as its second argument:

`sass` 메소드는 [Sass](https://sass-lang.com/)파일을 CSS 파일로 컴파일 합니다. 이 메소드는 다음과 같이 사용할 수 있습니다. `sass` 메소드는 첫 번째 인자로 sass 파일의 경로를 지정하고 두 번째 인자로 컴파일된 파일이 위치해야 하는 디렉토리를 지정합니다.

    mix.sass('resources/sass/app.scss', 'public/css');

You may compile multiple Sass files into their own respective CSS files and even customize the output directory of the resulting CSS by calling the `sass` method multiple times:

`sass` 메소드를 여러번 호출하는 것으로 여러 Sass 파일을 각각의 CSS 파일로 컴파일할 수 있으며 컴파일된 CSS 파일이 위치할 디렉토리를 지정할 수 있습니다.

    mix.sass('resources/sass/app.sass', 'public/css')
        .sass('resources/sass/admin.sass', 'public/css/admin');

<a name="url-processing"></a>
### URL Processing
### URL 프로세싱

Because Laravel Mix is built on top of Webpack, it's important to understand a few Webpack concepts. For CSS compilation, Webpack will rewrite and optimize any `url()` calls within your stylesheets. While this might initially sound strange, it's an incredibly powerful piece of functionality. Imagine that we want to compile Sass that includes a relative URL to an image:

Laravel Mix는 Webpack 위에 구축되었기 때문에 몇 가지 Webpack 개념을 이해하는 것이 중요합니다. CSS 컴파일의 경우 Webpack은 스타일시트 내의 모든 `url()` 호출을 재작성하고 최적화합니다. 처음에는 이상해보일 수도 있지만, 이는 믿을 수 없을 정도로 강력한 기능입니다. 특정 이미지의 상대경로를 포함하고 있는 Sass 파일을 컴파일 한다고 생각해보겠습니다.

    .example {
        background: url('../images/example.png');
    }

> {note} Absolute paths for any given `url()` will be excluded from URL-rewriting. For example, `url('/images/thing.png')` or `url('http://example.com/images/thing.png')` won't be modified.

> {note} `url()`에 주어진 절대 경로들은 URL-재작성에서 제외됩니다. 예를 들어, `url('/images/thing.png')` 와 `url('http://example.com/images/thing.png')` 는 수정되지 않습니다.

By default, Laravel Mix and Webpack will find `example.png`, copy it to your `public/images` folder, and then rewrite the `url()` within your generated stylesheet. As such, your compiled CSS will be:

기본적으로, 라라벨 Mix와 Webpack은 `example.png` 파일을 찾아 이를 `public/images` 폴더에 복사하고, 생성된 스타일시트 내에서 `url()`을 재작성합니다. 그 결과, 컴파일된 CSS는 다음과 같습니다.

    .example {
        background: url(/images/example.png?d41d8cd98f00b204e9800998ecf8427e);
    }

As useful as this feature may be, it's possible that your existing folder structure is already configured in a way you like. If this is the case, you may disable `url()` rewriting like so:

이 기능이 유용할 수 있지만 기존 폴더 구조가 이미 원하는 방식으로 설정되어 있을 수 있습니다. 이런 경우에는 `url()`의 재작성 동작을 다음처럼 비활성화 할 수 있습니다.

    mix.sass('resources/sass/app.scss', 'public/css').options({
        processCssUrls: false
    });

With this addition to your `webpack.mix.js` file, Mix will no longer match any `url()` or copy assets to your public directory. In other words, the compiled CSS will look just like how you originally typed it:

`webpack.mix.js` 파일에서 url 재작성 동작을 비활성화하면, Mix는 더 이상 어떠한 `url()` 또는 복사된 에셋을 public 폴더 내의 파일 경로와 매칭하지 않을 것입니다. 다시 말해서 컴파일된 CSS는 컴파일 전의 원본 파일과 똑같은 url을 가집니다.

    .example {
        background: url("../images/thing.png");
    }

<a name="css-source-maps"></a>
### Source Maps
### 소스 맵

Though disabled by default, source maps may be activated by calling the `mix.sourceMaps()` method in your `webpack.mix.js` file. Though it comes with a compile/performance cost, this will provide extra debugging information to your browser's developer tools when using compiled assets:

기본적으로는 비활성화 되어 있지만, `webpack.mix.js` 파일에서 `mix.sourceMaps()`을 호출하여 소스 맵을 활성화 시킬 수 있습니다. 컴파일하는데 성능상의 비용이 들지만, 컴파일 된 asset을 사용하면 브라우저의 개발자 도구에서 추가적인 디버깅 정보를 확인할 수 있습니다.

    mix.js('resources/js/app.js', 'public/js')
        .sourceMaps();

<a name="style-of-source-mapping"></a>
#### Style Of Source Mapping
#### 소스 매핑 스타일

Webpack offers a variety of [source mapping styles](https://webpack.js.org/configuration/devtool/#devtool). By default, Mix's source mapping style is set to `eval-source-map`, which provides a fast rebuild time. If you want to change the mapping style, you may do so using the `sourceMaps` method:

Webpack은 다양한 [소스 매핑 스타일](https://webpack.js.org/configuration/devtool/#devtool)을 제공합니다. 기본적으로 Mix의 소스 매핑 스타일은 `eval-source-map`으로 설정되어있어 빠른 rebuild를 제공합니다. 매핑 스타일을 변경하려면 `sourceMaps` 메소드를 사용하십시오.

    let productionSourceMaps = false;

    mix.js('resources/js/app.js', 'public/js')
        .sourceMaps(productionSourceMaps, 'source-map');

<a name="working-with-scripts"></a>
## Working With JavaScript
## 자바스크립트 작업하기

Mix provides several features to help you work with your JavaScript files, such as compiling ECMAScript 2015, module bundling, minification, and concatenating plain JavaScript files. Even better, this all works seamlessly, without requiring an ounce of custom configuration:

Mix는 자바스크립트 작업에 도움이 될만한 몇 가지 기능을 제공합니다. ECMAScript 2015 컴파일, 모듈 번들링, 파일 압축(minification), 브라우저에서 일반적으로 바로 로딩할 수 있는 자바스크립트(plain JavaScript) 파일의 결합 등입니다. 더 나아가 이 모든 기능은 여러가지 별도의 설정을 할 필요없이 원활하게(seamlessly) 동작합니다.

    mix.js('resources/js/app.js', 'public/js');

With this single line of code, you may now take advantage of:

이 한줄의 코드로 다음의 기능들을 취할 수 있습니다.

<div class="content-list" markdown="1">
- The latest EcmaScript syntax.
- Modules
- Minification for production environments.
</div>

<div class="content-list" markdown="1">
- 최신 에크마 스크립트 문법
- 모듈들
- 프로덕션 환경을 위한 압축
</div>

<a name="vue"></a>
### Vue
### 뷰

Mix will automatically install the Babel plugins necessary for Vue single-file component compilation support when using the `vue` method. No further configuration is required:

`vue` 메소드를 사용하면 Mix는 단일 파일의 vue 컴포넌트를 컴파일 하기 위해 필요한 Babel 플러그인을 자동으로 설치합니다. 따라서 Vue를 컴파일 하기 위해서 추가적인 구성이 필요하지 않습니다.

    mix.js('resources/js/app.js', 'public/js')
       .vue();

Once your JavaScript has been compiled, you can reference it in your application:

JavaScript가 컴파일되면 애플리케이션에서 참조할 수 있습니다.

```html
<head>
    <!-- ... -->

    <script src="/js/app.js"></script>
</head>
```

<a name="react"></a>
### React
### 리액트

Mix can automatically install the Babel plugins necessary for React support. To get started, add a call to the `react` method:

Mix는 리액트에 필요한 바벨 플러그인을 자동적으로 인스톨 할 수 있습니다. 시작하려면 `react` 메소드를 호출하도록 고려하세요.

    mix.js('resources/js/app.jsx', 'public/js')
       .react();

Behind the scenes, Mix will download and include the appropriate `babel-preset-react` Babel plugin. Once your JavaScript has been compiled, you can reference it in your application:

내부적으로 Mix는 미리 적절히 설정 된(the appropriate) `babel-preset-react` Babel 플러그인을 포함하여 다운로드 합니다. JavaScript가 컴파일되면 애플리케이션에서 참조할 수 있습니다.

```html
<head>
    <!-- ... -->

    <script src="/js/app.js"></script>
</head>
```

<a name="vendor-extraction"></a>
### Vendor Extraction
### Vendor 분할

One potential downside to bundling all of your application-specific JavaScript with your vendor libraries such as React and Vue is that it makes long-term caching more difficult. For example, a single update to your application code will force the browser to re-download all of your vendor libraries even if they haven't changed.

애플리케이션에 따라 별도로 만들어지는 자바스크립트와 vendor 라이브러리의 자바스크립트(React와 Vue와 같은)를 함께 번들로 묶는 것의 한 가지 단점(downside)은 장기 캐싱(long-term caching)을 어렵게 만든다는 것입니다. 예를 들어서 애플리케이션 코드의 한 부분을 업데이트 했을 때 브라우저는 바뀐 부분이 없는 파일을 포함한 모든 vendor 라이브러리를 다시 다운로드 해야 합니다.

If you intend to make frequent updates to your application's JavaScript, you should consider extracting all of your vendor libraries into their own file. This way, a change to your application code will not affect the caching of your large `vendor.js` file. Mix's `extract` method makes this a breeze:

애플리케이션의 자바스크립트를 자주 업데이트 한다면, vendor 라이브러리를 별도로 분리(extracting)하는 것을 고려해야 합니다. 이렇게 하면 애플리케이션의 코드가 변경되더라도 변경 사항이 없는 큰 용량의 `vendor.js` 파일의 캐싱에는 영향을 주지 않습니다. Mix의 `extract` 메소드는 다음과 같이 처리합니다.

    mix.js('resources/js/app.js', 'public/js')
        .extract(['vue'])

The `extract` method accepts an array of all libraries or modules that you wish to extract into a `vendor.js` file. Using the above snippet as an example, Mix will generate the following files:

`extract` 메소드는 `vendor.js` 파일로 별도 구성(추출)하고자 하는 라이브러리 혹은 모듈의 배열을 전달 받습니다. 앞선 예의 경우에는 Mix는 다음 파일을 생성합니다.

<div class="content-list" markdown="1">
- `public/js/manifest.js`: *The Webpack manifest runtime*
- `public/js/vendor.js`: *Your vendor libraries*
- `public/js/app.js`: *Your application code*
</div>

<div class="content-list" markdown="1">
- `public/js/manifest.js`: *웹펙의 manifest 런타임*
- `public/js/vendor.js`: *애플리케이션의 vendor 라이브러리들*
- `public/js/app.js`: *애플리케이션의 자바스크립트 코드*
</div>

To avoid JavaScript errors, be sure to load these files in the proper order:

자바 스크립트 오류를 방지하려면 적절한 순서로 다음 파일들을 로드해야합니다.

    <script src="/js/manifest.js"></script>
    <script src="/js/vendor.js"></script>
    <script src="/js/app.js"></script>

<a name="custom-webpack-configuration"></a>
### Custom Webpack Configuration
### 커스텀 Webpack 설정

Occasionally, you may need to manually modify the underlying Webpack configuration. For example, you might have a special loader or plugin that needs to be referenced.

때때로 Webpack을 원본 설정을(underlying) 수동으로 수정해야 할 수도 있습니다. 예를 들어, 애플리케이션 별도로 참조해야 하는 로더(loader) 또는 플러그인이 있을 수 있습니다.

Mix provides a useful `webpackConfig` method that allows you to merge any short Webpack configuration overrides. This is particularly appealing, as it doesn't require you to copy and maintain your own copy of the `webpack.config.js` file. The `webpackConfig` method accepts an object, which should contain any [Webpack-specific configuration](https://webpack.js.org/configuration/) that you wish to apply.

Mix는 웹펙의 설정을 재정의(override) 하거나 추가할 수 있도록 간단한 웹펙 설정 코드들을 웹펙 설정에 합쳐주는 `webpackConfig` 메서드를 제공합니다. 이 메소드를 사용하면 `webpack.config.js` 파일의 사본을 복사하고 유지/관리(maintain)할 필요가 없기 때문에 특히 매력적입니다. `webpackConfig` 메서드의 인자로는 [Webpack 설정](https://webpack.js.org/configuration/)에 포함되어 있는 어떠한 객체도 지정 될 수 있습니다.

    mix.webpackConfig({
        resolve: {
            modules: [
                path.resolve(__dirname, 'vendor/laravel/spark/resources/assets/js')
            ]
        }
    });

<a name="versioning-and-cache-busting"></a>
## Versioning / Cache Busting
## 버전 관리 / 캐시 갱신

Many developers suffix their compiled assets with a timestamp or unique token to force browsers to load the fresh assets instead of serving stale copies of the code. Mix can handle this for you using the `version` method.

많은 개발자들은 브라우저에 캐싱되어 있는 지난 버전(stale)의 코드 복사본을 웹에서 제공하지 않도록 하고 새로운 에셋(assets)이 브라우저에서 강제로 로드 될 수 있도록 타임스템프나 유니크 토큰을 컴파일된 에셋의 접미사에 붙입니다. Mix는 `version` 메소드를 사용여 이러한 문제를 처리(handle)합니다.

The `version` method will automatically append a unique hash to the filenames of all compiled files, allowing for more convenient cache busting:

`version` 메소드는 컴파일 된 파일의 이름 뒤에 자동으로 고유한 hash를 덧붙여 좀 더 편리하게 캐시를 날릴 수 있도록 합니다.

    mix.js('resources/js/app.js', 'public/js')
        .version();

After generating the versioned file, you won't know the exact file name. So, you should use Laravel's global `mix` function within your [views](/docs/{{version}}/views) to load the appropriately hashed asset. The `mix` function will automatically determine the current name of the hashed file:

버전이 지정된 파일이 생성되면, 여러분은 정확한 파일 이름을 알 수가 없습니다. 따라서 [뷰-views](/docs/{{version}}/views)에서 라라벨의 글로벌 `mix` 헬퍼를 사용하여 해시값이 붙은 asset 을 로딩할 수 있습니다. `mix` 함수는 자동으로 해시값이 붙어 있는 현재의 파일 이름을 결정합니다.

    <script src="{{ mix('/js/app.js') }}"></script>

Because versioned files are usually unnecessary in development, you may instruct the versioning process to only run during `npm run production`:

개발중에는 버전이 지정된 파일이 항상 필요하지는 않기 때문에, `npm run production` 일 때만 파일 경로에 버전 식별자가 붙도록(versioning) 처리를 지시할 수 있습니다.

    mix.js('resources/js/app.js', 'public/js');

    if (mix.inProduction()) {
        mix.version();
    }

<a name="custom-mix-base-urls"></a>
#### Custom Mix Base URLs
#### 믹스 기본 URL 커스텀

If your Mix compiled assets are deployed to a CDN separate from your application, you will need to change the base URL generated by the `mix` function. You may do so by adding a `mix_url` configuration option to your application's `config/app.php` configuration file:

Mix로 컴파일 된 assets이 애플리케이션과 별도로 CDN에 배포 할 경우 `mix` 함수로 생성 된 기본 URL을 변경해야합니다. 각 파일의 url을 변경할 필요 없이 애플리케이션의 `config/app.php` 설정 파일에서 `mix_url` 설정 옵션을 추가하면됩니다.

    'mix_url' => env('MIX_ASSET_URL', null)

After configuring the Mix URL, The `mix` function will prefix the configured URL when generating URLs to assets:

믹스 URL을 구성한 후 `mix` 함수는 assets에 대한 URL을 생성 할 때 설정된 URL을 앞에 붙입니다. 적용된 url을 생성하기 위해서는 새로 빌드해야 합니다.

```bash
https://cdn.example.com/js/app.js?id=1964becbdd96414518cd
```

<a name="browsersync-reloading"></a>
## Browsersync Reloading
## Browsersync 리로딩

[BrowserSync](https://browsersync.io/) can automatically monitor your files for changes, and inject your changes into the browser without requiring a manual refresh. You may enable support for this by calling the `mix.browserSync()` method:

[BrowserSync](https://browsersync.io/)는 파일의 변경사항을 감시하고 있다가, 수동으로 페이지를 다시 로드하지 않아도 자동으로 변경 사항을 브라우저에 반영합니다. `mix.browserSync()` 메소드를 호출하여 이 Browsersync 기능을 활성화 시킬 수 있습니다.

```js
mix.browserSync('laravel.test');
```

[BrowserSync options](https://browsersync.io/docs/options) may be specified by passing a JavaScript object to the `browserSync` method:

[BrowserSync 옵션](https://browsersync.io/docs/options) 은 JavaScript 객체를 `browserSync` 메서드에 전달하여 지정할 수 있습니다.

```js
mix.browserSync({
    proxy: 'laravel.test'
});
```

Next, start webpack's development server using the `npm run watch` command. Now, when you modify a script or PHP file you can watch as the browser instantly refreshes the page to reflect your changes.

다음으로 `npm run watch` 명령을 사용하여 웹팩의 개발 서버를 시작합니다. 이제 스크립트나 PHP 파일을 수정할 때 해당 페이지의 변경 사항을 반영하여 즉시 리프레쉬 되는 것을 브라우저에서 확인할 수 있습니다.

<a name="environment-variables"></a>
## Environment Variables

You may inject environment variables into your `webpack.mix.js` script by prefixing one of the environment variables in your `.env` file with `MIX_`:

`.env` 파일의 환경 변수 중 하나에 `MIX_`를 접두사로 추가하여 `webpack.mix.js` 스크립트에 환경 변수를 삽입할 수 있습니다.

    MIX_SENTRY_DSN_PUBLIC=http://example.com

After the variable has been defined in your `.env` file, you may access it via the `process.env` object. However, you will need to restart the task if the environment variable's value changes while the task is running:

`MIX_` 접두사의 환경 변수가 `.env` 파일에 정의되었다면 `process.env` 객체를 통해 이 변수에 액세스할 수 있습니다. 그러나 Mix 테스크가 실행되는 중 환경 변수의 값이 변경되면 Mix 테스크을 재시작해야 합니다.

    process.env.MIX_SENTRY_DSN_PUBLIC

<a name="notifications"></a>
## Notifications
## Notification-알림

When available, Mix will automatically display OS notifications for each bundle. This will give you instant feedback, as to whether the compilation was successful or not. However, there may be instances when you'd prefer to disable these notifications. One such example might be triggering Mix on your production server. Notifications may be deactivated, via the `disableNotifications` method.

가능한 경우에, Mix 는 자동으로 번들링을 마칠 때 OS 알림을 표시합니다. 이렇게 하면 컴파일이 성공했는지, 그렇지 않은지 즉각적인 피드백을 얻을 수 있습니다. 만약 이러한 알림을 비활성화 시키고자 할 수도 있는데, 프로덕션 서버에서 Mix를 사용할 때가 그렇습니다. `disableNotifications` 메소드를 사용하면 알림 기능을 비활성화 시킬 수 있습니다.

    mix.disableNotifications();
