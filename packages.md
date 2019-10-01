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
- [명령어](#commands)
- [Public Assets](#public-assets)
- [Publishing File Groups](#publishing-file-groups)

<a name="introduction"></a>
## 시작하기

패키지를 개발하는 것은 라라벨에 기능을 추가하는 주요 방법입니다. 패키지는 예를 들어 날짜 라이브러리인 [Carbon](https://github.com/briannesbitt/Carbon)이나 BDD(행위 주도 개발) 테스트 프레임워크인 [Behat](https://github.com/Behat/Behat)과 같이 어느 것이든 될 수 있습니다.

다양한 종류의 패키지가 존재합니다. 다른 어떤 프레임워크와도 작동할 수 있는 독립적인 패키지도 있습니다. Carbon 과 Behat 은 이런 독립적으로 동작하는 패키지들의 예입니다. 이러한 패키지들을 라라벨과 사용하려면 `composer.json` 파일에 추가하면 됩니다.

반대로, 라라벨 프레임워크에만 사용할 수 있는 패키지들도 있습니다. 이러한 패키지들은 라라벨 애플리케이션의 기능을 사용하는 라우트, 컨트롤러, 뷰, 설정들을 가질 것입니다. 이 가이드는 라라벨에 특화된 패키지의 개발을 주로 설명합니다.

<a name="a-note-on-facades"></a>
### 파사드 사용의 주의사항

라라벨 애플리케이션을 작성하는 경우, contracts 또는 파사드의 어느 쪽을 사용해도 일반적으로는 테스트레벨에서 동일하게 문제가 없습니다. 그러나 패키지를 작성하는 경우, 패키지는 일반적으로 라라벨의 테스트 헬퍼 모두에게 접근 할 수 없습니다. 여러분이 일반적인 라라벨 애플리케이션에 들어 있는 것과같이 패키지 테스트를 작성하고자 한다면, [Orchestral Testbench](https://github.com/orchestral/testbench) 패키지를 사용할 수 있습니다.

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

[서비스 프로바이더](/docs/{{version}}/providers)는 라라벨과 패키지 사이의 접점이라고 할 수 있습니다. 하나의 서비스 프로바이더는 [서비스 컨테이너](/docs/{{version}}/container) 바인딩에 대응하고, 패키지의 뷰, 설정파일, 언어파일의 위치를 라라벨에 알려주는 역할을 합니다.

서비스 프로바이더는 `Illuminate\Support\ServiceProvider`를 상속 받고, 다음의 두개의 메소드: `register` 와 `boot` 메소드를 포함합니다. 베이스 `ServiceProvider` 클래스는 컴포저 패키지의 `illuminate/support` 에 위치하고 있으며, 여러분의 패키지에 필요한 의존성을 컴포저에 추가해야 합니다. 서비스 프로바이더의 구조와 목적에 대한 보다 자세한 사항은 [문서](/docs/{{version}}/providers)를 참고하십시오.

<a name="resources"></a>
## Resources

<a name="configuration"></a>
### 설정파일

일반적으로 여러분은 패키지의 설정 파일을 애플리케이션의 `config` 디렉토리에 퍼블리싱 하는 것이 필요할 것입니다. 이것은 여러분의 패키지의 사용자가 패키지 설정 파일의 기본 옵션들을 손쉽게 수정할 수 있도록 해줍니다. 설정 파일들을 퍼블리싱 하도록 하기 위해서는 서비스 프로바이더의 `boot` 메소드에서 `publishes` 메소드를 사용하면 됩니다.

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        $this->publishes([
            __DIR__.'/path/to/config/courier.php' => config_path('courier.php'),
        ]);
    }

이제, 라라벨의 `vendor:publish` 명령어가 실행될 때 파일들이 지정된 위치로 복사될 것입니다. 패키지의 설정 파일들이 퍼블리싱 되고 나면, 이 설정 값들은 다른 설정 파일들과 같이 엑세스 할 수 있습니다.

    $value = config('courier.option');

> {note} 설정 파일에서 클로저를 정의하면 안됩니다. `config:cache` 아티즌 명령어가 실행될 때 설정들이 정상적으로 serialize 되지 못하기 때문입니다.

#### 패키지 기본 설정

여러분은 또한 패키지 설정 파일이 애플리케이션의 퍼블리싱된 설정 파일에 합쳐지도록 할 수도 있습니다. 이렇게 하면 여러분의 사용자가 퍼블리싱된 설정 파일의 복사본에서 실제로 오버라이딩 하기를 원하는 옵션만 정의하는 것 가능하게 합니다. 설정 파일을 합치려면 서비스 프로바이더의 `register` 메소드안에서 `mergeConfigFrom` 메소드를 사용하면 됩니다.

    /**
     * Register bindings in the container.
     *
     * @return void
     */
    public function register()
    {
        $this->mergeConfigFrom(
            __DIR__.'/path/to/config/courier.php', 'courier'
        );
    }

> {note} 이 메소드는 설정 배열의 첫번째 레벨만을 병합합니다. 만약 사용자가 부분적으로 다차원 배열로 된 설정 배열을 정의한다면, 손실된 옵션은 병합되지 않습니다.

<a name="routes"></a>
### 라우트

패키지가 라우트를 포함하고 있다면, `loadRoutesForm` 메소드를 사용하여 이를 로딩해야 합니다. 이 메소드는 애플리케이션의 라우트가 캐싱되어 있는지를 자동으로 확인하여, 라우트가 이미 캐싱되어 있는 경우에는 라우트를 로딩하지 않습니다.

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadRoutesFrom(__DIR__.'/routes.php');
    }

<a name="migrations"></a>
### 마이그레이션 파일들

패키지가 [데이터베이스 마이그레이션 파일들](/docs/{{version}}/migrations)을 가지고 있다면, 라라벨에게 이를 어떻게 로딩할 수 있도록 알려주는데 `loadMigrationsFrom` 메소드를 사용할 수 있습니다. `loadMigrationsFrom` 메소드는 여러분의 패키지에서 마이그레이션이 있는 위치를 유일한 인자로 전달받습니다.

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadMigrationsFrom(__DIR__.'/path/to/migrations');
    }

패키지의 마이그레이션 파일들이 등록되고 나면, `php artisan migrate` 명령어를 실행할 때 자동으로 이 파일들이 구동될 것입니다. 이 파일들을 애플리케이션의 메인 `database/migrations` 디렉토리에 복사할 필요가 없습니다.

<a name="translations"></a>
### 언어 파일

패키지가 [언어 파일](/docs/{{version}}/localization)을 가지고 있다면, `loadTranslationsFrom` 메소드를 사용하여 라라벨이 이를 로드할 수 있게 할 수 있습니다. 예를 들어 패키지 이름이 `courier` 라면, 다음처럼 서비스 프로바이더의 `boot` 메소드에 다음 라인을 추가해야 합니다.

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadTranslationsFrom(__DIR__.'/path/to/translations', 'courier');
    }

패키지의 언어 파일들은 `package::file.line` 문법을 사용하여 편리하게 참조됩니다. 따라서 `courier` 패키지의 `message`파일에서 `welcome` 라인을 다음처럼 로드 할 수 있습니다.

    echo trans('courier::messages.welcome');

#### 언어 파일 퍼블리싱하기

패키지의 언어파일을 애플리케이션의 `resources/lang/vendor` 디렉토리로 퍼블리싱하려면 서비스 프로바이더의 `publishes` 메소드를 사용하면 됩니다. `publishes` 메소드는 패키지 경로와 퍼블리싱 되기를 바라는 위치를 나타내는 배열을 인자로 전달 받습니다. 예를 들어 `courier` 패키지의 언어 파일을 퍼블리싱 하려면, 다음과 같이 할 수 있습니다.

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadTranslationsFrom(__DIR__.'/path/to/translations', 'courier');

        $this->publishes([
            __DIR__.'/path/to/translations' => resource_path('lang/vendor/courier'),
        ]);
    }

이제 라라벨의 `vendor:publish` 아티즌 명령어가 실행될 때 패키지의 언어 파일들은 지정된 퍼블리싱 위치로 복사될 것입니다.

<a name="views"></a>
### 뷰-views

라라벨에 패키지의 [뷰-views](/docs/{{version}}/views)를 등록하기 위해서는 뷰 파일이 어디에 위치하고 있는지 라라벨에 알려줄 필요가 있습니다. 이를 위해서는 서비스 프로바이더의 `loadViewsForm` 메소드를 사용하면 됩니다. `loadViewsFrom` 메소드는 두개의 인자를 받아 들이는데: 뷰 템플릿의 패스와, 패키지의 이름입니다. 예를 들어 패키지 이름이 `courier`라면, 다음의 서비스 프로바이더 `boot` 메소드 처럼 추가하면 됩니다.

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadViewsFrom(__DIR__.'/path/to/views', 'courier');
    }

패키지의 뷰는 편리하게 `package::view` 문법을 사용하여 참조됩니다. 따라서 서비스 프로바이더에서 뷰의 경로가 등록된 다음에, `courier` 패키지의 `admin` 뷰를 다음처럼 로드 할 수 있습니다.

    Route::get('admin', function () {
        return view('courier::admin');
    });

#### 패키지 뷰-views 오버라이딩 하기

`loadViewsFrom` 메소드를 사용할 때, 라라벨에서는 실질적으로 뷰파일을 로드하기 위한 두개의 경로를 등록합니다. 애플리케이션의 `resources/views/vendor` 디렉토리와 여러분이 지정하는 디렉토리 입니다. 따라서 `courier` 예제에서, 라라벨은 먼저 `resources/views/vendor/courier` 에서 개발자에 의해서 제공되는 뷰가 있는지 확인할 것입니다. 그결과 커스텀 뷰가 설정되어 있지 않다면, 라라벨은 `loadViewsFrom` 메소드를 통해서 지정된 패키지 뷰 디렉토리를 확인할 것입니다. 이러한 방법은 패키지 사용자가 여러분의 패키지 뷰를 수정하거나, 재정의 하기 쉽게 해줍니다.

#### 뷰-Views 퍼블리싱하기

여러분의 뷰를 애플리케이션의 `resources/views/vendor` 디렉토리로 퍼블리싱하려면 서비스 프로바이더의 `publishes` 메소드를 사용하면 됩니다. `publishes` 메소드는 패키지 뷰의 경로와 퍼블리싱 되길 바라는 위치를 나타내는 배열을 인자로 전달 받습니다.

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadViewsFrom(__DIR__.'/path/to/views', 'courier');

        $this->publishes([
            __DIR__.'/path/to/views' => resource_path('views/vendor/courier'),
        ]);
    }

이제 라라벨의 `vendor:publish` 아티즌 명령어가 실행될 때 패키지의 뷰 파일들은 지정된 퍼블리싱 위치로 복사될 것입니다.

<a name="commands"></a>
## 명령어

패키지의 아티즌 명령어를 라라벨에 등록하려면, `commands` 메소드를 사용하면 됩니다. 이 메소드는 명령어 클래스이름을 가진 배열을 인자로 받습니다. 명령어를 등록하고나면, [아티즌 CLI](/docs/{{version}}/artisan)를 통해서 실행할 수 있습니다.

    /**
     * Bootstrap the application services.
     *
     * @return void
     */
    public function boot()
    {
        if ($this->app->runningInConsole()) {
            $this->commands([
                FooCommand::class,
                BarCommand::class,
            ]);
        }
    }

<a name="public-assets"></a>
## Public Assets

여러분의 패키지가 JavaScript, CSS 그리고 이미지 파일들 처럼 asset 파일들을 가지고 있을 수 있습니다. 이 파일들을 애플리케이션의 `public` 디렉토리로 퍼블리싱 하기 위해서는 서비스 프로바이더의 `publishes` 메소드를 사용하면 됩니다. 다음 예제에서는 연관된 asset 의 그룹을 퍼블리싱하는데 사용되는 "public" asset 그룹 태그를 추가로 지정하고 있습니다.

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        $this->publishes([
            __DIR__.'/path/to/assets' => public_path('vendor/courier'),
        ], 'public');
    }

이제, `vendor:publish` 명령어가 실행될 때, asset 들은 지정된 퍼블리싱 위치로 복사됩니다. 일반적으로 패키지가 업데이트 될 때에는 asset 파일들을 덮어써야 하기 때문에 이 경우 `--force` 플래그를 사용하면 됩니다.

    php artisan vendor:publish --tag=public --force

<a name="publishing-file-groups"></a>
## 파일을 그룹단위로 퍼블리싱하기

패키지의 asset들과 resources 파일들을 그룹별로 분할하여 퍼블리싱 할 수도 있습니다. 예를 들어, asset 파일들을 퍼블리싱하지 않고, 설정 파일들을 퍼블리싱하는 것이 가능하도록 하고자 할 수도 있습니다. 이렇게 하려면 패키지의 서비스 프로바이더에서 `publishes` 메소드가 호출 될 때 "태그를 지정"을 하여 할 수 있습니다. 예를 들어 패키지의 서비스 프로바이더의 `boot` 메소드에서 두개의 퍼블리싱 그룹을 정의하는데 태그를 사용해 보겠습니다.

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        $this->publishes([
            __DIR__.'/../config/package.php' => config_path('package.php')
        ], 'config');

        $this->publishes([
            __DIR__.'/../database/migrations/' => database_path('migrations')
        ], 'migrations');
    }

이제 `vendor:publish` 명령어를 실행할 때 태그를 참조하도록 하여 그룹별로 분리되어 퍼블리싱 할 수 있습니다.

    php artisan vendor:publish --tag=config
