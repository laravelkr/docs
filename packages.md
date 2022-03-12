# 패키지 개발

- [시작하기](#introduction)
    - [파사드 사용의 주의사항](#a-note-on-facades)
- [패키지 Discovery](#package-discovery)
- [서비스 프로바이더](#service-providers)
- [Resources](#resources)
    - [설정파일](#configuration)
    - [마이그레이션 파일들](#migrations)
    - [라우트](#routes)
    - [언어 파일](#translations)
    - [뷰 파일들](#views)
    - [뷰 컴포넌트 파일](#view-components)
- [명령어](#commands)
- [Public Assets](#public-assets)
- [파일을 그룹단위로 게시하기](#publishing-file-groups)

<a name="introduction"></a>
## 시작하기

패키지는 라라벨에 기능을 추가하는 주요 방법입니다. 패키지는 [Carbon](https://github.com/briannesbitt/Carbon) 과 같은 날짜와 관련된 작업을 위한 좋은 패키지나 Spatie의 [Laravel Media Library](https://github.com/spatie/laravel-medialibrary) 와 같은 Eloquent 모델과 파일을 연결할 수 있는 패키지까지 다양합니다.

다양한 종류의 패키지가 있습니다. 일부 패키지는 독립 실행형입니다. 즉, 모든 PHP 프레임워크와 함께 작동합니다. Carbon과 PHPUnit은 독립 실행형 패키지의 예입니다. 이러한 패키지는 `composer.json` 파일에서 요구하여 라라벨과 함께 사용할 수 있습니다.

반대로, 라라벨 프레임워크에만 사용할 수 있는 패키지들도 있습니다. 이러한 패키지들은 라라벨 애플리케이션의 기능을 사용하는 라우트, 컨트롤러, 뷰, 설정들을 가질 것입니다. 이 가이드는 라라벨에 특화된 패키지의 개발을 주로 설명합니다.

<a name="a-note-on-facades"></a>
### 파사드 사용의 주의사항

라라벨 애플리케이션을 개발할 때는, 일반적으로 contract이나 파사드를 사용하는 것이 본질적으로 동일한 수준의 테스트 가능성을 제공하기 때문에 중요하지 않습니다. 그러나 패키지를 작성할 때 패키지는 일반적으로 라라벨의 모든 테스트 헬퍼에 액세스할 수 없습니다. 패키지가 일반적인 라라벨 애플리케이션 내부에 설치된 것처럼 패키지 테스트를 작성하려면 [Orchestral Testbench](https://github.com/orchestral/testbench) 패키지를 사용하면 됩니다.

<a name="package-discovery"></a>
## 패키지 Discovery

라라벨 애플리케이션의 `config/app.php` 설정 파일안에는 라라벨에서 로딩되어야 하는 서비스 프로바이더들의 리스트가 `providers` 옵션에 정의되어 있습니다. 패키지를 인스톨하게 되면, 일반적으로 서비스 프로바이더가 이 리스트에 포함되기를 원할 수 있습니다. 사용자가 직접  서비스 프러바이더를 이 목록에 추가하는 대신에, 패키지의 `composer.json` 파일의 `extra` 섹션에서 프로바이더를 정의할 수 있습니다. 서비스 프로바이더에 더해서, 등록하고자 하는 [facades](/docs/{{version}}/facades)도 나열 할 수 있습니다.

    "extra": {
        "laravel": {
            "providers": [
                "Barryvdh\\Debugbar\\ServiceProvider"
            ],
            "aliases": {
                "Debugbar": "Barryvdh\\Debugbar\\Facade"
            }
        }
    },

패키지 discovery 를 위한 설정이 되었다면, 라라벨은 패키지가 설치되었을 때 자동으로 서비스 프로바이더와 파사드를 등록하여 패키지 사용자에게 편리한 설치 경험을 제공해주게 됩니다.

<a name="opting-out-of-package-discovery"></a>
### 패키지 Discovery에서 제외시키기

패키지 사용자가 패키지 discovery 기능을 사용하지 않기를 원한다면, 애플리케이션의 `composer.json` 파일의 `extra` 섹션에 패키지 이름을 나열해놓으면 됩니다.

    "extra": {
        "laravel": {
            "dont-discover": [
                "barryvdh/laravel-debugbar"
            ]
        }
    },

애플리케이션의 `dont-discover` 지시어안에서 `*` 문자열을 사용하여 모든 패키지 dicovery를 비활성화 할 수도 있습니다.

    "extra": {
        "laravel": {
            "dont-discover": [
                "*"
            ]
        }
    },

<a name="service-providers"></a>
## 서비스 프로바이더

[서비스 프로바이더](/docs/{{version}}/providers)는 패키지와 라라벨 사이의 연결 지점입니다. 서비스 프로바이더는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에 바인딩하고 뷰, 설정 및 현지화 파일과 같은 패키지 리소스를 로드할 위치를 라라벨에 알려줄 책임이 있습니다.

서비스 프로바이더는 `Illuminate\Support\ServiceProvider`를 상속 받고, 다음의 두개의 메소드: `register` 와 `boot` 메소드를 포함합니다. 베이스 `ServiceProvider` 클래스는 컴포저 패키지의 `illuminate/support` 에 위치하고 있으며, 여러분의 패키지에 필요한 의존성을 컴포저에 추가해야 합니다. 서비스 프로바이더의 구조와 목적에 대한 보다 자세한 사항은 [문서](/docs/{{version}}/providers)를 참고하십시오.

<a name="resources"></a>
## Resources

<a name="configuration"></a>
### 설정파일

일반적으로 패키지의 설정 파일을 애플리케이션의 `config` 디렉토리에 게시해야 합니다. 이렇게 하면 패키지 사용자가 기본 설정 옵션을 쉽게 변경할 수 있습니다. 설정 파일을 게시할 수 있도록 하려면 서비스 프로바이더의 `boot` 메서드에서 `publishes` 메서드를 호출하세요.

    /**
     * Bootstrap any package services.
     *
     * @return void
     */
    public function boot()
    {
        $this->publishes([
            __DIR__.'/../config/courier.php' => config_path('courier.php'),
        ]);
    }

이제, 라라벨의 `vendor:publish` 명령어가 실행될 때 파일들이 지정된 위치로 복사될 것입니다. 패키지의 설정 파일들이 퍼블리싱 되고 나면, 이 설정 값들은 다른 설정 파일들과 같이 엑세스 할 수 있습니다.

    $value = config('courier.option');

> {note} 설정 파일에 클로저를 정의해서는 안 됩니다. 사용자가 `config:cache` Artisan 명령을 실행할 때 올바르게 직렬화할 수 없습니다.

<a name="default-package-configuration"></a>
#### 패키지 기본 설정

또한 자신의 패키지 설정 파일을 애플리케이션의 게시된 복사본과 병합할 수 있습니다. 이렇게 하면 사용자가 설정 파일의 게시된 복사본에서 실제로 재정의하려는 옵션만 정의할 수 있습니다. 설정 파일 값을 병합하려면 서비스 프로바이더의 `register` 메소드 내에서 `mergeConfigFrom` 메소드를 사용하십시오.

`mergeConfigFrom` 메소드는 패키지의 설정 파일에 대한 경로를 첫 번째 인수로 입력받고, 애플리케이션의 설정 파일 복사본 이름을 두 번째 인수로 입력받습니다.

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        $this->mergeConfigFrom(
            __DIR__.'/../config/courier.php', 'courier'
        );
    }

> {note} 이 메소드는 설정 배열의 첫번째 레벨만을 병합합니다. 만약 사용자가 부분적으로 다차원 배열로 된 설정 배열을 정의한다면, 손실된 옵션은 병합되지 않습니다.

<a name="routes"></a>
### 라우트

패키지가 라우트를 포함하고 있다면, `loadRoutesForm` 메소드를 사용하여 이를 로딩해야 합니다. 이 메소드는 애플리케이션의 라우트가 캐싱되어 있는지를 자동으로 확인하여, 라우트가 이미 캐싱되어 있는 경우에는 라우트를 로딩하지 않습니다.

    /**
     * Bootstrap any package services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadRoutesFrom(__DIR__.'/../routes/web.php');
    }

<a name="migrations"></a>
### 마이그레이션 파일들

패키지가 [데이터베이스 마이그레이션 파일들](/docs/{{version}}/migrations)을 가지고 있다면, 라라벨에게 이를 어떻게 로딩할 수 있도록 알려주는데 `loadMigrationsFrom` 메소드를 사용할 수 있습니다. `loadMigrationsFrom` 메소드는 여러분의 패키지에서 마이그레이션이 있는 위치를 유일한 인자로 전달받습니다.

    /**
     * Bootstrap any package services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadMigrationsFrom(__DIR__.'/../database/migrations');
    }

패키지의 마이그레이션이 등록되면 `php artisan migrate` 명령이 실행될 때 자동으로 실행됩니다. 애플리케이션의 `database/migrations` 디렉토리로 내보낼 필요가 없습니다.

<a name="translations"></a>
### 언어 파일

패키지가 [언어 파일](/docs/{{version}}/localization)을 가지고 있다면, `loadTranslationsFrom` 메소드를 사용하여 라라벨이 이를 로드할 수 있게 할 수 있습니다. 예를 들어 패키지 이름이 `courier` 라면, 다음처럼 서비스 프로바이더의 `boot` 메소드에 다음 라인을 추가해야 합니다.

    /**
     * Bootstrap any package services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadTranslationsFrom(__DIR__.'/../resources/lang', 'courier');
    }

패키지의 언어 파일들은 `package::file.line` 문법을 사용하여 편리하게 참조됩니다. 따라서 `courier` 패키지의 `message`파일에서 `welcome` 라인을 다음처럼 로드 할 수 있습니다.

    echo trans('courier::messages.welcome');

<a name="publishing-translations"></a>
#### 언어 파일 퍼블리싱하기

패키지의 언어파일을 애플리케이션의 `resources/lang/vendor` 디렉토리로 퍼블리싱하려면 서비스 프로바이더의 `publishes` 메소드를 사용하면 됩니다. `publishes` 메소드는 패키지 경로와 퍼블리싱 되기를 바라는 위치를 나타내는 배열을 인자로 전달 받습니다. 예를 들어 `courier` 패키지의 언어 파일을 퍼블리싱 하려면, 다음과 같이 할 수 있습니다.

    /**
     * Bootstrap any package services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadTranslationsFrom(__DIR__.'/../resources/lang', 'courier');

        $this->publishes([
            __DIR__.'/../resources/lang' => resource_path('lang/vendor/courier'),
        ]);
    }

이제 라라벨의 `vendor:publish` 아티즌 명령어가 실행될 때 패키지의 언어 파일들은 지정된 퍼블리싱 위치로 복사될 것입니다.

<a name="views"></a>
### 뷰-views

라라벨에 패키지의 [뷰-views](/docs/{{version}}/views)를 등록하기 위해서는 뷰 파일이 어디에 위치하고 있는지 라라벨에 알려줄 필요가 있습니다. 이를 위해서는 서비스 프로바이더의 `loadViewsForm` 메소드를 사용하면 됩니다. `loadViewsFrom` 메소드는 두개의 인자를 받아 들이는데: 뷰 템플릿의 패스와, 패키지의 이름입니다. 예를 들어 패키지 이름이 `courier`라면, 다음의 서비스 프로바이더 `boot` 메소드 처럼 추가하면 됩니다.

    /**
     * Bootstrap any package services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadViewsFrom(__DIR__.'/../resources/views', 'courier');
    }

패키지 뷰는 `package::view` 구문 규칙을 사용하여 참조됩니다. 따라서 뷰 경로가 서비스 프로바이더에 등록되면 다음과 같이 `courier` 패키지에서 `dashboard` 뷰를 로드할 수 있습니다.

    Route::get('/dashboard', function () {
        return view('courier::dashboard');
    });

<a name="overriding-package-views"></a>
#### 패키지 뷰-views 오버라이딩 하기

`loadViewsFrom` 메소드를 사용하면 라라벨은 실제로 애플리케이션의 `resources/views/vendor` 디렉토리와 사용자가 지정한 디렉토리의 두 위치를 뷰에 등록합니다. 따라서 `courier` 패키지를 예로 사용하여 라라벨은 먼저 개발자가 `resources/views/vendor/courier` 디렉토리에 뷰의 커스텀 버전을 배치했는지 확인합니다. 그런 다음 뷰가 커스텀되지 않은 경우 라라벨은 `loadViewsFrom`에 대한 호출에서 지정한 패키지 뷰 디렉토리를 검색합니다. 이렇게 하면 패키지 사용자가 패키지 뷰를 재정의할 수 있도록 쉽게 커스텀 할 수 있습니다.

<a name="publishing-views"></a>
#### 뷰-Views 퍼블리싱하기

여러분의 뷰를 애플리케이션의 `resources/views/vendor` 디렉토리로 퍼블리싱하려면 서비스 프로바이더의 `publishes` 메소드를 사용하면 됩니다. `publishes` 메소드는 패키지 뷰의 경로와 퍼블리싱 되길 바라는 위치를 나타내는 배열을 인자로 전달 받습니다.

    /**
     * Bootstrap the package services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadViewsFrom(__DIR__.'/../resources/views', 'courier');

        $this->publishes([
            __DIR__.'/../resources/views' => resource_path('views/vendor/courier'),
        ]);
    }

이제 라라벨의 `vendor:publish` 아티즌 명령어가 실행될 때 패키지의 뷰 파일들은 지정된 퍼블리싱 위치로 복사될 것입니다.

<a name="view-components"></a>
### 뷰 컴포넌트

패키지에 [view components](/docs/{{version}}/blade#components)가 포함되어 있으면 `loadViewComponentsAs` 메소드를 사용하여 라라벨에 로드하는 방법을 알려줄 수 있습니다. `loadViewComponentsAs` 메소드는 두 개의 인수, 즉 뷰 컴포넌트의 태그 접두어와 뷰 컴포넌트 클래스 이름의 배열을 입력받습니다. 예를 들어 패키지의 접두사가 `courier`이고 `Alert` 및 `Button` 뷰 컴포넌트가 있는 경우 서비스 프로바이더의 `boot` 메서드에 다음을 추가합니다.

    use Courier\Components\Alert;
    use Courier\Components\Button;

    /**
     * Bootstrap any package services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadViewComponentsAs('courier', [
            Alert::class,
            Button::class,
        ]);
    }

뷰 컴포넌트가 서비스 프로바이더에 등록되고나면, 뷰파일에서 다음과 같이 참조할 수 있습니다.

    <x-courier-alert />

    <x-courier-button />

<a name="anonymous-components"></a>
#### 익명 컴포넌트

패키지에 익명의 컴포넌트가 포함 된 경우 패키지의 "views"디렉토리 (`loadViewsFrom`에 지정한대로)의 `components` 디렉토리에 배치해야합니다. 그런 다음 컴포넌트 이름 앞에 패키지의 뷰 네임스페이스를 추가하여 렌더링 할 수 있습니다.

    <x-courier::alert />

<a name="commands"></a>
## 명령어

패키지의 아티즌 명령어를 라라벨에 등록하려면, `commands` 메소드를 사용하면 됩니다. 이 메소드는 명령어 클래스이름을 가진 배열을 인자로 받습니다. 명령어를 등록하고나면, [아티즌 CLI](/docs/{{version}}/artisan)를 통해서 실행할 수 있습니다.

    use Courier\Console\Commands\InstallCommand;
    use Courier\Console\Commands\NetworkCommand;

    /**
     * Bootstrap any package services.
     *
     * @return void
     */
    public function boot()
    {
        if ($this->app->runningInConsole()) {
            $this->commands([
                InstallCommand::class,
                NetworkCommand::class,
            ]);
        }
    }

<a name="public-assets"></a>
## Public Assets

패키지에는 JavaScript, CSS 및 이미지와 같은 자산-assets이 있을 수 있습니다. 이러한 자산을 애플리케이션의 `public` 디렉토리에 게시하려면 서비스 프로바이더의 `publishes` 메소드를 사용하세요. 이 예에서는 관련 자산 그룹을 쉽게 게시하는 데 사용할 수 있는 `public` 자산 그룹 태그도 추가합니다.

    /**
     * Bootstrap any package services.
     *
     * @return void
     */
    public function boot()
    {
        $this->publishes([
            __DIR__.'/../public' => public_path('vendor/courier'),
        ], 'public');
    }

이제 패키지 사용자가 `vendor:publish` 명령을 실행하면 자산이 지정된 게시 위치에 복사됩니다. 사용자는 일반적으로 패키지가 업데이트될 때마다 자산을 덮어써야 하므로 `--force` 플래그를 사용할 수 있습니다.

    php artisan vendor:publish --tag=public --force

<a name="publishing-file-groups"></a>
## 파일을 그룹단위로 게시하기

패키지 자산 및 리소스 그룹을 각각 별도로 게시할 수 있습니다. 예를 들어, 패키지 자산을 게시하지 않고도 사용자가 패키지 설정 파일을 게시할 수 있게 만들 수 있습니다. 패키지의 서비스 프로바이더로부터 `publishes` 메소드를 호출할 때 "태그"를 지정하여 이를 수행할 수 있습니다. 예를 들어 태그를 사용하여 패키지 서비스 프로바이더의 `boot` 메소드에서 `courier` 패키지(`courier-config` 및 `courier-migrations`)에 대한 두 개의 게시 그룹을 정의해 보겠습니다.

    /**
     * Bootstrap any package services.
     *
     * @return void
     */
    public function boot()
    {
        $this->publishes([
            __DIR__.'/../config/package.php' => config_path('package.php')
        ], 'courier-config');

        $this->publishes([
            __DIR__.'/../database/migrations/' => database_path('migrations')
        ], 'courier-migrations');
    }

이제 `vendor:publish` 명령어를 실행할 때 태그를 참조하도록 하여 그룹별로 분리되어 게시 할 수 있습니다.

    php artisan vendor:publish --tag=courier-config
