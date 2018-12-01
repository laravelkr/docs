# Laravel Elixir
# 라라벨 Elixir

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation & Setup](#installation)
- [설치 & 준비](#installation)
- [Running Elixir](#running-elixir)
- [Elixir 구동하기](#running-elixir)
- [Working With Stylesheets](#working-with-stylesheets)
- [스타일시트 작업](#working-with-stylesheets)
    - [Less](#less)
    - [Less](#less)
    - [Sass](#sass)
    - [Sass](#sass)
    - [Plain CSS](#plain-css)
    - [일반적인 CSS](#plain-css)
    - [Source Maps](#css-source-maps)
    - [소스 맵](#css-source-maps)
- [Working With Scripts](#working-with-scripts)
- [스크립트 작업](#working-with-scripts)
    - [CoffeeScript](#coffeescript)
    - [CoffeeScript](#coffeescript)
    - [Browserify](#browserify)
    - [Browserify](#browserify)
    - [Babel](#babel)
    - [Babel](#babel)
    - [Scripts](#javascript)
    - [Scripts](#javascript)
- [Copying Files & Directories](#copying-files-and-directories)
- [파일 & 디렉토리 복사](#copying-files-and-directories)
- [Versioning / Cache Busting](#versioning-and-cache-busting)
- [버전 관리 / 캐시 갱신](#versioning-and-cache-busting)
- [BrowserSync](#browser-sync)
- [BrowserSync](#browser-sync)
- [Calling Existing Gulp Tasks](#calling-existing-gulp-tasks)
- [기존의 Gulp 작업 호출하기](#calling-existing-gulp-tasks)
- [Writing Elixir Extensions](#writing-elixir-extensions)
- [Elixir 확장 기능 작성하기](#writing-elixir-extensions)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel Elixir provides a clean, fluent API for defining basic [Gulp](http://gulpjs.com) tasks for your Laravel application. Elixir supports several common CSS and JavaScript pre-processors, and even testing tools. Using method chaining, Elixir allows you to fluently define your asset pipeline. For example:

라라벨 Elixir(엘릭서)는 여러분의 라라벨 애플리케이션에 기본적인 [Gulp](http://gulpjs.com) 작업들을 정의하기 위해 깔끔하고 편리한 API를 제공합니다. Elixir는 몇가지 공통적인 CSS와 JavaScript 전처리 작업, 또한 테스팅 툴까지 제공합니다. Elixir는 메소드 체이닝을 사용하여 여러분이 asset pipeline 을 손쉽게 정의 할 수 있게 해줍니다. 다음 예제를 확인하세요:

```javascript
elixir(function(mix) {
    mix.sass('app.scss')
       .coffee('app.coffee');
});
```

If you've ever been confused about how to get started with Gulp and asset compilation, you will love Laravel Elixir. However, you are not required to use it while developing your application. You are free to use any asset pipeline tool you wish, or even none at all.

만약 여러분이 Gulp 과 asset compilation를 어떻게 시작해야 되는지 어려워한 적이 있다면 라라벨 Elixir 를 좋아하게 될 것입니다. 하지만 애플리케이션을 개발할 때 이것이 꼭 필요한 것은 아닙니다. 어떠한 asset pipeline 툴을 사용해도, 또 사용하지 않아도 괜찮습니다.

<a name="installation"></a>
## Installation & Setup
## 설치 & 준비

### Installing Node
### Node 설치

Before triggering Elixir, you must first ensure that Node.js is installed on your machine.

Elixir를 사용하기전 여러분의 작업환경에 Node.js가 설치되어있는지 확인하십시오.

    node -v

By default, Laravel Homestead includes everything you need; however, if you aren't using Vagrant, then you can easily install Node by visiting [their download page](http://nodejs.org/en/download/).

기본적으로 라라벨의 홈스테드는 여러분이 필요로하는 모든것을 포함하고 있습니다. 그러나 Vagrant를 사용하지 않으신다면 [Node.js 다운로드 페이지](http://nodejs.org/en/download/)에서 관련 문서를 통해서 쉽게 설치할 수 있습니다.

### Gulp
### Gulp

Next, you'll want to pull in [Gulp](http://gulpjs.com) as a global NPM package:

다음으로 [Gulp](http://gulpjs.com)을 글로벌 NPM 패키지로 설치합니다.

    npm install --global gulp-cli

If you use a version control system, you may wish to run the `npm shrinkwrap` to lock your NPM requirements:

버전 관리 시스템을 사용하는 경우 NPM 에 추가하기 위해서 `npm shrinkwrap` 라고 실행할 수도 있습니다:

     npm shrinkwrap

Once you have run this command, feel free to commit the [npm-shrinkwrap.json](https://docs.npmjs.com/cli/shrinkwrap) into source control.

이 명령어를 실행한 뒤에, [npm-shrinkwrap.json](https://docs.npmjs.com/cli/shrinkwrap) 파일을 소스 컨트롤에 자유롭게 커밋할 수 있습니다.

### Laravel Elixir
### Laravel Elixir

The only remaining step is to install Elixir! Within a fresh installation of Laravel, you'll find a `package.json` file in the root. Think of this like your `composer.json` file, except it defines Node dependencies instead of PHP. You may install the dependencies it references by running:

마지막으로 남은 과정은 Elixir를 설치하는 것 뿐입니다. 라라벨을 새로 설치하면 루트 폴더에 위치한 `package.json` 파일을 볼 수 있습니다. 이 파일은 PHP 대신 Node 의존 패키지를 정의 한다는것을 빼고는 여러분의 `composer.json`과 동일합니다. 아래의 명령어로 의존 패키지들을 설치할 수 있습니다.

    npm install

If you are developing on a Windows system or you are running your VM on a Windows host system, you may need to run the `npm install` command with the `--no-bin-links` switch enabled:

여러분이 Windows 시스템에서 개발하고 있는 경우, 또는 Windows를 호스트로 가상 머신을 실행하는 경우에는 `--no-bin-links` 를 사용하여 `npm install` 명령어를 실행하십시오. 

    npm install --no-bin-links

<a name="running-elixir"></a>
## Running Elixir
## Elixir 구동하기

Elixir is built on top of [Gulp](http://gulpjs.com), so to run your Elixir tasks you only need to run the `gulp` command in your terminal. Adding the `--production` flag to the command will instruct Elixir to minify your CSS and JavaScript files:

Elixir 는 [Gulp](http://gulpjs.com)을 기반으로 만들어졌습니다. 따라서 Elixir 작업을 실행하기 위해서 필요한 것은 터미널에서 `gulp` 명령어를 실행하는 것 뿐입니다. `--production` 플래그를 추가하여 명령어를 실행하면 Elixir 에 CSS 와 JavaScript 파일을 압축하도록 지시할 것입니다. 

    // Run all tasks...
    gulp

    // Run all tasks and minify all CSS and JavaScript...
    gulp --production

#### Watching Assets For Changes
#### Assets 변경 감시하기

Since it is inconvenient to run the `gulp` command on your terminal after every change to your assets, you may use the `gulp watch` command. This command will continue running in your terminal and watch your assets for any changes. When changes occur, new files will automatically be compiled:

assets 파일들을 변경할 때마다 터미널에서 `gulp` 명령을 실행하는 것은 번거로운 일이므로, `gulp watch` 명령어를 사용할 수 있습니다. 이 명령어는 터미널에서 동작하여 assets 파일들의 변경을 감시합니다. 변경이 있을 때 마다, 파일들은 자동으로 컴파일됩니다.

    gulp watch

<a name="working-with-stylesheets"></a>
## Working With Stylesheets
## 스타일시트 작업

The `gulpfile.js` file in your project's root directory contains all of your Elixir tasks. Elixir tasks can be chained together to define exactly how your assets should be compiled.

프로젝트의 루트 디렉토리에 있는 `gulpfile.js` 파일에 모든 Elixir 작업이 기록되어 있습니다. Elixir 작업은 assets 들이 어떻게 컴파일하는지 체이닝 형태로 정의되어 있습니다.

<a name="less"></a>
### Less
### Less

To compile [Less](http://lesscss.org/) into CSS, you may use the `less` method. The `less` method assumes that your Less files are stored in `resources/assets/less`. By default, the task will place the compiled CSS for this example in `public/css/app.css`:

[Less](http://lesscss.org/)를 CSS로 컴파일하기 위해서 `less` 메소드를 사용하면 됩니다. `less` 메소드는 Less 파일들이 `resources/assets/less` 디렉토리에 에 저장되어 있다고 가정하고 있습니다. 컴파일 된 CSS는 기본적으로 `public/css/app.css` 에 저장됩니다.

```javascript
elixir(function(mix) {
    mix.less('app.less');
});
```

You may also combine multiple Less files into a single CSS file. Again, the resulting CSS will be placed in `public/css/app.css`: 

여러개의 Less 파일들도 결합하여 하나의 CSS 파일로 컴파일할 수 있습니다. 동일하게, 컴파일된 CSS 파일은 `public/css/app.css` 파일로 저장됩니다:

```javascript
elixir(function(mix) {
    mix.less([
        'app.less',
        'controllers.less'
    ]);
});
```

If you wish to customize the output location of the compiled CSS, you may pass a second argument to the `less` method:

컴파일된 CSS의 위치를 변경하고자 한다면 `less` 메소드의 두번째 인자를 전달하면 됩니다:

```javascript
elixir(function(mix) {
    mix.less('app.less', 'public/stylesheets');
});

// Specifying a specific output filename...
elixir(function(mix) {
    mix.less('app.less', 'public/stylesheets/style.css');
});
```

<a name="sass"></a>
### Sass
### Sass

The `sass` method allows you to compile [Sass](http://sass-lang.com/) into CSS. Assuming your Sass files are stored at `resources/assets/sass`, you may use the method like so:

`sass` 메소드는 [Sass](http://sass-lang.com/)파일을 CSS 파일로 컴파일해줍니다. Sass 파일들은 `resources/assets/sass` 디렉토리에 저장되어 있다고 가정합니다. 메소드는 다음과 같이 사용합니다: 

```javascript
elixir(function(mix) {
    mix.sass('app.scss');
});
```

Again, like the `less` method, you may compile multiple Sass files into a single CSS file, and even customize the output directory of the resulting CSS:

`less` 메소드와 같이 여러개의 Sass 파일들을 하나의 CSS 파일로 합칠 수 있으며, CSS 파일이 저장될 결과 디렉토리를 지정할 수도 있습니다:

```javascript
elixir(function(mix) {
    mix.sass([
        'app.scss',
        'controllers.scss'
    ], 'public/assets/css');
});
```

<a name="plain-css"></a>
### Plain CSS
### 일반적인 CSS

If you would just like to combine some plain CSS stylesheets into a single file, you may use the `styles` method. Paths passed to this method are relative to the `resources/assets/css` directory and the resulting CSS will be placed in `public/css/all.css`:

일반적인 CSS 스타일시트 파일들을 하나의 파일로 합치려면 `sytles` 메소드를 사용하면 됩니다. 이 메소드에 전달되는 경로 `resources/assets/css` 디렉토리로 부터 CSS를 찾고 그 결과 CSS 파일을 `public/css/all.css` 파일로 저장합니다. 

```javascript
elixir(function(mix) {
    mix.styles([
        'normalize.css',
        'main.css'
    ]);
});
```

Of course, you may also output the resulting file to a custom location by passing a second argument to the `styles` method:

물론 결과 파일이 저장될 위치를 `styles` 메소드의 두번째 인자로 전달할 수 있습니다:


```javascript
elixir(function(mix) {
    mix.styles([
        'normalize.css',
        'main.css'
    ], 'public/assets/css');
});
```

<a name="css-source-maps"></a>
### Source Maps
### 소스 맵

Source maps are enabled out of the box. So, for each file that is compiled you will find a companion `*.css.map` file in the same directory. This mapping allows you to trace your compiled stylesheet selectors back to your original Sass or Less while debugging in your browser.

별다른 설정없이도 소스맵이 활성화 되어 있습니다. 따라서 각 파일들을 컴파일 할 때 마다 `*.css.map` 파일이 동일한 경로에 생성됩니다. 이 매핑은 여러분이 컴파일한 스타일시트를 브라우저에서 디버깅을 할 때 원래의 Sass 또는 Less 를 찾을 수 있게 해줍니다. 

If you do not want source maps generated for your CSS, you may disable them using a simple configuration option:

만약 CSS를 위한 소스맵이 생성되지 않길 원한다면, 간단한 설정옵션으로 비활성화 시킬 수 있습니다:

```javascript
elixir.config.sourcemaps = false;

elixir(function(mix) {
    mix.sass('app.scss');
});
```

<a name="working-with-scripts"></a>
## Working With Scripts
## 스크립트 작업

Elixir also provides several functions to help you work with your JavaScript files, such as compiling ECMAScript 6, compiling CoffeeScript, Browserify, minification, and simply concatenating plain JavaScript files.

또한 Elixir는 ECMAScript 6 컴파일, CoffeeScript 컴파일, Browserify, 스크립트 압축, JavaScript 파일 결합 등의 JavaScript 작업을 도와주는 많은 기능들을 제공합니다. 

<a name="coffeescript"></a>
### CoffeeScript
### CoffeeScript

The `coffee` method may be used to compile [CoffeeScript](http://coffeescript.org/) into plain JavaScript. The `coffee` function accepts a string or array of CoffeeScript files relative to the `resources/assets/coffee` directory and generates a single `app.js` file in the `public/js` directory:

`coffee` 메소드는 [CoffeeScript](http://coffeescript.org/)를 일반적인 JavaScript 파일로 컴파일하는데 사용됩니다. `coffee` 함수는 `resources/assets/coffee` 디렉토리에 들어 있는 CoffeeScript 파일들의 이름으로 된 배열을 인자로 전달 받아 `public/js` 디렉토리에 하나의 `app.js` 파일을 생성합니다. 

```javascript
elixir(function(mix) {
    mix.coffee(['app.coffee', 'controllers.coffee']);
});
```

<a name="browserify"></a>
### Browserify
### Browserify

Elixir also ships with a `browserify` method, which gives you all the benefits of requiring modules in the browser and using ECMAScript 6 and JSX.

Elixir 는 또한 ECMAScript 6와 JSX 를 사용하는 모듈들의 require 를 구성하는 장점을 활용할 수 있도록 `browserify` 메소드도 제공합니다. 

This task assumes that your scripts are stored in `resources/assets/js` and will place the resulting file in `public/js/main.js`. You may pass a custom output location as an optional second argument:

이 메소드는 스크립트 파일들이 `resources/assets/js` 에 위치한다고 가정하고 있으며, 결과 파일은 `public/js/main.js` 파일이 됩니다. 두번째 인자를 넘겨주어 결과 파일의 위치를 지정할 수도 있습니다:

```javascript
elixir(function(mix) {
    mix.browserify('main.js');
});

// Specifying a specific output filename...
elixir(function(mix) {
    mix.browserify('main.js', 'public/javascripts/main.js');
});
```

While Browserify ships with the Partialify and Babelify transformers, you're free to install and add more if you wish:

Browserify 는 Partialify 와 Babelify 변환을 제공하지만, 원한다면 추가적으로 설치할 수도 있습니다:

    npm install aliasify --save-dev

```javascript
elixir.config.js.browserify.transformers.push({
    name: 'aliasify',
    options: {}
});

elixir(function(mix) {
    mix.browserify('main.js');
});
```

<a name="babel"></a>
### Babel
### Babel

The `babel` method may be used to compile [ECMAScript 6 and 7](https://babeljs.io/docs/learn-es2015/) and [JSX](https://facebook.github.io/react/docs/jsx-in-depth.html) into plain JavaScript. This function accepts an array of files relative to the `resources/assets/js` directory, and generates a single `all.js` file in the `public/js` directory:

`babel` 메소드는 [ECMAScript 6 과 7](https://babeljs.io/docs/learn-es2015/) 그리고 [JSX](https://facebook.github.io/react/docs/jsx-in-depth.html)를 일반적인 JavaScript로 컴파일하는데 사용됩니다. 이 함수는 `resources/assets/js`디렉토리에 들어 있는 파일들의 배열을 인자로 받아서 `public/js` 디렉토리에 하나의 `all.js` 파일을 생성합니다. 

```javascript
elixir(function(mix) {
    mix.babel([
        'order.js',
        'product.js',
        'react-component.jsx'
    ]);
});
```

To choose a different output location, simply specify your desired path as the second argument. The signature and functionality of this method are identical to `mix.scripts()`, excluding the Babel compilation.

다른 출력 위치를 선택하려면, 원하는 경로를 두번째 인자로 지정하면 됩니다. 이 메소드의 사용법과 기능은 Babel을 컴파일 한다는 점을 제외하고, `mix.scripts()` 와 동일합니다.

<a name="javascript"></a>
### Scripts
### Scripts

If you have multiple JavaScript files that you would like to combine into a single file, you may use the `scripts` method.

여러개의 JavaScript 파일을 가지고 있고, 이것들을 하나의 파일로 합치고자 한다면, `scripts` 메소드를 사용하면 됩니다. 

The `scripts` method assumes all paths are relative to the `resources/assets/js` directory, and will place the resulting JavaScript in `public/js/all.js` by default:

`scripts` 메소드는 `resources/assets/js` 디렉토리에서 파일들을 찾는다고 가정합니다. 그리고 기본적으로 `public/js/all.js` 파일이 생성됩니다. 

```javascript
elixir(function(mix) {
    mix.scripts([
        'jquery.js',
        'app.js'
    ]);
});
```

If you need to combine multiple sets of scripts into different files, you may make multiple calls to the `scripts` method. The second argument given to the method determines the resulting file name for each concatenation:

여러개의 파일들을 각기 다른 파일로 합치고자 한다면, `scripts` 메소드를 여러번 호출하면 됩니다. 메소드에 주어진 두번째 인자는 각각의 결합된 파일의 이름이 됩니다:

```javascript
elixir(function(mix) {
    mix.scripts(['app.js', 'controllers.js'], 'public/js/app.js')
       .scripts(['forum.js', 'threads.js'], 'public/js/forum.js');
});
```

If you need to combine all of the scripts in a given directory, you may use the `scriptsIn` method. The resulting JavaScript will be placed in `public/js/all.js`:

지정된 디렉토리에 모든 스크립트들을 합치고자 한다면 `scriptIn` 메소드를 사용하면 됩니다. 합쳐진 JavaScript 는 `public/js/all.js` 가 될것입니다. 

```javascript
elixir(function(mix) {
    mix.scriptsIn('public/js/some/directory');
});
```

<a name="copying-files-and-directories"></a>
## Copying Files & Directories
## 파일 & 디렉토리 복사

The `copy` method may be used to copy files and directories to new locations. All operations are relative to the project's root directory:

`copy` 메소드는 파일들과 디렉토리를 새로운 위치에 복사하는데 사용됩니다. 모든 작업은 프로젝트의 루트 디렉토리로 부터 지정합니다.

```javascript
elixir(function(mix) {
    mix.copy('vendor/foo/bar.css', 'public/css/bar.css');
});

elixir(function(mix) {
    mix.copy('vendor/package/views', 'resources/views');
});
```

<a name="versioning-and-cache-busting"></a>
## Versioning / Cache Busting
## 버전지정 / 캐시 갱신

Many developers suffix their compiled assets with a timestamp or unique token to force browsers to load the fresh assets instead of serving stale copies of the code. Elixir can handle this for you using the `version` method.

많은 개발자들이 기본 코드 대신 새로운 assets 을 강제로 로드하게끔 하기 위해서 컴파일된 assets 에 timestamp 또는 고유한 토큰을 접미사로 추가합니다. Elixir는 이러한 문제를 처리하기 위해 `version` 메소드를 사용합니다.

The `version` method accepts a file name relative to the `public` directory, and will append a unique hash to the filename, allowing for cache-busting. For example, the generated file name will look something like: `all-16d570a7.css`:

`version` 메소드는 `public` 디렉토리에 있는 파일을 인자로 전달 받고, 캐시를 갱신하기 위해서 파일 이름에 고유한 해시값을 추가할 것입니다. 다음 예제에서 생성된 파일은 `all-16d570a7.css`와 같은 이름이 될것입니다. 

```javascript
elixir(function(mix) {
    mix.version('css/all.css');
});
```

After generating the versioned file, you may use Laravel's global `elixir` PHP helper function within your [views](/docs/{{version}}/views) to load the appropriately hashed asset. The `elixir` function will automatically determine the name of the hashed file:

버전이 지정된 파일이 생성되면, [뷰-views](/docs/{{version}}/views)에서 라라벨의 `elixir` 헬퍼 함수를 사용하여 해시값이 붙은 asset 을 로딩할 수 있습니다. `elixir`함수는 자동으로 해시값이 붙어 있는 파일이름을 결정합니다.

    <link rel="stylesheet" href="{{ elixir('css/all.css') }}">

#### Versioning Multiple Files
#### 다수의 파일들을 버전 처리하기

You may pass an array to the `version` method to version multiple files:

`version` 메소드에 여러개의 파일을 전달 할 수도 있습니다:

```javascript
elixir(function(mix) {
    mix.version(['css/all.css', 'js/app.js']);
});
```

Once the files have been versioned, you may use the `elixir` helper function to generate links to the proper hashed files. Remember, you only need to pass the name of the un-hashed file to the `elixir` helper function. The helper will use the un-hashed name to determine the current hashed version of the file:

파일에 버전이 지정되면 `elixir` 헬퍼 함수는 해시된 실제 파일의 링크를 생성합니다. 주의할 점은 `elixir` 헬퍼 함수에 해시를 붙이지 않은 파일 이름을 전달해야 한다는 것입니다. 헬퍼 함수는 이 해시를 붙이지 않은 파일 이름을 통해 현재의 해시가 붙은 파일 이름을 찾게 됩니다:

    <link rel="stylesheet" href="{{ elixir('css/all.css') }}">

    <script src="{{ elixir('js/app.js') }}"></script>

<a name="browser-sync"></a>
## BrowserSync
## BrowserSync

BrowserSync automatically refreshes your web browser after you make changes to your front-end resources. You can use the `browserSync` method to instruct Elixir to start a BrowserSync server when you run the `gulp watch` command:

BrowserSync 는 여러분의 프론트엔드 리소스에 변경이 발생했을 때, 자동으로 웹 브라우저를 다시 로드 합니다. `gulp watch` 명령어를 실행할 때 BrowserSync를 시작하도록 Elixir 에 지시하기위해서 `BrowserSync` 메소드를 사용할 수 있습니다. 

```javascript
elixir(function(mix) {
    mix.browserSync();
});
```

Once you run `gulp watch`, access your web application using port 3000 to enable browser syncing: `http://homestead.app:3000`. If you're using a domain other than `homestead.app` for local development, you may pass an array of [options](http://www.browsersync.io/docs/options/) as the first argument to the `browserSync` method:

`gulp watch` 를 실행하고, 브라우저 동기화를 활성화 하기 위해서 웹 애플리케이션에 포트 3000으로 접속하십시오: `http://homestead.app:3000`. 로컬 개발 환경에서 `homestead.app` 이외에 다른 도메인을 사용한다면 `browserSync` 메소드의 첫번째 인자로 [옵션](http://www.browsersync.io/docs/options/)을 지정하여 전달 하십시오

```javascript
elixir(function(mix) {
    mix.browserSync({
        proxy: 'project.app'
    });
});
```

<a name="calling-existing-gulp-tasks"></a>
## Calling Existing Gulp Tasks
## 기존의 Gulp 작업 호출하기

If you need to call an existing Gulp task from Elixir, you may use the `task` method. As an example, imagine that you have a Gulp task that simply speaks a bit of text when called:

Elixir 에서 이미 존재하는 Gulp 작업을 호출할 필요가 있다면 `task` 메소드를 사용하면 됩니다. 예를 들어 간한단 메세지를 출력하는 Gulp 작업이 있다고 생각해 봅시다:

```javascript
gulp.task('speak', function() {
    var message = 'Tea...Earl Grey...Hot';

    gulp.src('').pipe(shell('say ' + message));
});
```

If you wish to call this task from Elixir, use the `mix.task` method and pass the name of the task as the only argument to the method:

이 작업을 Elixir 에서 호출하려면 `mix.task` 메소드를 사용하여 첫번째 인자로 작업의 이름을 메소드에 전달하면 됩니다:

```javascript
elixir(function(mix) {
    mix.task('speak');
});
```

#### Custom Watchers
#### 사용자 지정 파일 감시

If you need to register a watcher to run your custom task each time some files are modified, pass a regular expression as the second argument to the `task` method:

사용자 지정 작업을 실행하기 위해서 매번 어떤 파일들이 수정되었는지 감시하려는 대상 파일을 등록하려면 `task` 메소드의 두번째 인자로 정규표현식을 지정하면 됩니다:

```javascript
elixir(function(mix) {
    mix.task('speak', 'app/**/*.php');
});
```

<a name="writing-elixir-extensions"></a>
## Writing Elixir Extensions
## Elixir 확장 기능 작성하기

If you need more flexibility than Elixir's `task` method can provide, you may create custom Elixir extensions. Elixir extensions allow you to pass arguments to your custom tasks. For example, you could write an extension like so:

Elixir 의 `task` 메소드가 제공하는 것 보다 더 다양한 기능이 필요하다면, Elixir 확장 기능을 작성할 수도 있습니다. Elixir 확장기능에 고유한 작업을 위한 인자를 전달 합니다. 예를 들어 다음과 같이 확장 기능을 작성할 수도 있습니다. 

```javascript
// File: elixir-extensions.js

var gulp = require('gulp');
var shell = require('gulp-shell');
var Elixir = require('laravel-elixir');

var Task = Elixir.Task;

Elixir.extend('speak', function(message) {

    new Task('speak', function() {
        return gulp.src('').pipe(shell('say ' + message));
    });

});

// mix.speak('Hello World');
```

That's it! Notice that your Gulp-specific logic should be placed within the function passed as the second argument to the `Task` constructor. You may either place this at the top of your Gulpfile, or instead extract it to a custom tasks file. For example, if you place your extensions in `elixir-extensions.js`, you may require the file from your main `Gulpfile` like so:

이렇게 작성합니다! 기억할 것은 여러분의 Gulp 로직은 `Task` 생성자의 두번째 인자로 전달된 함수 안에 작성되어야 한다는 것입니다. 이 내용은 Gulp 파일의 가장 최상단에 작성하거나 또는 외부 파일에 구성할 수도 있습니다. 예를 들어 확장 기능에 대한 정의를 `elixir-extensions.js` 로 지정할 수 있으며 다음과 같이 `Gulpfile` 에서 불러 들일수도 있습니다. 

```javascript
// File: Gulpfile.js

var elixir = require('laravel-elixir');

require('./elixir-extensions')

elixir(function(mix) {
    mix.speak('Tea, Earl Grey, Hot');
});
```

#### Custom Watchers
#### 사용자 지정 파일 감시

If you would like your custom task to be re-triggered while running `gulp watch`, you may register a watcher:

`gulp watch` 가 실행되는 동안 사용자 지정 작업을 다시 시작하려면 파일 감시를 등록하면 됩니다:

```javascript
new Task('speak', function() {
    return gulp.src('').pipe(shell('say ' + message));
})
.watch('./app/**');
```
