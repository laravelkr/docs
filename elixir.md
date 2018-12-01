# 라라벨 Elixir

- [시작하기](#introduction)
- [설치 & 준비](#installation)
- [Elixir 구동하기](#running-elixir)
- [스타일시트 작업](#working-with-stylesheets)
    - [Less](#less)
    - [Sass](#sass)
    - [일반적인 CSS](#plain-css)
    - [소스 맵](#css-source-maps)
- [스크립트 작업](#working-with-scripts)
    - [CoffeeScript](#coffeescript)
    - [Browserify](#browserify)
    - [Babel](#babel)
    - [Scripts](#javascript)
- [파일 & 디렉토리 복사](#copying-files-and-directories)
- [버전 관리 / 캐시 갱신](#versioning-and-cache-busting)
- [BrowserSync](#browser-sync)
- [기존의 Gulp 작업 호출하기](#calling-existing-gulp-tasks)
- [Elixir 확장 기능 작성하기](#writing-elixir-extensions)

<a name="introduction"></a>
## 시작하기

라라벨 Elixir(엘릭서)는 여러분의 라라벨 애플리케이션에 기본적인 [Gulp](http://gulpjs.com) 작업들을 정의하기 위해 깔끔하고 편리한 API를 제공합니다. Elixir는 몇가지 공통적인 CSS와 JavaScript 전처리 작업, 또한 테스팅 툴까지 제공합니다. Elixir는 메소드 체이닝을 사용하여 여러분이 asset pipeline 을 손쉽게 정의 할 수 있게 해줍니다. 다음 예제를 확인하세요:

```javascript
elixir(function(mix) {
    mix.sass('app.scss')
       .coffee('app.coffee');
});
```

만약 여러분이 Gulp 과 asset compilation를 어떻게 시작해야 되는지 어려워한 적이 있다면 라라벨 Elixir 를 좋아하게 될 것입니다. 하지만 애플리케이션을 개발할 때 이것이 꼭 필요한 것은 아닙니다. 어떠한 asset pipeline 툴을 사용해도, 또 사용하지 않아도 괜찮습니다.

<a name="installation"></a>
## 설치 & 준비

### Node 설치

Elixir를 사용하기전 여러분의 작업환경에 Node.js가 설치되어있는지 확인하십시오.

    node -v

기본적으로 라라벨의 홈스테드는 여러분이 필요로하는 모든것을 포함하고 있습니다. 그러나 Vagrant를 사용하지 않으신다면 [Node.js 다운로드 페이지](http://nodejs.org/en/download/)에서 관련 문서를 통해서 쉽게 설치할 수 있습니다.

### Gulp

다음으로 [Gulp](http://gulpjs.com)을 글로벌 NPM 패키지로 설치합니다.

    npm install --global gulp-cli

버전 관리 시스템을 사용하는 경우 NPM 에 추가하기 위해서 `npm shrinkwrap` 라고 실행할 수도 있습니다:

     npm shrinkwrap

이 명령어를 실행한 뒤에, [npm-shrinkwrap.json](https://docs.npmjs.com/cli/shrinkwrap) 파일을 소스 컨트롤에 자유롭게 커밋할 수 있습니다.

### 라라벨 Elixir

마지막으로 남은 과정은 Elixir를 설치하는 것 뿐입니다. 라라벨을 새로 설치하면 루트 폴더에 위치한 `package.json` 파일을 볼 수 있습니다. 이 파일은 PHP 대신 Node 의존 패키지를 정의 한다는것을 빼고는 여러분의 `composer.json`과 동일합니다. 아래의 명령어로 의존 패키지들을 설치할 수 있습니다.

    npm install

여러분이 Windows 시스템에서 개발하고 있는 경우, 또는 Windows를 호스트로 가상 머신을 실행하는 경우에는 `--no-bin-links` 를 사용하여 `npm install` 명령어를 실행하십시오. 

    npm install --no-bin-links

<a name="running-elixir"></a>
## Elixir 구동하기

Elixir 는 [Gulp](http://gulpjs.com)을 기반으로 만들어졌습니다. 따라서 Elixir 작업을 실행하기 위해서 필요한 것은 터미널에서 `gulp` 명령어를 실행하는 것 뿐입니다. `--production` 플래그를 추가하여 명령어를 실행하면 Elixir 에 CSS 와 JavaScript 파일을 압축하도록 지시할 것입니다. 

    // Run all tasks...
    gulp

    // Run all tasks and minify all CSS and JavaScript...
    gulp --production

#### Assets 변경 감시하기

assets 파일들을 변경할 때마다 터미널에서 `gulp` 명령을 실행하는 것은 번거로운 일이므로, `gulp watch` 명령어를 사용할 수 있습니다. 이 명령어는 터미널에서 동작하여 assets 파일들의 변경을 감시합니다. 변경이 있을 때 마다, 파일들은 자동으로 컴파일됩니다.

    gulp watch

<a name="working-with-stylesheets"></a>
## 스타일시트 작업

프로젝트의 루트 디렉토리에 있는 `gulpfile.js` 파일에 모든 Elixir 작업이 기록되어 있습니다. Elixir 작업은 assets 들이 어떻게 컴파일하는지 체이닝 형태로 정의되어 있습니다.

<a name="less"></a>
### Less

[Less](http://lesscss.org/)를 CSS로 컴파일하기 위해서 `less` 메소드를 사용하면 됩니다. `less` 메소드는 Less 파일들이 `resources/assets/less` 디렉토리에 에 저장되어 있다고 가정하고 있습니다. 컴파일 된 CSS는 기본적으로 `public/css/app.css` 에 저장됩니다.

```javascript
elixir(function(mix) {
    mix.less('app.less');
});
```

여러개의 Less 파일들도 결합하여 하나의 CSS 파일로 컴파일할 수 있습니다. 동일하게, 컴파일된 CSS 파일은 `public/css/app.css` 파일로 저장됩니다:

```javascript
elixir(function(mix) {
    mix.less([
        'app.less',
        'controllers.less'
    ]);
});
```

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

`sass` 메소드는 [Sass](http://sass-lang.com/)파일을 CSS 파일로 컴파일해줍니다. Sass 파일들은 `resources/assets/sass` 디렉토리에 저장되어 있다고 가정합니다. 메소드는 다음과 같이 사용합니다: 

```javascript
elixir(function(mix) {
    mix.sass('app.scss');
});
```

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
### 일반적인 CSS

일반적인 CSS 스타일시트 파일들을 하나의 파일로 합치려면 `sytles` 메소드를 사용하면 됩니다. 이 메소드에 전달되는 경로 `resources/assets/css` 디렉토리로 부터 CSS를 찾고 그 결과 CSS 파일을 `public/css/all.css` 파일로 저장합니다. 

```javascript
elixir(function(mix) {
    mix.styles([
        'normalize.css',
        'main.css'
    ]);
});
```

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
### 소스 맵

별다른 설정없이도 소스맵이 활성화 되어 있습니다. 따라서 각 파일들을 컴파일 할 때 마다 `*.css.map` 파일이 동일한 경로에 생성됩니다. 이 매핑은 여러분이 컴파일한 스타일시트를 브라우저에서 디버깅을 할 때 원래의 Sass 또는 Less 를 찾을 수 있게 해줍니다. 

만약 CSS를 위한 소스맵이 생성되지 않길 원한다면, 간단한 설정옵션으로 비활성화 시킬 수 있습니다:

```javascript
elixir.config.sourcemaps = false;

elixir(function(mix) {
    mix.sass('app.scss');
});
```

<a name="working-with-scripts"></a>
## 스크립트 작업

또한 Elixir는 ECMAScript 6 컴파일, CoffeeScript 컴파일, Browserify, 스크립트 압축, JavaScript 파일 결합 등의 JavaScript 작업을 도와주는 많은 기능들을 제공합니다. 

<a name="coffeescript"></a>
### CoffeeScript

`coffee` 메소드는 [CoffeeScript](http://coffeescript.org/)를 일반적인 JavaScript 파일로 컴파일하는데 사용됩니다. `coffee` 함수는 `resources/assets/coffee` 디렉토리에 들어 있는 CoffeeScript 파일들의 이름으로 된 배열을 인자로 전달 받아 `public/js` 디렉토리에 하나의 `app.js` 파일을 생성합니다. 

```javascript
elixir(function(mix) {
    mix.coffee(['app.coffee', 'controllers.coffee']);
});
```

<a name="browserify"></a>
### Browserify

Elixir는 또한 ECMAScript 6와 JSX 를 사용하는 모듈들의 require 를 구성하는 장점을 활용할 수 있도록 `browserify` 메소드도 제공합니다.  

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

다른 출력 위치를 선택하려면, 원하는 경로를 두번째 인자로 지정하면 됩니다. 이 메소드의 사용법과 기능은 Babel을 컴파일 한다는 점을 제외하고, `mix.scripts()` 와 동일합니다.

<a name="javascript"></a>
### Scripts


여러개의 JavaScript 파일을 가지고 있고, 이것들을 하나의 파일로 합치고자 한다면, `scripts` 메소드를 사용하면 됩니다. 

`scripts` 메소드는 `resources/assets/js` 디렉토리에서 파일들을 찾는다고 가정합니다. 그리고 기본적으로 `public/js/all.js` 파일이 생성됩니다. 

```javascript
elixir(function(mix) {
    mix.scripts([
        'jquery.js',
        'app.js'
    ]);
});
```

여러개의 파일들을 각기 다른 파일로 합치고자 한다면, `scripts` 메소드를 여러번 호출하면 됩니다. 메소드에 주어진 두번째 인자는 각각의 결합된 파일의 이름이 됩니다:

```javascript
elixir(function(mix) {
    mix.scripts(['app.js', 'controllers.js'], 'public/js/app.js')
       .scripts(['forum.js', 'threads.js'], 'public/js/forum.js');
});
```

지정된 디렉토리에 모든 스크립트들을 합치고자 한다면 `scriptIn` 메소드를 사용하면 됩니다. 합쳐진 JavaScript 는 `public/js/all.js` 가 될것입니다. 

```javascript
elixir(function(mix) {
    mix.scriptsIn('public/js/some/directory');
});
```

<a name="copying-files-and-directories"></a>
## 파일 & 디렉토리 복사

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
## 버전지정 / 캐시 갱신

많은 개발자들이 기본 코드 대신 새로운 assets 을 강제로 로드하게끔 하기 위해서 컴파일된 assets 에 timestamp 또는 고유한 토큰을 접미사로 추가합니다. Elixir는 이러한 문제를 처리하기 위해 `version` 메소드를 사용합니다.

`version` 메소드는 `public` 디렉토리에 있는 파일을 인자로 전달 받고, 캐시를 갱신하기 위해서 파일 이름에 고유한 해시값을 추가할 것입니다. 다음 예제에서 생성된 파일은 `all-16d570a7.css`와 같은 이름이 될것입니다. 

```javascript
elixir(function(mix) {
    mix.version('css/all.css');
});
```

버전이 지정된 파일이 생성되면, [뷰-views](/docs/{{version}}/views)에서 라라벨의 `elixir` 헬퍼 함수를 사용하여 해시값이 붙은 asset 을 로딩할 수 있습니다. `elixir`함수는 자동으로 해시값이 붙어 있는 파일이름을 결정합니다.

    <link rel="stylesheet" href="{{ elixir('css/all.css') }}">

#### 다수의 파일들을 버전 처리하기

`version` 메소드에 여러개의 파일을 전달 할 수도 있습니다:

```javascript
elixir(function(mix) {
    mix.version(['css/all.css', 'js/app.js']);
});
```

파일에 버전이 지정되면 `elixir` 헬퍼 함수는 해시된 실제 파일의 링크를 생성합니다. 주의할 점은 `elixir` 헬퍼 함수에 해시를 붙이지 않은 파일 이름을 전달해야 한다는 것입니다. 헬퍼 함수는 이 해시를 붙이지 않은 파일 이름을 통해 현재의 해시가 붙은 파일 이름을 찾게 됩니다:

    <link rel="stylesheet" href="{{ elixir('css/all.css') }}">

    <script src="{{ elixir('js/app.js') }}"></script>

<a name="browser-sync"></a>
## BrowserSync

BrowserSync 는 여러분의 프론트엔드 리소스에 변경이 발생했을 때, 자동으로 웹 브라우저를 다시 로드 합니다. `gulp watch` 명령어를 실행할 때 BrowserSync를 시작하도록 Elixir 에 지시하기위해서 `BrowserSync` 메소드를 사용할 수 있습니다. 

```javascript
elixir(function(mix) {
    mix.browserSync();
});
```

`gulp watch` 를 실행하고, 브라우저 동기화를 활성화 하기 위해서 웹 애플리케이션에 포트 3000으로 접속하십시오: `http://homestead.app:3000`. 로컬 개발 환경에서 `homestead.app` 이외에 다른 도메인을 사용한다면 `browserSync` 메소드의 첫번째 인자로 [옵션](http://www.browsersync.io/docs/options/)을 지정하여 전달 하십시오

```javascript
elixir(function(mix) {
    mix.browserSync({
        proxy: 'project.app'
    });
});
```

<a name="calling-existing-gulp-tasks"></a>
## 기존의 Gulp 작업 호출하기

Elixir 에서 이미 존재하는 Gulp 작업을 호출할 필요가 있다면 `task` 메소드를 사용하면 됩니다. 예를 들어 간한단 메세지를 출력하는 Gulp 작업이 있다고 생각해 봅시다:

```javascript
gulp.task('speak', function() {
    var message = 'Tea...Earl Grey...Hot';

    gulp.src('').pipe(shell('say ' + message));
});
```

이 작업을 Elixir 에서 호출하려면 `mix.task` 메소드를 사용하여 첫번째 인자로 작업의 이름을 메소드에 전달하면 됩니다:

```javascript
elixir(function(mix) {
    mix.task('speak');
});
```

#### 사용자 지정 파일 감시

사용자 지정 작업을 실행하기 위해서 매번 어떤 파일들이 수정되었는지 감시하려는 대상 파일을 등록하려면 `task` 메소드의 두번째 인자로 정규표현식을 지정하면 됩니다:

```javascript
elixir(function(mix) {
    mix.task('speak', 'app/**/*.php');
});
```

<a name="writing-elixir-extensions"></a>
## Elixir 확장 기능 작성하기

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

이렇게 작성합니다! 기억할 것은 여러분의 Gulp 로직은 `Task` 생성자의 두번째 인자로 전달된 함수 안에 작성되어야 한다는 것입니다. 이 내용은 Gulp 파일의 가장 최상단에 작성하거나 또는 외부 파일에 구성할 수도 있습니다. 예를 들어 확장 기능에 대한 정의를 `elixir-extensions.js` 로 지정할 수 있으며 다음과 같이 `Gulpfile` 에서 불러 들일수도 있습니다. 

```javascript
// File: Gulpfile.js

var elixir = require('laravel-elixir');

require('./elixir-extensions')

elixir(function(mix) {
    mix.speak('Tea, Earl Grey, Hot');
});
```

#### 사용자 지정 파일 감시

`gulp watch` 가 실행되는 동안 사용자 지정 작업을 다시 시작하려면 파일 감시를 등록하면 됩니다:

```javascript
new Task('speak', function() {
    return gulp.src('').pipe(shell('say ' + message));
})
.watch('./app/**');
```
