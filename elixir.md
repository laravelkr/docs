# Laravel Elixir

- [소개](#introduction)
- [설치 및 준비](#installation)
- [사용법](#usage)
- [Gulp](#gulp)
- [Extensions](#extensions)

<a name="introduction"></a>
## Introduction
## 소개 

Laravel Elixir provides a clean, fluent API for defining basic [Gulp](http://gulpjs.com) tasks for your
Laravel application. Elixir supports several common CSS and JavaScript pre-processors, and even testing tools.
라라벨 Elixir(사전적 정의. 만병통치약)는 여러분의 라라벨 어플리케이션에 기본적인 [Gulp](http://gulpjs.com) 작업들을 정의하기 위해 깔끔하고 능수능란한 API를 제공합니다. Elixir는 몇가지 공통적인 CSS와 JavaScript pre-processors 심지어 테스팅 툴까지 제공합니다.

If you've ever been confused about how to get started with Gulp and asset compilation, you will love Laravel Elixir!
만약 여러분이 Gulp 과 asset compilation로 어떻게 시작할지 혼동한 적이 있다면 여러분은 Laravel Elixir 를 좋아할 것입니다!

<a name="installation"></a>
## Installation & Setup
## 설치 및 준비

### Installing Node
## Node 설치하기

Before triggering Elixir, you must first ensure that Node.js is installed on your machine.
Elixir를 사용하기전 여러분의 머신에 Node.js가 설치되어있는지 확인하십시오.

    node -v

By default, Laravel Homestead includes everything you need; however, if you aren't using Vagrant, then you can easily install Node by visiting [their download page](http://nodejs.org/download/). Don't worry, it's
quick and easy!
기본적으로 라라벨의 홈스테드는 여러분이 필요로하는 모든것을 포함하고 있습니다. 그러나 Vagrant를 사용하지 않으신다면 [their download page](http://nodejs.org/download/)에 방문하여 관련 문서를 읽으시면 쉽게 설치할 수 있습니다. 걱정하지마세요! 이 과정은 빠르고 간단합니다.

### Gulp

Next, you'll want to pull in [Gulp](http://gulpjs.com) as a global NPM package like so:
다음으로 아래와 같이 [Gulp](http://gulpjs.com)을 글로벌 NPM 패키지로 설치합니다.

    npm install --global gulp

### Laravel Elixir
### 라라벨 Elixir

The only remaining step is to install Elixir! With a new install of Laravel, you'll find a `package.json` file in the root. Think of this like your `composer.json` file, except it defines Node dependencies instead of PHP. You may install the dependencies it references by running:
마지막으로 남은 과정은 Elixir를 설치하는 것 뿐입니다. 라라벨을 새로 설치하면 루트 폴더에 위치한 `package.json` 파일을 볼 수 있습니다. 이 파일은 PHP 대신 Node 의존 패키지를 정의 한다는것을 빼고는 여러분의 `composer.json`과 같습니다. 아래의 명령어로 의존 패키지들을 설치할 수 있습니다.

	npm install

<a name="usage"></a>
## Usage
## 사용법

Now that you've installed Elixir, you'll be compiling and concatenating in no time! The `gulpfile.js` file in your project's root directory contains all of your Elixir tasks.
Elixir 설치를 완료했으므로 이제 즉시 컴파일하고 결합할 수 있습니다! 프로젝트 루트 폴더에 존재하는 `gulpfile.js` 파일은 모든 Elixir 작업들을 포함하고 있습니다.

#### Compile Less
Less 컴파일하기

```javascript
elixir(function(mix) {
	mix.less("app.less");
});
```

In the example above, Elixir assumes that your Less files are stored in `resources/assets/less`.
위의 예제에서 Elixir는 Less 파일들이 `resources/assets/less` 안에 위치하다고 가정하고 있습니다.

#### Compile Multiple Less Files
#### 여러개의  Less 파일들 컴파일하기

```javascript
elixir(function(mix) {
	mix.less([
		'app.less',
		'something-else.less'
	]);
});
```

#### Compile Sass
#### Sass 컴파일하기

```javascript
elixir(function(mix) {
	mix.sass("app.sass");
});
```

This assumes that your Sass files are stored in `resources/assets/sass`. The `sass` method may only be called once. If you would like to compile multiple Sass files, pass an array to the `sass` method.
이것은 Sass 파일들이 `resources/assets/sass`에 포함되어있다고 가정하고 있습니다. `sass` 메소드는 한번만 호출될것입니다. 여러 Sass 파일들을 컴파일하고 싶다면, `sass` 메소드에 배열 형태로 전달하십시오.

By default, Elixir, underneath the hood, uses the LibSass library for compilation. 
기본적으로, Elixir는 컴파일을 위해 LibSass 라이브러리를 뒤에서 사용하고 있습니다.
In some instances, it might prove advantageous to instead leverage the Ruby version, which, though slower, is more feature rich. 
몇몇 경우에서는 비록 느리지만 대신 기능이 풍부한 Ruby 버전을 활용하는 것이 유리하다는 것을 증명합니다.
Assuming that you have both Ruby and the Sass gem installed (`gem install sass`), you may enable Ruby-mode, like so:
만약 Ruby 와 Sass가 gem (`gem install sass`) 으로 설치되어있다면 Ruby-mode를 아래와 같이 사용할 수 있습니다.

```javascript
elixir(function(mix) {
	mix.rubySass("app.sass");
});
```

#### Compile Without Source Maps
#### 소스 맵을 만들지 않고 컴파일 하기

```javascript
elixir.config.sourcemaps = false;

elixir(function(mix) {
	mix.sass("app.scss");
});
```

Source maps are enabled out of the box. As such, for each file that is compiled, you'll find a companion `*.css.map` file in the same directory. 소스 맵 생성은 기본으로 활성화 되어있습니다. 각 파일들이 컴파일 될때 `*.css.map` 파일이 같은 폴더안에 생성되는것을 확인할 수 있습니다.
This mapping allows you to, when debugging, trace your compiled stylesheet selectors  back to your original Sass or Less partials! 
이 매핑은 여러분이 디버깅을 하거나 컴파일된 스타일 시트 셀렉터에서 원래 Sass 나 Less에서 정의된 부분을 찾을 수 있도록 합니다.
Should you need to disable this functionality, however, the code sample above will do the trick.
그러나 이 기능을 끄기 원한다면 위와같은 코드 샘플을 사용할 수 있습니다.

#### Compile CoffeeScript
#### CoffeeScript 컴파일하기

```javascript
elixir(function(mix) {
	mix.coffee();
});
```

This assumes that your CoffeeScript files are stored in `resources/assets/coffee`.
이 코드는 CoffeeScript 파일들이 `resources/assets/coffee`에 저장되어 있다고 가정합니다.

#### Compile All Less and CoffeeScript
#### 모든 Less 와 CoffeeScript 컴파일하기

```javascript
elixir(function(mix) {
    mix.less()
       .coffee();
});
```

#### Trigger PHPUnit Tests
#### PHPUnit 테스트 시작

```javascript
elixir(function(mix) {
	mix.phpUnit();
});
```

#### Trigger PHPSpec Tests
#### PHPSpec 테스트 시작

```javascript
elixir(function(mix) {
	mix.phpSpec();
});
```

#### Combine Stylesheets
#### 스타일 시트 결합하기

```javascript
elixir(function(mix) {
	mix.styles([
		"normalize.css",
		"main.css"
	]);
});
```

Paths passed to this method are relative to the `resources/css` directory.
이 메소드에 전달된 경로는 `resources/css` 폴더의 상대 경로입니다.

#### Combine Stylesheets and Save to a Custom Directory
#### 스타일 시트를 결합하고 사용자 정의 폴더에 저장하기

```javascript
elixir(function(mix) {
	mix.styles([
		"normalize.css",
		"main.css"
	], 'public/build/css/everything.css');
});
```

#### Combine Stylesheets From A Custom Base Directory
#### 사용자 정의된 폴더로부터 스타일 시트 결합하기

```javascript
elixir(function(mix) {
	mix.styles([
		"normalize.css",
		"main.css"
	], 'public/build/css/everything.css', 'public/css');
});
```

The third argument to both the `styles` and `scripts` methods determines the relative directory for all paths passed to the methods.
`styles`와 `scripts` 메소드 모두 세번 째 인수는 메소드에 전달되는 모든 상대 경로에 적용되는 기본 디렉토리입니다.

#### Combine All Styles in a Directory
#### 폴더에 포함된 모든 스타일 시트 결합하기.

```javascript
elixir(function(mix) {
	mix.stylesIn("public/css");
});
```

#### Combine Scripts
#### 스크립트 파일 결합하기.

```javascript
elixir(function(mix) {
	mix.scripts([
		"jquery.js",
		"app.js"
	]);
});
```

Again, this assumes all paths are relative to the `resources/js` directory.
이 경우에서도 전체 경로는 `resources/js` 폴더의 상대 경로로 가정됩니다.

#### Combine All Scripts in a Directory
#### 폴더내의 모든 스크립트 파일 결합하기

```javascript
elixir(function(mix) {
	mix.scriptsIn("public/js/some/directory");
});
```

#### Combine Multiple Sets of Scripts
#### 여러 스크립트 파일 결합하기.

```javascript
elixir(function(mix) {
    mix.scripts(['jquery.js', 'main.js'], 'public/js/main.js')
       .scripts(['forum.js', 'threads.js'], 'public/js/forum.js');
});
```

#### Version / Hash A File
#### 버전 / 해시 파일

```javascript
elixir(function(mix) {
	mix.version("css/all.css");
});
```

This will append a unique hash to the filename, allowing for cache-busting. For example, the generated file name will look something like: `all-16d570a7.css`.
cache-busting을 위해 고유의 해시를 파일 이름에 추가합니다. 예를들어 생성된 파일 이름은 `all-16d570a7.css`와 같이 생성될 것입니다.

Within your views, you may use the `elixir()` function to load the appropriately hashed asset. Here's an example:
뷰(view)에서 `elixir()` 함수는 적절하게 해쉬된 asset를 로드합니다. 아래의 예제가 있습니다.

```html
<link rel="stylesheet" href="{{ elixir("css/all.css") }}">
```

Behind the scenes, the `elixir()` function will determine the name of the hashed file that should be included. Don't you feel the weight lifting off your shoulders already?
뒤에서 `elixir()`함수는 로드(include) 해야하는 해시된 파일의 이름을 결정합니다. 벌써 무거운 짐을 내려놓은 것 같지 않으시나요?

You may also pass an array to the `version` method to version multiple files:
여러 파일들에 버전 지정을 위해 `version` 메소드에 배열을 전달할 수 있습니다.

```javascript
elixir(function(mix) {
	mix.version(["css/all.css", "js/app.js"]);
});
```

```html
<link rel="stylesheet" href="{{ elixir("css/all.css") }}">
<script src="{{ elixir("js/app.js") }}"></script>
```

#### Copy a File to a New Location
#### 새로운 경로에 파일 복사하기

```javascript
elixir(function(mix) {
	mix.copy('vendor/foo/bar.css', 'public/css/bar.css');
});
```

#### Copy an Entire Directory to a New Location
#### 새로운 경로에 폴더 전체를 복사하기

```javascript
elixir(function(mix) {
	mix.copy('vendor/package/views', 'resources/views');
});
```

#### Method Chaining
#### 메소드 체인 (Method Chaining)

Of course, you may chain almost all of Elixir's methods together to build your recipe:
물론, 여러분의 레시피를 만들기 위해 거의 모든 Elixir의 메소드를 연결(chain)하여 사용할 수 도 있습니다.

```javascript
elixir(function(mix) {
    mix.less("app.less")
       .coffee()
       .phpUnit()
       .version("css/bootstrap.css");
});
```

<a name="gulp"></a>
## Gulp

Now that you've told Elixir which tasks to execute, you only need to trigger Gulp from the command line.
이제 작업을 수행하기 위해 커맨드 라인에서 Gulp을 실행만 하면 됩니다.

#### Execute All Registered Tasks Once
#### 한번에 모든 등록된 작업을 수행하기

	gulp

#### Watch Assets For Changes
#### Assets 변경 감시하기

	gulp watch

#### Only Compile Scripts
#### 스크립트 파일만 컴파일하기

	gulp scripts

#### Only Compile Styles
#### 스타일시트 파일만 컴파일하기

	gulp styles

#### Watch Tests And PHP Classes for Changes
#### 테스트와 PHP 클래스의 변경 감시하기

	gulp tdd

> **Note:** All tasks will assume a development environment, and will exclude minification. For production, use `gulp --production`.
> **참고** 모든 작업은 개발 환경이라고 가정하고 있기 때문에 압축되지 않습니다. 생산용(production)으로 작업 하기 위해서는 `gulp --production`을 사용하십시오.

<a name="extensions"></a>
## Custom Tasks and Extensions
## 커스텀 작업과 확장

Sometimes, you'll want to hook your own Gulp tasks into Elixir. Perhaps you have a special bit of functionality that you'd like Elixir to mix and watch for you. No problem!
때때로 여러분은 Gulp 작업들을 엘릭서에 훅(hook) 처리 하기를 원합니다.  Perhaps you have a special bit of functionality that you'd like Elixir to mix and watch for you. No problem!

As an example, imagine that you have a general task that simply speaks a bit of text when called. 아래의 예제와 같이 작업이 호출되었을때 단순히 텍스트를 출력하는 일반적인 작업이 있다고 가정해봅시다.

```javascript
gulp.task("speak", function() {
	var message = "Tea...Earl Grey...Hot";

	gulp.src("").pipe(shell("say " + message));
});
```

Easy enough. From the command line, you may, of course, call `gulp speak` to trigger the task. To add it to Elixir, however, use the `mix.task()` method:
충분히 쉽습니다. 물론 커맨드 라인에서도 작업을 시작하기 위해 `gulp speak`를 호출할 수 있습니다. 그러나 이것을 Elixir에 추가하기 위해서는 `mix.task()`메소드를 사용하십시오.

```javascript
elixir(function(mix) {
    mix.task('speak');
});
```

That's it! Now, each time you run Gulp, your custom "speak" task will be executed alongside any other Elixir tasks that you've mixed in. 
이것이 다입니다! 이제 Gulp을 실행할때마다 사용자 정의된 "speak" 작업은 여러분이 혼합(mix)한 다른 Elixir 작업들과 함께 실행될것입니다.
To additionally register a watcher, so that your custom tasks will be re-triggered each time one or more files are modified, you may pass a regular expression as the second argument.
한개 혹은 여러개의 파일들이 수정될 때마다 사용자 정의 작업이 다시 실행될 수 있도록 감시자(watcher)를 추가로 등록하기 위해 정규 표현식을 두번째 인수로 전달할 수 있습니다.

```javascript
elixir(function(mix) {
    mix.task('speak', 'app/**/*.php');
});
```

By adding this second argument, we've instructed Elixir to re-trigger the "speak" task each time a PHP file in the "app/" directory is saved.
두 번째 인수를 추가하여 ”app/" 폴더 안의 PHP 파일이 저장될때마다 "speak" 작업을 다시 실행(re-trigger) 하도록 지시 할 수 있습니다.

For even more flexibility, you can create full Elixir extensions. Using the previous "speak" example, you may write an extension, like so:
더 유연하게 사용하기 위해 여러분은 전체의 엘릭서(Elixir) extension들을 만들 수 있습니다. 아래와 같이 이전에 사용하던 "speak" 예제로 extension을 작성할 수 있습니다.

```javascript
var gulp = require("gulp");
var shell = require("gulp-shell");
var elixir = require("laravel-elixir");

elixir.extend("speak", function(message) {

	gulp.task("speak", function() {
		gulp.src("").pipe(shell("say " + message));
	});

	return this.queueTask("speak");

 });
```

Notice that we `extend` Elixir's API by passing the name that we will reference within our Gulpfile, as well as a callback function that will create the Gulp task.

As before, if you want your custom task to be monitored, then register a watcher.
이전처럼 여러분의 사용자 정의 작업이 모니터링 되기 원한다면 감시자(watcher)를 등록하십시오.

```javascript
this.registerWatcher("speak", "app/**/*.php");
```

This lines designates that when any file that matches the regular expression, `app/**/*.php`, is modified, we want to trigger the `speak` task.
이 명령은  `app/**/*.php`와 같은 정규식을 일치하는 어떤 파일이 수정되었을때 `speak`작업이 실행되기를 원한다는 것을 나타냅니다.

That's it! You may either place this at the top of your Gulpfile, or instead extract it to a custom tasks file. If you choose the latter approach, simply require it into your Gulpfile, like so:
이것이 끝입니다! 이것을 Gulpfile 상단에 위치시키거나 사용자 정의 작업 파일에 추출할 수 있습니다. 후자를 선택할경우 Gulpfile 안에 이것을 아래와 같이 로드(require) 하십시오.

```javascript
require("./custom-tasks")
```

You're done! Now, you can mix it in.
끝났습니다! 이제 이것을 혼합(mix)하여 사용할 수 있습니다.

```javascript
elixir(function(mix) {
	mix.speak("Tea, Earl Grey, Hot");
});
```

With this addition, each time you trigger Gulp, Picard will request some tea.
이렇게 추가하면 Picard는 Gulp이 시행될 때마다 약간의 차(Tea)를 요구할 것입니다.
