# Package Development
# 패키지 개발

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Service Providers](#service-providers)
- [서비스 프로바이더](#service-providers)
- [Routing](#routing)
- [라우팅](#routing)
- [Resources](#resources)
- [Resources](#resources)
    - [Views](#views)
    - [뷰 파일들](#views)
    - [Translations](#translations)
    - [언어 파일](#translations)
    - [Configuration](#configuration)
    - [설정파일](#configuration)
- [Public Assets](#public-assets)
- [Public Assets](#public-assets)
- [Publishing File Groups](#publishing-file-groups)
- [Publishing File Groups](#publishing-file-groups)

<a name="introduction"></a>
## Introduction
## 시작하기

Packages are the primary way of adding functionality to Laravel. Packages might be anything from a great way to work with dates like [Carbon](https://github.com/briannesbitt/Carbon), or an entire BDD testing framework like [Behat](https://github.com/Behat/Behat).

패키지를 개발하는 것은 라라벨에 기능을 추가하는 주요한 방법입니다. 패키지는 예를 들어 날짜 라이브러리인 [Carbon](https://github.com/briannesbitt/Carbon)이나 BDD(행위 주도 개발) 테스트 프레임워크인 [Behat](https://github.com/Behat/Behat)과 같이 어느 것이든 될 수 있습니다. 

Of course, there are different types of packages. Some packages are stand-alone, meaning they work with any framework, not just Laravel. Both Carbon and Behat are examples of stand-alone packages. Any of these packages may be used with Laravel by simply requesting them in your `composer.json` file.

물론 다양한 종류의 패키지가 존재합니다. 라라벨 뿐만 아니라 다른 어떤 프레임워크와도 작동할 수 있는 독립적인 패키지도 있습니다. Carbon 과 Behat 은 이런 독립적으로 동작하는 패키지들의 예입니다. 이러한 패키지들을 라라벨과 사용하려면 `composer.json`파일에 추가하면 됩니다. 

On the other hand, other packages are specifically intended for use with Laravel. These packages may have routes, controllers, views, and configuration specifically intended to enhance a Laravel application. This guide primarily covers the development of those packages that are Laravel specific.

반대로, 라라벨 프레임워크에만 사용할 수 있는 패키지들도 있습니다. 이러한 패키지들은 라라벨 응용 프로그램의 기능을 사용하는 라우트, 컨트롤러, 뷰, 설정들을 가질 것입니다. 이 가이드는 라라벨에 특화된 패키지의 개발을 주로 설명합니다.

<a name="service-providers"></a>
## Service Providers
## 서비스 프로바이더

[Service providers](/docs/{{version}}/providers) are the connection points between your package and Laravel. A service provider is responsible for binding things into Laravel's [service container](/docs/{{version}}/container) and informing Laravel where to load package resources such as views, configuration, and localization files.

[서비스 프로바이더](/docs/{{version}}/providers)는 라라벨과 패키지 사이의 접점이라고 할 수 있습니다. 하나의 서비스 프로바이더는 [서비스 컨테이너](/docs/{{version}}/container) 바인딩에 대응하고, 패키지의 뷰, 설정파일, 언어파일의 위치를 라라벨에 알려주는 역할을 합니다. 

A service provider extends the `Illuminate\Support\ServiceProvider` class and contains two methods: `register` and `boot`. The base `ServiceProvider` class is located in the `illuminate/support` Composer package, which you should add to your own package's dependencies.

서비스 프로바이더는 `Illuminate\Support\ServiceProvider`를 상속 받고, 다음의 두개의 메소드: `register` 와 `boot` 메소드를 포함합니다. 베이스 `ServiceProvider` 클래스는 컴포저 패키지의 `illuminate/support` 에 위치하고 있으며, 여러분의 패키지에 필요한 의존성을 컴포저에 추가해야 합니다. 

To learn more about the structure and purpose of service providers, check out [their documentation](/docs/{{version}}/providers).

서비스 프로바이더의 구조와 목적에 대한 보다 자세한 사항은 [문서](/docs/{{version}}/providers)를 참고하십시오. 

<a name="routing"></a>
## Routing

To define routes for your package, simply `require` the routes file from within your package service provider's `boot` method. From within your routes file, you may use the `Route` facade to [register routes](/docs/{{version}}/routing) just as you would within a typical Laravel application:

패키지에서 사용할 라우트를 정의하기 위해서는 패키지의 서비스 프로바이더의 `boot` 메소드 안에서 간단하게 라우트 파일을 `require` 하면 됩니다. 여러분이 지정한 라우트 파일 안에서는 라라벨의 일반적인 애플리케이션에서 사용된 것과 같이 `Route` 파사드를 통해서 [라우트 등록](/docs/{{version}}/routing)을 할 수 있습니다. 

    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        if (! $this->app->routesAreCached()) {
            require __DIR__.'/../../routes.php';
        }
    }

<a name="resources"></a>
## Resources
## Resources

<a name="views"></a>
### Views
### 뷰-Views

To register your package's [views](/docs/{{version}}/views) with Laravel, you need to tell Laravel where the views are located. You may do this using the service provider's `loadViewsFrom` method. The `loadViewsFrom` method accepts two arguments: the path to your view templates and your package's name. For example, if your package name is "courier", add the following to your service provider's `boot` method:

라라벨에 패키지의 [뷰-views](/docs/{{version}}/views)를 등록하기 위해서는 뷰 파일이 어디에 위치하고 있는지 라라벨에 알려줄 필요가 있습니다. 이를 위해서는 서비스 프로바이더의 `loadViewsForm` 메소드를 사용하면 됩니다. `loadViewsFrom` 메소드는 두개의 인자를 받아 들이는데: 뷰 템플릿의 패스와, 패키지의 이름입니다. 예를 들어 패키지 이름이 "courier"라면, 다음의 서비스 프로바이더 `boot` 메소드 처럼 추가하면 됩니다. 

Your package's internal structure is entirely up to you; however, typically each package will contain one or more [service providers](/docs/{{version}}/providers). The service provider contains any [service container](/docs/{{version}}/container) bindings, as well as instructions as to where package configuration, views, and translation files are located.

여러분이 개발하고자 하는 패키지의 내부 구조는 전적으로 여러분의 마음대로 구성할 수 있습니다. 다만, 일반적으로 패키지들은 하나 이상의 [서비스 프로바이더](/docs/{{version}}/providers)를 가지고 있습니다. 서비스 프로바이더는 패키지의 설정, 뷰, 언어 파일의 위치가 어디인지 알려주며, [서비스 컨테이너](/docs/{{version}}/container) 바인딩을 포함하고 있습니다. 

    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadViewsFrom(__DIR__.'/path/to/views', 'courier');
    }

Package views are referenced using a double-colon `package::view` syntax. So, you may load the `admin` view from the `courier` package like so:

패키지의 뷰는 더블콜론을 사용하는 `package::view` 문법을 통해서 참조됩니다. 따라서 `courier` 패키지의 `admin` 뷰를 다음처럼 로드 할 수 있습니다: 

    Route::get('admin', function () {
        return view('courier::admin');
    });

#### Overriding Package Views
#### 패키지 뷰-views 오버라이딩 하기

When you use the `loadViewsFrom` method, Laravel actually registers **two** locations for your views: one in the application's `resources/views/vendor` directory and one in the directory you specify. So, using our `courier` example: when requesting a package view, Laravel will first check if a custom version of the view has been provided by the developer in `resources/views/vendor/courier`. Then, if the view has not been customized, Laravel will search the package view directory you specified in your call to `loadViewsFrom`. This makes it easy for end-users to customize / override your package's views.

`loadViewsFrom` 메소드를 사용할 때, 라라벨에서는 실질적으로 뷰파일을 로드하기 위한 **두개의** 경로를 등록합니다: 하나는 애플리케이션의 `resources/views/vendor` 디렉토리 이고 다른 하나는 지정된 디렉토리 입니다. 따라서 `courier` 예제에서, 패키지 뷰를 요청하는 동안 라라벨은 먼저 `resources/views/vendor/courier` 에서 개발자에 의해서 제공되는 뷰가 있는지 확인할 것입니다. 그결과 커스텀 뷰가 설정되어 있지 않다면, 라라벨은 `loadViewsFrom` 메소드를 통해서 지정된 패키지 뷰 디렉토리를 확인할 것입니다. 이러한 방법은 사용자가 여러분의 패키지 뷰를 수정하거나, 재정의 하기 쉽게 해줍니다. 

#### Publishing Views
#### 뷰-Views 퍼블리싱하기

If you would like to make your views available for publishing to the application's `resources/views/vendor` directory, you may use the service provider's `publishes` method. The `publishes` method accepts an array of package view paths and their corresponding publish locations.

여러분의 뷰를 애플리케이션의 `resources/views/vendor` 디렉토리로 퍼블리싱하려면 서비스 프로바이더의 `publishes` 메소드를 사용하면 됩니다. `publishes` 메소드는 패키지 뷰 경로와 옮겨질 위치를 나타내는 배열을 인자로 전달 받습니다. 

    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadViewsFrom(__DIR__.'/path/to/views', 'courier');

        $this->publishes([
            __DIR__.'/path/to/views' => base_path('resources/views/vendor/courier'),
        ]);
    }

Now, when users of your package execute Laravel's `vendor:publish` Artisan command, your package's views will be copied to the specified location.

이제 라라벨의 `vendor:publish` 아티즌 명령어가 실행될 때 패키지의 뷰 파일들은 지정된 위치로 복사될 것입니다. 

<a name="translations"></a>
### Translations
### 언어 파일

If your package contains [translation files](/docs/{{version}}/localization), you may use the `loadTranslationsFrom` method to inform Laravel how to load them. For example, if your package is named "courier", you should add the following to your service provider's `boot` method:

패키지가 [언어 파일](/docs/{{version}}/localization)을 가지고 있다면, `loadTranslationsFrom` 메소드를 사용하여 라라벨이 이를 로드할 수 있게 할 수 있습니다. 예를 들어 패키지 이름이 "courier" 라면, 다음처럼 서비스 프로바이더의 `boot` 메소드에 다음 라인을 추가해야 합니다. 

    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadTranslationsFrom(__DIR__.'/path/to/translations', 'courier');
    }

Package translations are referenced using a double-colon `package::file.line` syntax. So, you may load the `courier` package's `welcome` line from the `messages` file like so:

패키지의 언어 파일들은 더블콜론을 사용하는 `package::file.line` 문법을 통해서 참조됩니다. 따라서 `courier` 패키지의 `message`파일에서 `welcome` 라인을 다음처럼 로드 할 수 있습니다: 

    echo trans('courier::messages.welcome');

#### Publishing Translations
#### 언어 파일 퍼블리싱하기

If you would like to publish your package's translations to the application's `resources/lang/vendor` directory, you may use the service provider's `publishes` method. The `publishes` method accepts an array of package paths and their corresponding publish locations. For example, to the publish the translation files for our example `courier` package:

패키지의 언어파일을 애플리케이션의 `resources/lang/vendor` 디렉토리로 퍼블리싱하려면 서비스 프로바이더의 `publishes` 메소드를 사용하면 됩니다. `publishes` 메소드는 패키지 경로와 옮겨질 위치를 나타내는 배열을 인자로 전달 받습니다. 예를 들어 `courier` 패키지의 언어 파일을 다음처럼 퍼블리싱 할 수 있습니다. 

    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadTranslationsFrom(__DIR__.'/path/to/translations', 'courier');

        $this->publishes([
            __DIR__.'/path/to/translations' => base_path('resources/lang/vendor/courier'),
        ]);
    }

Now, when users of your package execute Laravel's `vendor:publish` Artisan command, your package's translations will be published to the specified location.

이제 라라벨의 `vendor:publish` 아티즌 명령어가 실행될 때 패키지의 언어 파일들은 지정된 위치로 복사될 것입니다. 

<a name="configuration"></a>
### Configuration
### 설정파일

Typically, you will want to publish your package's configuration file to the application's own `config` directory. This will allow users of your package to easily override your default configuration options. To publish a configuration file, just use the `publishes` method from the `boot` method of your service provider:

일반적으로 여러분은 패키지의 설정 파일을 애플리케이션의 `config` 디렉토리에 퍼블리싱 하기를 원할 것입니다. 이것은 여러분의 패키지의 사용자가 패키지 설정 파일의 기본 옵션들을 손쉽게 수정할 수 있도록 해줍니다. 설정 파일들을 퍼블리싱 하기 위해서는 서비스 프로바이더의 `boot` 메소드에서 `publishes` 메소드를 사용하면 됩니다. 

    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        $this->publishes([
            __DIR__.'/path/to/config/courier.php' => config_path('courier.php'),
        ]);
    }

Now, when users of your package execute Laravel's `vendor:publish` command, your file will be copied to the specified location. Of course, once your configuration has been published, it can be accessed like any other configuration file:

Now, when users of your package execute Laravel's `vendor:publish` Artisan command, your package's translations will be published to the specified location.

이제 라라벨의 `vendor:publish` 명령어가 실행될 때 파일들이 지정된 위치로 복사될 것입니다. 패키지의 설정 파일들이 퍼블리싱 되고 나면, 다른 설정 파일들과 같이 엑세스 할 수 있습니다. 

    $value = config('courier.option');

#### Default Package Configuration
#### 패키지 기본 설정

You may also choose to merge your own package configuration file with the application's copy. This allows your users to include only the options they actually want to override in the published copy of the configuration. To merge the configurations, use the `mergeConfigFrom` method within your service provider's `register` method:

여러분은 또한 패키지 설정 파일이 애플리케이션의 설정 파일에 합쳐지도록 할 수도 있습니다. 이렇게 하면 퍼블리싱된 설정 파일의 복사에서 사용자가 실제로 재 정의 하기를 원하는 옵션만을 포함 하도록 할 수 있습니다. 설정 파일을 합치려면 서비스 프로바이더의 `register` 메소드안에서 `mergeConfigFrom` 메소드를 사용하면 됩니다:

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

<a name="public-assets"></a>
## Public Assets

Your packages may have assets such as JavaScript, CSS, and images. To publish these assets to the application's `public` directory, use the service provider's `publishes` method. In this example, we will also add a `public` asset group tag, which may be used to publish groups of related assets:

여러분의 패키지가 JavaScript, CSS 그리고 이미지 파일들 처럼 asset 파일들을 가지고 있을 수 있습니다. 이 파일들을 애플리케이션의 `public` 디렉토리로 퍼블리싱 하기 위해서는 서비스 프로바이더의 `publishes` 메소드를 사용하면 됩니다. 다음 예제에서는 연관된 asset 의 그룹을 퍼블리싱하는데 사용되는 "public" asset 그룹 태그를 추가로 지정하고 있습니다.

    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        $this->publishes([
            __DIR__.'/path/to/assets' => public_path('vendor/courier'),
        ], 'public');
    }

Now, when your package's users execute the `vendor:publish` command, your assets will be copied to the specified location. Since you typically will need to overwrite the assets every time the package is updated, you may use the `--force` flag:

이제, `vendor:publish` 명령어가 실행될 때, asset 들은 지정된 위치로 복사될것입니다. 일반적으로 패키지가 업데이트 될 때에는 asset 파일들을 덮어써야 하기 때문에 이 경우 `--force` 플래스를 사용하면 됩니다. 

    php artisan vendor:publish --tag=public --force

If you would like to make sure your public assets are always up-to-date, you can add this command to the `post-update-cmd` list in your `composer.json` file.

Public 의 asset 파일들을 항상 안전하게 업데이트하려면 이 명령어들을 `composer.json` 파일의 `post-update-cmd` 목록에 추가하는 것이 좋습니다. 

<a name="publishing-file-groups"></a>
## Publishing File Groups
## 파일을 그룹단위로 퍼블리싱하기

You may want to publish groups of package assets and resources separately. For instance, you might want your users to be able to publish your package's configuration files without being forced to publish your package's assets at the same time. You may do this by "tagging" them when calling the `publishes` method. For example, let's define two publish groups in the `boot` method of a package service provider:

패키지의 asset들과 resources 파일들을 그룹별로 분할하여 퍼블리싱 할 수도 있습니다. 예를 들어, asset 파일들을 퍼블리싱하지 않고, 동시에, 설정 파일들이 사용가능하도록 하고자 할 수도 있습니다. 이렇게 하려면 `publishes` 메소드가 호출 될 때 "태그 지정"을 하여 할 수 있습니다. 예를 들어 패키지의 서비스 프로바이더의 `boot` 메소드에서 두개의 퍼블리싱 그룹을 정의해 보겠습니다. 

    /**
     * Perform post-registration booting of services.
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

Now your users may publish these groups separately by referencing their tag name when using the `vendor:publish` Artisan command:

이제 `vendor:publish` 아티즌 명령어를 사용할 때 태그를 참조하도록 하여 그룹별로 분리되어 퍼블리싱 할 수 있습니다. 

    php artisan vendor:publish --provider="Vendor\Providers\PackageServiceProvider" --tag="config"
