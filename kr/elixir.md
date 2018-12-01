# Compiling Assets (Laravel Elixir)
# Assets 컴파일 하기 (라라벨 Elixir)

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Installation & Setup](#installation)
- [설치하기 & 설정하기](#installation)
- [Running Elixir](#running-elixir)
- [Elixir 실행하기](#running-elixir)
- [Working With Stylesheets](#working-with-stylesheets)
- [스타일시트 작업하기](#working-with-stylesheets)
    - [Less](#less)
    - [Less](#less)
    - [Sass](#sass)
    - [Sass](#sass)
    - [Stylus](#stylus)
    - [Stylus](#stylus)
    - [Plain CSS](#plain-css)
    - [일반적인 CSS](#plain-css)
    - [Source Maps](#css-source-maps)
    - [소스 맵](#css-source-maps)
- [Working With Scripts](#working-with-scripts)
- [스크립트 작업하기](#working-with-scripts)
    - [Webpack](#webpack)
    - [Webpack](#webpack)
    - [Rollup](#rollup)
    - [Rollup](#rollup)
    - [Scripts](#javascript)
    - [자바스크립트](#javascript)
- [Copying Files & Directories](#copying-files-and-directories)
- [파일 & 디렉토리 복사](#copying-files-and-directories)
- [Versioning / Cache Busting](#versioning-and-cache-busting)
- [버전 관리 / 캐시 갱신](#versioning-and-cache-busting)
- [BrowserSync](#browser-sync)
- [BrowserSync](#browser-sync)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel Elixir provides a clean, fluent API for defining basic [Gulp](http://gulpjs.com) tasks for your Laravel application. Elixir supports common CSS and JavaScript pre-processors like [Sass](http://sass-lang.com) and [Webpack](https://webpack.github.io/). Using method chaining, Elixir allows you to fluently define your asset pipeline. For example:

라라벨 Elixir(엘릭서)는 여러분의 라라벨 애플리케이션에 기본적인 [Gulp](http://gulpjs.com) 작업들을 정의하기 위해 깔끔하고 편리한 API를 제공합니다. Elixir는 [Sass](http://sass-lang.com) 와  [Webpack](https://webpack.github.io/)과 같은 공통적인 CSS와 JavaScript 전처리 작업을 지원합니다. Elixir는 메소드 체이닝을 사용하여 여러분이 asset pipeline 을 손쉽게 정의 할 수 있게 해줍니다. 다음 예제를 확인하세요:

```javascript
elixir(function(mix) {
    mix.sass('app.scss')
       .webpack('app.js');
});
```

If you've ever been confused and overwhelmed about getting started with Gulp and asset compilation, you will love Laravel Elixir. However, you are not required to use it while developing your application. You are free to use any asset pipeline tool you wish, or even none at all.

만약 여러분이 Gulp 과 asset 컴파일에 대해서 혼란스럽고 부담을 느끼고 있다면, 라라벨 Elixir 를 좋아하게 될 것입니다. 하지만 애플리케이션을 개발할 때 이것이 꼭 필요한 것은 아닙니다. 어떠한 asset pipeline 툴을 사용해도, 또 사용하지 않아도 괜찮습니다.

<a name="installation"></a>
## Installation & Setup
## 설치하기 & 설정하기

#### Installing Node
#### Node 설치하기

Before triggering Elixir, you must first ensure that Node.js and NPM are installed on your machine.

Elixir를 사용하기전 여러분의 작업환경에 Node.js와 NPM이 설치되어있는지 확인하십시오.

    node -v
    npm -v

By default, Laravel Homestead includes everything you need; however, if you aren't using Vagrant, then you can easily install the latest version of Node and NPM using simple graphical installers from [their download page](https://nodejs.org/en/download/).

기본적으로 라라벨의 홈스테드는 여러분이 필요로하는 모든것을 포함하고 있습니다. 하지만 Vagrant를 사용하지 않으신다면 [Node.js 다운로드 페이지](https://nodejs.org/en/download/)에서 간단한 화면 기반의 인스톨러를 사용하여 Node 와 NPM의 최신 버전을 손쉽게 설치할 수 있습니다.

#### Gulp
#### Gulp

Next, you'll need to pull in [Gulp](http://gulpjs.com) as a global NPM package:

다음으로 [Gulp](http://gulpjs.com)을 글로벌 NPM 패키지로 설치할 필요가 있습니다.

    npm install --global gulp-cli

#### Laravel Elixir
#### 라라벨 Elixir

The only remaining step is to install Laravel Elixir. Within a fresh installation of Laravel, you'll find a `package.json` file in the root of your directory structure. The default `package.json` file includes Elixir and the Webpack JavaScript module bundler. Think of this like your `composer.json` file, except it defines Node dependencies instead of PHP. You may install the dependencies it references by running:

이제 남은 과정은 라라벨 Elixir를 설치하는 것 뿐입니다. 라라벨을 새롭게 설치하고나면 디렉토리 구조의 루트 폴더에 위치한 `package.json` 파일을 볼 수 있습니다. 이 기본적인 `package.json` 파일은 Elixir 와 Webpack 자바스크립트 모듈 번들러를 포함하고 있습니다. 이 파일은 PHP 대신 Node 의존 패키지를 정의 한다는것을 빼고는 여러분의 `composer.json`과 동일합니다. 아래의 명령어로 의존 패키지들을 설치할 수 있습니다.

    npm install

If you are developing on a Windows system or you are running your VM on a Windows host system, you may need to run the `npm install` command with the `--no-bin-links` switch enabled:

여러분이 Windows 시스템에서 개발하고 있는 경우, 또는 Windows를 호스트로 가상 머신을 실행하는 경우에는 `--no-bin-links` 를 사용하여 `npm install` 명령어를 실행하십시오. 

    npm install --no-bin-links

<a name="running-elixir"></a>
## Running Elixir
## Elixir 실행하기

Elixir is built on top of [Gulp](http://gulpjs.com), so to run your Elixir tasks you only need to run the `gulp` command in your terminal. Adding the `--production` flag to the command will instruct Elixir to minify your CSS and JavaScript files:

Elixir 는 [Gulp](http://gulpjs.com)을 기반으로 만들어졌습니다. 따라서 Elixir 작업을 실행하기 위해서 필요한 것은 터미널에서 `gulp` 명령어를 실행하는 것 뿐입니다. `--production` 플래그를 추가하여 명령어를 실행하면 Elixir 에 CSS 와 JavaScript 파일을 압축하도록 지시할 것입니다:

    // Run all tasks...
    gulp

    // Run all tasks and minify all CSS and JavaScript...
    gulp --production

Upon running this command, you'll see a nicely formatted table that displays a summary of the events that just took place.

이 명령어를 실행하면, 해당 시점의 이벤트를 요약한 멋진 형태의 테이블이 표시되는 것을 볼 수 있습니다.

#### Watching Assets For Changes
#### Assets 변경 감시하기

The `gulp watch` command will continue running in your terminal and watch your assets for any changes. Gulp will automatically recompile your assets if you modify them while the `watch` command is running:

`gulp watch` 명령어는 터미널에서 계속 실행되면서 assets 파일이 변경되는 것을 감시할 것입니다. Gulp는 `watch` 명령어가 실행되는 동안 assets 이 수정되면 자동으로 이를 다시 컴파일 합니다:

    gulp watch

<a name="working-with-stylesheets"></a>
## Working With Stylesheets
## 스타일시트 작업하기

The `gulpfile.js` file in your project's root directory contains all of your Elixir tasks. Elixir tasks can be chained together to define exactly how your assets should be compiled.

프로젝트의 루트 디렉토리에 있는 `gulpfile.js` 파일에 모든 Elixir 작업이 기록되어 있습니다. Elixir 작업은 assets 들이 어떻게 컴파일하는지 체이닝 형태로 정의되어 있습니다.

<a name="less"></a>
### Less
### Less

The `less` method may be used to compile [Less](http://lesscss.org/) into CSS. The `less` method assumes that your Less files are stored in `resources/assets/less`. By default, the task will place the compiled CSS for this example in `public/css/app.css`:

`less` 메소드는 [Less](http://lesscss.org/)를 CSS로 컴파일하는데 사용됩니다. `less` 메소드는 Less 파일들이 `resources/assets/less` 디렉토리에 에 저장되어 있다고 가정하고 있습니다. 컴파일 된 CSS는 기본적으로 `public/css/app.css` 에 저장됩니다.

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

#### Custom Paths
#### 사용자 정의 경로

While it's recommended that you use Laravel's default asset directories, if you require a different base directory, you may begin any file path with `./`. This instructs Elixir to begin at the project root, rather than using the default base directory.


라라벨이 기본적으로 제공하는 asset 디렉토리를 사용하지만, 다른 베이스 디렉토리가 필요하다면, 파일 경로를 `./` 로 부터 지정할 수 있습니다. 이렇게 하면 Elixir 가 기본으로 설정된 베이스 디렉토리 대신 프로젝트 루트로 부터 시작하도록 지시할 수 있습니다. 

For example, to compile a file located at `app/assets/sass/app.scss` and output the results to `public/css/app.css`, you would make the following call to the `sass` method:

예를 들어 `app/assets/sass/app.scss`에 있는 파일을 컴파일 하여 그 결과를 `public/css/app.css`로 저장하고자 한다면, 다음처럼 `sass` 메소드를 호출하면 됩니다:

```javascript
elixir(function(mix) {
    mix.sass('./app/assets/sass/app.scss');
});
```

<a name="stylus"></a>
### Stylus
### Stylus

The `stylus` method may be used to compile [Stylus](http://stylus-lang.com/) into CSS. Assuming that your Stylus files are stored in `resources/assets/stylus`, you may call the method like so:

`stylus` 메소드는 [Stylus](http://stylus-lang.com/)를 CSS 로 컴파일 하는데 사용됩니다. Stylus 파일들은 `resources/assets/stylus`에 저장되어 있다고 가정하고, 다음처럼 메소드를 호출 할 수 있습니다: 

```javascript
elixir(function(mix) {
    mix.stylus('app.styl');
});
```

> {tip} This method's signature is identical to both `mix.less()` and `mix.sass()`.

> {tip} 이 메소드의 특징은 `mix.less()` 와 `mix.sass()` 모두와 동일합니다.

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

You may also instruct Elixir to write the resulting file to a custom directory or file by passing a second argument to the `styles` method:

또한 Elixir 의 결과물을 저장할 위치나 파일을 `styles` 메소드의 두번째 인자로 전달할 수도 있습니다:

```javascript
elixir(function(mix) {
    mix.styles([
        'normalize.css',
        'main.css'
    ], 'public/assets/css/site.css');
});
```

<a name="css-source-maps"></a>
### Source Maps
### 소스 맵

In Elixir, source maps are enabled by default and provide better debugging information to your browser's developer tools when using compiled assets. For each relevant file that is compiled, you will find a companion `*.css.map` or `*.js.map` file in the same directory.

기본적으로 Elixir 에서 소스맵이 활성화 되어 assets 을 컴파일 할 때 브라우저의 개발자 도구에서 확인할 수 있는 디버깅 정보를 제공합니다. 컴파일 되는 각각의 연관된 파일들에 대해서 동일한 디렉토리의 `*.css.map` 또는 `*.js.map` 파일을 찾을 수 있습니다. 

If you do not want source maps generated for your application, you may disable them using the `sourcemaps` configuration option:

만약 애플리케이션에서 소스맵이 생성되지 않길 원한다면, `sourcemaps` 설정옵션을 사용하여 비활성화 시킬 수 있습니다:

```javascript
elixir.config.sourcemaps = false;

elixir(function(mix) {
    mix.sass('app.scss');
});
```

<a name="working-with-scripts"></a>
## Working With Scripts
## 스크립트 작업하기

Elixir provides several features to help you work with your JavaScript files, such as compiling ECMAScript 2015, module bundling, minification, and simply concatenating plain JavaScript files.

Elixir는 ECMAScript 2015 컴파일, 모듈 번들링, 스크립트 압축, JavaScript 파일 결합 등의 JavaScript 작업을 도와주는 많은 기능들을 제공합니다. 

When writing ES2015 with modules, you have your choice between [Webpack](https://webpack.github.io) and [Rollup](http://rollupjs.org/). If these tools are foreign to you, don't worry, Elixir will handle all of the hard work behind the scenes. By default, the Laravel `gulpfile` uses `webpack` to compile Javascript, but you are free to use any module bundler you like.

모듈을 ES2015로 작성하는 경우 [Webpack](https://webpack.github.io) 또는 [Rollup](http://rollupjs.org/) 을 선택할 수 있습니다. 두 도구에 대해 잘 모르더라도 걱정하지 마십시오. Elixir가 뒤에서 모든 복잡한 일들을 처리할 것입니다. 기본적으로 라라벨의 `gulpfile` 는  자바스크립트를 컴파일 하는데 `webpack`을 사용하지만, 원하는 어떤 모듈 번들러라도 자유롭게 사용할 수 있습니다.

<a name="webpack"></a>
### Webpack
### Webpack

The `webpack` method may be used to compile and bundle [ECMAScript 2015](https://babeljs.io/docs/learn-es2015/) into plain JavaScript. This function accepts a file path relative to the `resources/assets/js` directory and generates a single bundled file in the `public/js` directory:

`webpack` 메소드는 [ECMAScript 2015](https://babeljs.io/docs/learn-es2015/)를 JavaScript로 컴파일하고 번들하는데 사용됩니다. 이 함수는 `resources/assets/js` 디렉토리로 부터 파일의 상대 경로를 인자로 전달 받아 `public/js` 디렉토리 안에 하나의 번들된 파일을 생성합니다.

```javascript
elixir(function(mix) {
    mix.webpack('app.js');
});
```

To choose a different output or base directory, simply specify your desired paths with a leading `.`. Then you may specify the paths relative to the root of your application. For example, to compile `app/assets/js/app.js` to `public/dist/app.js`:

다른 출력이나 베이스 디렉토리를 선택하려면, 원하는 경로를 `.` 로부터 지정하면 됩니다. 이렇게 하면 애플리케이션의 루트로 부터 연관된 경로를 지정할 수 있습니다. 예를 들어, `app/assets/js/app.js` 를 `public/dist/app.js`으로 컴파일 하려면:

```javascript
elixir(function(mix) {
    mix.webpack(
        './app/assets/js/app.js',
        './public/dist'
    );
});
```

If you'd like to leverage more of Webpack's functionality, Elixir will read any `webpack.config.js` file that is in your project root and [factor its configuration](https://webpack.github.io/docs/configuration.html) into the build process.

Webpack의 기능을 더 활용 하고자 한다면, 프로젝트의 루트에 `webpack.config.js` 파일을 통해서 Elixir가 이파일을 읽어 [이 설정을](https://webpack.github.io/docs/configuration.html) 통해서 프로세스를 빌드하도록 하십시오. 

<a name="rollup"></a>
### Rollup
### Rollup

Similar to Webpack, Rollup is a next-generation bundler for ES2015. This function accepts an array of files relative to the `resources/assets/js` directory, and generates a single file in the `public/js` directory:

Webpack 과 비슷하게 Rollup은 ES2015를 위한 차세대 번들러 입니다. 이 함수는 `resources/assets/js` 디렉토리로부터 상대 경로의 배열을 전달 받아 `public/js` 디렉토리 안에 하나의 파일을 생성합니다:

```javascript
elixir(function(mix) {
    mix.rollup('app.js');
});
```

Like the `webpack` method, you may customize the location of the input and output files given to the `rollup` method:

`webpack` 메소드와 같이, `rollup` 메소드에 입력 파일의 위치와 결과 파일을 직접 지정할 수 있습니다:

    elixir(function(mix) {
        mix.rollup(
            './resources/assets/js/app.js',
            './public/dist'
        );
    });

<a name="javascript"></a>
### Scripts

If you have multiple JavaScript files that you would like to combine into a single file, you may use the `scripts` method, which provides automatic source maps, concatenation, and minification.

여러개의 JavaScript 파일을 가지고 있고, 이것들을 하나의 파일로 합치고자 한다면, `scripts` 메소드를 사용하여, 자동으로 소스맵과 여러개의 파일을 연결하고 최소화 작업을 자동으로 처리할 수 있습니다.

The `scripts` method assumes all paths are relative to the `resources/assets/js` directory, and will place the resulting JavaScript in `public/js/all.js` by default:

`scripts` 메소드는 `resources/assets/js` 디렉토리에서 파일들을 찾는다고 가정합니다. 그리고 기본적으로 `public/js/all.js` 파일이 생성됩니다: 

```javascript
elixir(function(mix) {
    mix.scripts([
        'order.js',
        'forum.js'
    ]);
});
```

If you need to concatenate multiple sets of scripts into different files, you may make multiple calls to the `scripts` method. The second argument given to the method determines the resulting file name for each concatenation:

여러개의 파일들을 각기 다른 파일로 연결하고자 한다면, `scripts` 메소드를 여러번 호출하면 됩니다. 메소드에 주어진 두번째 인자는 각각의 결합된 파일의 이름이 됩니다: 

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

> {tip} If you intend to concatenate multiple pre-minified vendor libraries, such as jQuery, instead consider using `mix.combine()`. This will combine the files, while omitting the source map and minification steps. As a result, compile times will drastically improve.

> {tip} 만약 여러분이 jQuery 와 같은 이미 최소화된 다수의 벤더 라이브러리들을 연결하는 것을 고려한다면 대신 `mix.combine()` 을 사용하는것을 고려해보십시오. 이렇게 하면 파일들을 합치지만, 소스맵과 최소화 하는 단계를 생략합니다. 그 결과 컴파일 시간이 매우 많이 개선됩니다.

<a name="copying-files-and-directories"></a>
## Copying Files & Directories
## 파일 & 디렉토리 복사하기

The `copy` method may be used to copy files and directories to new locations. All operations are relative to the project's root directory:

`copy` 메소드는 파일들과 디렉토리를 새로운 위치에 복사하는데 사용됩니다. 모든 작업은 프로젝트의 루트 디렉토리로 부터 지정합니다:

```javascript
elixir(function(mix) {
    mix.copy('vendor/foo/bar.css', 'public/css/bar.css');
});
```

<a name="versioning-and-cache-busting"></a>
## Versioning / Cache Busting
## 버전 관리 / 캐시 갱신

Many developers suffix their compiled assets with a timestamp or unique token to force browsers to load the fresh assets instead of serving stale copies of the code. Elixir can handle this for you using the `version` method.

많은 개발자들이 기본 코드 대신 새로운 assets 을 강제로 로드하게끔 하기 위해서 컴파일된 assets 에 timestamp 또는 고유한 토큰을 접미사로 추가합니다. Elixir는 이러한 문제를 처리하기 위해 `version` 메소드를 사용합니다.

The `version` method accepts a file name relative to the `public` directory, and will append a unique hash to the filename, allowing for cache-busting. For example, the generated file name will look something like: `all-16d570a7.css`:

`version` 메소드는 `public` 디렉토리에 있는 파일을 인자로 전달 받고, 캐시를 갱신하기 위해서 파일 이름에 고유한 해시값을 추가할 것입니다. 다음 예제에서 생성된 파일은 `all-16d570a7.css`와 같은 이름이 될것입니다.

```javascript
elixir(function(mix) {
    mix.version('css/all.css');
});
```

After generating the versioned file, you may use Laravel's global `elixir` helper within your [views](/docs/{{version}}/views) to load the appropriately hashed asset. The `elixir` function will automatically determine the current name of the hashed file:

버전이 지정된 파일이 생성되면, [뷰-views](/docs/{{version}}/views)에서 라라벨의 `elixir` 헬퍼를 사용하여 해시값이 붙은 asset 을 로딩할 수 있습니다. `elixir` 함수는 자동으로 해시값이 붙어 있는 현재의 파일이름을 결정합니다:

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

BrowserSync automatically refreshes your web browser after you make changes to your assets. The `browserSync` method accepts a JavaScript object with a `proxy` attribute containing the local URL for your application. Then, once you run `gulp watch` you may access your web application using port 3000 (`http://project.dev:3000`) to enjoy browser syncing:

BrowserSync 는 여러분의 asset에 변경이 발생했을 때, 자동으로 웹 브라우저를 다시 로드 합니다. `browserSync` 메소드는 자바스크립트 객체와 애플리케이션을 위한 로컬 URL을 포함하는 `proxy` 를 전달 받습니다. 그런뒤에, `gulp watch` 를 실행하면, 여러분은 포트 3000을 사용하여 (`http://project.dev:3000`) 웹 애플리케이션에서 엑세스 할 수 있고 브라우저 싱크를 확인할 수 있습니다:

```javascript
elixir(function(mix) {
    mix.browserSync({
        proxy: 'project.dev'
    });
});
```
