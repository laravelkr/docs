# Package Development
# 패키지 개발

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [A Note On Facades](#a-note-on-facades)
    - [파사드 사용의 주의사항](#a-note-on-facades)
- [Package Discovery](#package-discovery)
- [패키지 Discovery](#package-discovery)
- [Service Providers](#service-providers)
- [서비스 프로바이더](#service-providers)
- [Resources](#resources)
- [Resources](#resources)
    - [Configuration](#configuration)
    - [설정파일](#configuration)
    - [Migrations](#migrations)
    - [마이그레이션 파일들](#migrations)
    - [Routes](#routes)
    - [라우트](#routes)
    - [Translations](#translations)
    - [언어 파일](#translations)
    - [Views](#views)
    - [뷰 파일들](#views)
- [Commands](#commands)
- [명령어](#commands)
- [Public Assets](#public-assets)
- [Public Assets](#public-assets)
- [Publishing File Groups](#publishing-file-groups)
- [Publishing File Groups](#publishing-file-groups)

<a name="introduction"></a>
## Introduction
## 시작하기

Packages are the primary way of adding functionality to Laravel. Packages might be anything from a great way to work with dates like [Carbon](https://github.com/briannesbitt/Carbon), or an entire BDD testing framework like [Behat](https://github.com/Behat/Behat).

패키지를 개발하는 것은 라라벨에 기능을 추가하는 주요 방법입니다. 패키지는 예를 들어 날짜 라이브러리인 [Carbon](https://github.com/briannesbitt/Carbon)이나 BDD(행위 주도 개발) 테스트 프레임워크인 [Behat](https://github.com/Behat/Behat)과 같이 어느 것이든 될 수 있습니다.

There are different types of packages. Some packages are stand-alone, meaning they work with any PHP framework. Carbon and Behat are examples of stand-alone packages. Any of these packages may be used with Laravel by requesting them in your `composer.json` file.

다양한 종류의 패키지가 존재합니다. 다른 어떤 프레임워크와도 작동할 수 있는 독립적인 패키지도 있습니다. Carbon 과 Behat 은 이런 독립적으로 동작하는 패키지들의 예입니다. 이러한 패키지들을 라라벨과 사용하려면 `composer.json` 파일에 추가하면 됩니다.

On the other hand, other packages are specifically intended for use with Laravel. These packages may have routes, controllers, views, and configuration specifically intended to enhance a Laravel application. This guide primarily covers the development of those packages that are Laravel specific.

반대로, 라라벨 프레임워크에만 사용할 수 있는 패키지들도 있습니다. 이러한 패키지들은 라라벨 애플리케이션의 기능을 사용하는 라우트, 컨트롤러, 뷰, 설정들을 가질 것입니다. 이 가이드는 라라벨에 특화된 패키지의 개발을 주로 설명합니다.

<a name="a-note-on-facades"></a>
### A Note On Facades
### 파사드 사용의 주의사항

When writing a Laravel application, it generally does not matter if you use contracts or facades since both provide essentially equal levels of testability. However, when writing packages, your package will not typically have access to all of Laravel's testing helpers. If you would like to be able to write your package tests as if they existed inside a typical Laravel application, you may use the [Orchestral Testbench](https://github.com/orchestral/testbench) package.

라라벨 애플리케이션을 작성하는 경우, contracts 또는 파사드의 어느 쪽을 사용해도 일반적으로는 테스트레벨에서 동일하게 문제가 없습니다. 그러나 패키지를 작성하는 경우, 패키지는 일반적으로 라라벨의 테스트 헬퍼 모두에게 접근 할 수 없습니다. 여러분이 일반적인 라라벨 애플리케이션에 들어 있는 것과같이 패키지 테스트를 작성하고자 한다면, [Orchestral Testbench](https://github.com/orchestral/testbench) 패키지를 사용할 수 있습니다.

<a name="package-discovery"></a>
## Package Discovery
## 패키지 Discovery

In a Laravel application's `config/app.php` configuration file, the `providers` option defines a list of service providers that should be loaded by Laravel. When someone installs your package, you will typically want your service provider to be included in this list. Instead of requiring users to manually add your service provider to the list, you may define the provider in the `extra` section of your package's `composer.json` file. In addition to service providers, you may also list any [facades](/docs/{{version}}/facades) you would like to be registered:

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

Once your package has been configured for discovery, Laravel will automatically register its service providers and facades when it is installed, creating a convenient installation experience for your package's users.

패키지 discovery 를 위한 설정이 되었다면, 라라벨은 패키지가 설치되었을 때 자동으로 서비스 프로바이더와 파사드를 등록하여 패키지 사용자에게 편리한 설치 경험을 제공해주게 됩니다.

### Opting Out Of Package Discovery
### 패키지 Discovery에서 제외시키기

If you are the consumer of a package and would like to disable package discovery for a package, you may list the package name in the `extra` section of your application's `composer.json` file:

패키지 사용자가 패키지 discovery 기능을 사용하지 않기를 원한다면, 애플리케이션의 `composer.json` 파일의 `extra` 섹션에 패키지 이름을 나열해놓으면 됩니다.

    "extra": {
        "laravel": {
            "dont-discover": [
                "barryvdh/laravel-debugbar"
            ]
        }
    },

You may disable package discovery for all packages using the `*` character inside of your application's `dont-discover` directive:

애플리케이션의 `dont-discover` 지시어안에서 `*` 문자열을 사용하여 모든 패키지 dicovery를 비활성화 할 수도 있습니다.

    "extra": {
        "laravel": {
            "dont-discover": [
                "*"
            ]
        }
    },

<a name="service-providers"></a>
## Service Providers
## 서비스 프로바이더

[Service providers](/docs/{{version}}/providers) are the connection points between your package and Laravel. A service provider is responsible for binding things into Laravel's [service container](/docs/{{version}}/container) and informing Laravel where to load package resources such as views, configuration, and localization files.

[서비스 프로바이더](/docs/{{version}}/providers)는 라라벨과 패키지 사이의 접점이라고 할 수 있습니다. 하나의 서비스 프로바이더는 [서비스 컨테이너](/docs/{{version}}/container) 바인딩에 대응하고, 패키지의 뷰, 설정파일, 언어파일의 위치를 라라벨에 알려주는 역할을 합니다.

A service provider extends the `Illuminate\Support\ServiceProvider` class and contains two methods: `register` and `boot`. The base `ServiceProvider` class is located in the `illuminate/support` Composer package, which you should add to your own package's dependencies. To learn more about the structure and purpose of service providers, check out [their documentation](/docs/{{version}}/providers).

서비스 프로바이더는 `Illuminate\Support\ServiceProvider`를 상속 받고, 다음의 두개의 메소드: `register` 와 `boot` 메소드를 포함합니다. 베이스 `ServiceProvider` 클래스는 컴포저 패키지의 `illuminate/support` 에 위치하고 있으며, 여러분의 패키지에 필요한 의존성을 컴포저에 추가해야 합니다. 서비스 프로바이더의 구조와 목적에 대한 보다 자세한 사항은 [문서](/docs/{{version}}/providers)를 참고하십시오.

<a name="resources"></a>
## Resources
## Resources

<a name="configuration"></a>
### Configuration
### 설정파일

Typically, you will need to publish your package's configuration file to the application's own `config` directory. This will allow users of your package to easily override your default configuration options. To allow your configuration files to be published, call the `publishes` method from the `boot` method of your service provider:

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

Now, when users of your package execute Laravel's `vendor:publish` command, your file will be copied to the specified publish location. Once your configuration has been published, its values may be accessed like any other configuration file:

이제, 라라벨의 `vendor:publish` 명령어가 실행될 때 파일들이 지정된 위치로 복사될 것입니다. 패키지의 설정 파일들이 퍼블리싱 되고 나면, 이 설정 값들은 다른 설정 파일들과 같이 엑세스 할 수 있습니다.

    $value = config('courier.option');

> {note} You should not define Closures in your configuration files. They can not be serialized correctly when users execute the `config:cache` Artisan command.

> {note} 설정 파일에서 클로저를 정의하면 안됩니다. `config:cache` 아티즌 명령어가 실행될 때 설정들이 정상적으로 serialize 되지 못하기 때문입니다.

#### Default Package Configuration
#### 패키지 기본 설정

You may also merge your own package configuration file with the application's published copy. This will allow your users to define only the options they actually want to override in the published copy of the configuration. To merge the configurations, use the `mergeConfigFrom` method within your service provider's `register` method:

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

> {note} This method only merges the first level of the configuration array. If your users partially define a multi-dimensional configuration array, the missing options will not be merged.

> {note} 이 메소드는 설정 배열의 첫번째 레벨만을 병합합니다. 만약 사용자가 부분적으로 다차원 배열로 된 설정 배열을 정의한다면, 손실된 옵션은 병합되지 않습니다.

<a name="routes"></a>
### Routes
### 라우트

If your package contains routes, you may load them using the `loadRoutesFrom` method. This method will automatically determine if the application's routes are cached and will not load your routes file if the routes have already been cached:

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
### Migrations
### 마이그레이션 파일들

If your package contains [database migrations](/docs/{{version}}/migrations), you may use the `loadMigrationsFrom` method to inform Laravel how to load them. The `loadMigrationsFrom` method accepts the path to your package's migrations as its only argument:

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

Once your package's migrations have been registered, they will automatically be run when the `php artisan migrate` command is executed. You do not need to export them to the application's main `database/migrations` directory.

패키지의 마이그레이션 파일들이 등록되고 나면, `php artisan migrate` 명령어를 실행할 때 자동으로 이 파일들이 구동될 것입니다. 이 파일들을 애플리케이션의 메인 `database/migrations` 디렉토리에 복사할 필요가 없습니다.

<a name="translations"></a>
### Translations
### 언어 파일

If your package contains [translation files](/docs/{{version}}/localization), you may use the `loadTranslationsFrom` method to inform Laravel how to load them. For example, if your package is named `courier`, you should add the following to your service provider's `boot` method:

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

Package translations are referenced using the `package::file.line` syntax convention. So, you may load the `courier` package's `welcome` line from the `messages` file like so:

패키지의 언어 파일들은 `package::file.line` 문법을 사용하여 편리하게 참조됩니다. 따라서 `courier` 패키지의 `message`파일에서 `welcome` 라인을 다음처럼 로드 할 수 있습니다.

    echo trans('courier::messages.welcome');

#### Publishing Translations
#### 언어 파일 퍼블리싱하기

If you would like to publish your package's translations to the application's `resources/lang/vendor` directory, you may use the service provider's `publishes` method. The `publishes` method accepts an array of package paths and their desired publish locations. For example, to publish the translation files for the `courier` package, you may do the following:

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

Now, when users of your package execute Laravel's `vendor:publish` Artisan command, your package's translations will be published to the specified publish location.

이제 라라벨의 `vendor:publish` 아티즌 명령어가 실행될 때 패키지의 언어 파일들은 지정된 퍼블리싱 위치로 복사될 것입니다.

<a name="views"></a>
### Views
### 뷰-views

To register your package's [views](/docs/{{version}}/views) with Laravel, you need to tell Laravel where the views are located. You may do this using the service provider's `loadViewsFrom` method. The `loadViewsFrom` method accepts two arguments: the path to your view templates and your package's name. For example, if your package's name is `courier`, you would add the following to your service provider's `boot` method:

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

Package views are referenced using the `package::view` syntax convention. So, once your view path is registered in a service provider, you may load the `admin` view from the `courier` package like so:

패키지의 뷰는 편리하게 `package::view` 문법을 사용하여 참조됩니다. 따라서 서비스 프로바이더에서 뷰의 경로가 등록된 다음에, `courier` 패키지의 `admin` 뷰를 다음처럼 로드 할 수 있습니다.

    Route::get('admin', function () {
        return view('courier::admin');
    });

#### Overriding Package Views
#### 패키지 뷰-views 오버라이딩 하기

When you use the `loadViewsFrom` method, Laravel actually registers two locations for your views: the application's `resources/views/vendor` directory and the directory you specify. So, using the `courier` example, Laravel will first check if a custom version of the view has been provided by the developer in `resources/views/vendor/courier`. Then, if the view has not been customized, Laravel will search the package view directory you specified in your call to `loadViewsFrom`. This makes it easy for package users to customize / override your package's views.

`loadViewsFrom` 메소드를 사용할 때, 라라벨에서는 실질적으로 뷰파일을 로드하기 위한 두개의 경로를 등록합니다. 애플리케이션의 `resources/views/vendor` 디렉토리와 여러분이 지정하는 디렉토리 입니다. 따라서 `courier` 예제에서, 라라벨은 먼저 `resources/views/vendor/courier` 에서 개발자에 의해서 제공되는 뷰가 있는지 확인할 것입니다. 그결과 커스텀 뷰가 설정되어 있지 않다면, 라라벨은 `loadViewsFrom` 메소드를 통해서 지정된 패키지 뷰 디렉토리를 확인할 것입니다. 이러한 방법은 패키지 사용자가 여러분의 패키지 뷰를 수정하거나, 재정의 하기 쉽게 해줍니다.

#### Publishing Views
#### 뷰-Views 퍼블리싱하기

If you would like to make your views available for publishing to the application's `resources/views/vendor` directory, you may use the service provider's `publishes` method. The `publishes` method accepts an array of package view paths and their desired publish locations:

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

Now, when users of your package execute Laravel's `vendor:publish` Artisan command, your package's views will be copied to the specified publish location.

이제 라라벨의 `vendor:publish` 아티즌 명령어가 실행될 때 패키지의 뷰 파일들은 지정된 퍼블리싱 위치로 복사될 것입니다.

<a name="commands"></a>
## Commands
## 명령어

To register your package's Artisan commands with Laravel, you may use the `commands` method. This method expects an array of command class names. Once the commands have been registered, you may execute them using the [Artisan CLI](/docs/{{version}}/artisan):

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
## Public Assets

Your package may have assets such as JavaScript, CSS, and images. To publish these assets to the application's `public` directory, use the service provider's `publishes` method. In this example, we will also add a `public` asset group tag, which may be used to publish groups of related assets:

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

Now, when your package's users execute the `vendor:publish` command, your assets will be copied to the specified publish location. Since you will typically need to overwrite the assets every time the package is updated, you may use the `--force` flag:

이제, `vendor:publish` 명령어가 실행될 때, asset 들은 지정된 퍼블리싱 위치로 복사됩니다. 일반적으로 패키지가 업데이트 될 때에는 asset 파일들을 덮어써야 하기 때문에 이 경우 `--force` 플래그를 사용하면 됩니다.

    php artisan vendor:publish --tag=public --force

<a name="publishing-file-groups"></a>
## Publishing File Groups
## 파일을 그룹단위로 퍼블리싱하기

You may want to publish groups of package assets and resources separately. For instance, you might want to allow your users to publish your package's configuration files without being forced to publish your package's assets. You may do this by "tagging" them when calling the `publishes` method from a package's service provider. For example, let's use tags to define two publish groups in the `boot` method of a package service provider:

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

Now your users may publish these groups separately by referencing their tag when executing the `vendor:publish` command:

이제 `vendor:publish` 명령어를 실행할 때 태그를 참조하도록 하여 그룹별로 분리되어 퍼블리싱 할 수 있습니다.

    php artisan vendor:publish --tag=config
