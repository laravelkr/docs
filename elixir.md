# 라라벨 엘릭서(Laravel Elixir)

- [소개](#introduction)
- [설치 및 준비](#installation)
- [사용법](#usage)
- [Gulp](#gulp)
- [Extensions](#extensions)

<a name="introduction"></a>
## 소개

라라벨 Elixir(엘릭서)는 여러분의 라라벨 어플리케이션에 기본적인 [Gulp](http://gulpjs.com) 작업들을 정의하기 위해 깔끔하고 편리한 API를 제공합니다. Elixir는 몇 가지 공통적인 CSS와 JavaScript 전처리 작업, 또한, 테스팅 툴까지 제공합니다.

만약 여러분이 Gulp과 asset compilation를 어떻게 시작해야 되는지 어려워한 적이 있다면 라라벨 Elixir를 좋아하게 될 것입니다!

<!--chak-comment-라라벨-엘릭서(Laravel-Elixir)-소개-->

<a name="installation"></a>
## 설치 및 준비

### Node 설치하기

Elixir를 사용하기전 여러분의 작업환경에 Node.js가 설치되어있는지 확인하십시오.

    node -v

기본적으로 라라벨의 홈스테드는 여러분이 필요로하는 모든것을 포함하고 있습니다. 그러나 Vagrant를 사용하지 않으신다면 [Node.js 다운로드 페이지](http://nodejs.org/download/)에서 관련 문서를 통해서 쉽게 설치할 수 있습니다. 걱정하지 마십시오! 설치는 간단하고 빠르게 진행됩니다.

### Gulp

다음으로 아래와 같이 [Gulp](http://gulpjs.com)을 글로벌 NPM 패키지로 설치합니다.

    npm install --global gulp

### 라라벨 Elixir

마지막으로 남은 과정은 Elixir를 설치하는 것 뿐입니다. 라라벨을 새로 설치하면 루트 폴더에 위치한 `package.json` 파일을 볼 수 있습니다. 이 파일은 PHP 대신 Node 의존 패키지를 정의 한다는것을 빼고는 여러분의 `composer.json`과 동일합니다. 아래의 명령어로 의존 패키지들을 설치할 수 있습니다.

	npm install

<!--chak-comment-라라벨-엘릭서(Laravel-Elixir)-설치-및-준비-->

<a name="usage"></a>
## 사용법

Elixir 설치를 완료했으므로 이제 바로 파일들을 컴파일하고 결합할 수 있습니다! 프로젝트 루트 폴더에 존재하는 `gulpfile.js` 파일은 모든 Elixir 작업들을 가지고 있습니다..

#### Less 컴파일하기

```javascript
elixir(function(mix) {
	mix.less("app.less");
});
```

위의 예제에서 Elixir는 Less 파일들이 `resources/assets/less` 안에 위치하다고 가정하고 있습니다.

#### 여러개의 Less 파일들 컴파일하기

```javascript
elixir(function(mix) {
	mix.less([
		'app.less',
		'something-else.less'
	]);
});
```

#### Sass 컴파일하기

```javascript
elixir(function(mix) {
	mix.sass("app.scss");
});
```

이 때 Sass 파일들은 `resources/assets/sass`에 포함되어있다고 가정하고 있습니다.

기본적으로, Elixir는 컴파일을 위해 LibSass 라이브러리를 사용하고 있습니다. 몇몇의 경우에는 비록 느리지만 기능이 풍부한 Ruby 버전을 사용하는 것이 더 유리할 수도 있습니다. Ruby와 Sass가 gem(`gem install sass`)으로 설치되었다면, 다음처럼 Ruby-mode를 사용할 수 있습니다.

```javascript
elixir(function(mix) {
	mix.rubySass("app.sass");
});
```

#### 소스 맵없이 컴파일 하기

```javascript
elixir.config.sourcemaps = false;

elixir(function(mix) {
	mix.sass("app.scss");
});
```

별다른 설정 없이도 소스 맵은 활성화 되어있습니다. 각 파일들이 컴파일 될때 `*.css.map` 파일이 같은 폴더 안에 생성되는것을 확인하실 수 있습니다. 이 매핑은 여러분이 디버깅을 할 때 컴파일된 스타일 시트 셀렉터에서 원래의 Sass 나 Less에서 정의된 부분을 찾을 수 있게 해줍니다. 하지만 이 기능을 비활성화 하길 원한다면 위와 같은 코드 샘플을 사용할 수 있습니다.

#### CoffeeScript 컴파일하기

```javascript
elixir(function(mix) {
	mix.coffee();
});
```

이 코드는 CoffeeScript 파일들이 `resources/assets/coffee`에 저장되어 있다고 가정합니다.

#### 모든 Less와 CoffeeScript 컴파일하기

```javascript
elixir(function(mix) {
    mix.less()
       .coffee();
});
```

#### PHPUnit 테스트 시작

```javascript
elixir(function(mix) {
	mix.phpUnit();
});
```

#### PHPSpec 테스트 시작

```javascript
elixir(function(mix) {
	mix.phpSpec();
});
```

#### 스타일 시트 결합하기

```javascript
elixir(function(mix) {
	mix.styles([
		"normalize.css",
		"main.css"
	]);
});
```

이 메소드에 전달된 경로는 `resources/assets/css` 폴더의 상대 경로입니다.

#### 스타일 시트를 결합하고 사용자 정의 폴더에 저장하기

```javascript
elixir(function(mix) {
	mix.styles([
		"normalize.css",
		"main.css"
	], 'public/build/css/everything.css');
});
```

#### 사용자 정의된 폴더로부터 스타일 시트 결합하기

```javascript
elixir(function(mix) {
	mix.styles([
		"normalize.css",
		"main.css"
	], 'public/build/css/everything.css', 'public/css');
});
```

`styles`와 `scripts` 메소드의 세번 째 인자는 메소드에 전달되는 모든 상대 경로에 적용되는 기본 디렉토리를 의미합니다.

#### 폴더에 포함된 모든 스타일 시트 결합하기.

```javascript
elixir(function(mix) {
	mix.stylesIn("public/css");
});
```

#### 스크립트 파일 결합하기.

```javascript
elixir(function(mix) {
	mix.scripts([
		"jquery.js",
		"app.js"
	]);
});
```


이 경우에서도 전체 경로는 `resources/assets/js` 폴더의 상대 경로로 가정됩니다.

#### 폴더내의 모든 스크립트 파일 결합하기

```javascript
elixir(function(mix) {
	mix.scriptsIn("public/js/some/directory");
});
```

#### 여러 스크립트 파일 결합하기.

```javascript
elixir(function(mix) {
    mix.scripts(['jquery.js', 'main.js'], 'public/js/main.js')
       .scripts(['forum.js', 'threads.js'], 'public/js/forum.js');
});
```

#### 버전 / 해시 파일

```javascript
elixir(function(mix) {
	mix.version("css/all.css");
});
```

위의 코드는 캐시-버스팅을 위해 고유한 해시값을를 파일 이름에 추가합니다. 예를 들어, 생성된 파일 이름은 `all-16d570a7.css`와 같이 생성될 것입니다.

뷰 파일 안에서 해쉬된 asset를 로드하기 위해서 `elixir()` 함수를 사용할 수 있습니다. 다음은 그 예제입니다.

```html
<link rel="stylesheet" href="{{ elixir("css/all.css") }}">
```

어플리케이션 내부에서 `elixir()`함수는 로드(include) 해야하는 해시된 파일의 이름을 결정합니다. 벌써 무거운 짐을 내려놓은 것 같지 않으신가요?

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

#### 새로운 경로에 파일 복사하기

```javascript
elixir(function(mix) {
	mix.copy('vendor/foo/bar.css', 'public/css/bar.css');
});
```

#### 새로운 경로에 폴더 전체를 복사하기

```javascript
elixir(function(mix) {
	mix.copy('vendor/package/views', 'resources/views');
});
```

#### Browserify 트리거

```javascript
elixir(function(mix) {
	mix.browserify('index.js');
});
```

브라우저에 모듈들을 포함하고 싶으신가요? EcmaScript 6를 좀더 일찍 사용 하고 싶으신가요? 빌트인 JSX 변환기가 필요하신가요? 그렇다면, [Browserify](http://browserify.org/), browserify Elixir 태스크와 함께 작업을 멋지게 처리하세요.

이 태스크는 여러분의 스크립트가 `resources/assets/js` 저장되어 있다고 가정하지만, 기본 디렉토리를 재정의 할 수도 있습니다.

#### 메소드 체인 (Method Chaining)

당연하게도, 여러분의 거의 모든 Elixir의 메소드를 체이닝(chain)하여 사용할 수 도 있습니다.

```javascript
elixir(function(mix) {
    mix.less("app.less")
       .coffee()
       .phpUnit()
       .version("css/bootstrap.css");
});
```

<!--chak-comment-라라벨-엘릭서(Laravel-Elixir)-사용법-->

<a name="gulp"></a>
## Gulp

이제 작업을 수행하기 위해 커맨드 라인에서 Gulp을 실행만 하면 됩니다.

#### 한번에 모든 등록된 작업을 수행하기

	gulp

#### Assets 변경 감시하기

	gulp watch

#### 스크립트 파일만 컴파일하기

	gulp scripts

#### 스타일시트 파일만 컴파일하기

	gulp styles

#### 테스트와 PHP 클래스의 변경 감시하기

	gulp tdd

> **참고** 모든 작업은 개발환경에서 진행된다고 가정하고 있으므로 압축되지 않을 것입니다. 실서버용(production)으로 작업 하기 위해서는 `gulp --production`을 사용하십시오.

<!--chak-comment-라라벨-엘릭서(Laravel-Elixir)-Gulp-->

<a name="extensions"></a>
## 커스텀 작업과 확장

때때로, 여러분의 고유한 Glup 작업들을 엘릭서 안으로 처리하기를(hook) 원할 수도 있습니다. 아마도 여러분은 엘릭서가 합치고 감시하는 특별한 기능이 필요할 수도 있습니다. 문제 없습니다.

아래의 예제와 같이 작업이 호출되었을때 단순히 텍스트를 출력하는 일반적인 작업이 있다고 가정해봅시다.

```javascript
gulp.task("speak", function() {
	var message = "Tea...Earl Grey...Hot";

	gulp.src("").pipe(shell("say " + message));
});
```

충분히 쉽습니다. 물론 커맨드 라인에서도 작업을 시작하기 위해 `gulp speak`를 호출할 수 있습니다. 그러나 이것을 Elixir에 추가하기 위해서 `mix.task()`메소드를 사용하면됩니다.


```javascript
elixir(function(mix) {
    mix.task('speak');
});
```

이게 다입니다! 이제 Gulp을 실행할때마다 여러분이 지정한 "speak"라는 작업은 여러분이 혼합(mix)한 다른 Elixir 작업들과 함께 실행될것입니다. 덧붙여 하나 혹은 여러개의 파일들이 수정될 때마다 사용자 정의 작업이 다시 실행될 수 있도록 감시자(watcher)를 추가로 등록하기 위해 정규 표현식을 두 번째 인수로 전달할 수 있습니다.

```javascript
elixir(function(mix) {
    mix.task('speak', 'app/**/*.php');
});
```

두 번째 인수를 추가하여 ”app/" 폴더 안의 PHP 파일이 저장될때마다 "speak" 작업을 다시 실행(re-trigger) 하도록 지시 할 수 있습니다.

더 유연하게 사용하기 위해 여러분은 엘릭서(Elixir) extension들을 만들 수 있습니다. 아래와 같이 이전에 사용하던 "speak" 예제로 extension을 작성할 수 있습니다.

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

Gulp 파일 안에서 참조할 이름을 전달하여 엘릭서의 API를 `확장` 한것과, 콜백 함수를 통해서 Gulp 작업을 생성한것을 주목하십시오.

이전처럼 여러분의 사용자 정의(custom) 작업이 모니터링 되기 원한다면 감시자(watcher)를 등록하십시오.

```javascript
this.registerWatcher("speak", "app/**/*.php");
```

이 명령은  `app/**/*.php`와 같은 정규식을 일치하는 어떤 파일이 수정되었을때 `speak`작업이 실행되기를 원한다는 것을 나타냅니다.

이것이 끝입니다! 이것을 Gulpfile 상단에 위치시키거나 사용자 정의 작업 파일로 추출(extract)할 수 있습니다. 후자를 선택할경우 Gulpfile 안에 해당 파일을 아래와 같이 로드(require) 하십시오.

```javascript
require("./custom-tasks")
```

끝났습니다! 이제 이것을 혼합(mix)하여 사용할 수 있습니다.

```javascript
elixir(function(mix) {
	mix.speak("Tea, Earl Grey, Hot");
});
```

이렇게 추가하면 Picard는 Gulp이 시행될 때마다 약간의 차(Tea)를 요구할 것입니다.

<!--chak-comment-라라벨-엘릭서(Laravel-Elixir)-커스텀-작업과-확장-->
