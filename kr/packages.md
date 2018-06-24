# Package Development
# 패키지 개발

- [Introduction 소개](#introduction)
- [Views 뷰](#views)
- [Translations 언어파일](#translations)
- [Configuration 설정](#configuration)
- [Public Assets ](#public-assets)
- [Publishing File Groups ](#publishing-file-groups)
- [Routing 라우팅](#routing)

<a name="introduction"></a>
## Introduction
## 소개

Packages are the primary way of adding functionality to Laravel. 패키지를 개발하는 것은 라라벨에 기능을 추가하는 주요한 방법입니다. Packages might be anything from a great way to work with dates like [Carbon](https://github.com/briannesbitt/Carbon), or an entire BDD testing framework like [Behat](https://github.com/Behat/Behat). 패키지는 예를 들어 날짜 라이브러리인 [Carbon](https://github.com/briannesbitt/Carbon)이나 BDD(행위 주도 개발) 테스트 프레임워크인 [Behat](https://github.com/Behat/Behat)과 같이 어느 것이든 될 수 있습니다. 

Of course, there are different types of packages. 물론 다양한 종류의 패키지가 존재합니다. Some packages are stand-alone, meaning they work with any framework, not just Laravel. 라라벨 뿐만 아니라 다른 어떤 프레임워크와도 작동할 수 있는 독립적인 패키지도 있습니다. Both Carbon and Behat are examples of stand-alone packages. Carbon 과 Behat 은 이런 독립적으로 동작하는 패키지들의 예입니다. Any of these packages may be used with Laravel by simply requesting them in your `composer.json` file. 이러한 패키지들을 라라벨과 사용하려면  `composer.json`파일에 추가하면 됩니다. 

On the other hand, other packages are specifically intended for use with Laravel. These packages may have routes, controllers, views, and configuration specifically intended to enhance a Laravel application. This guide primarily covers the development of those that are Laravel specific. 반대로, 라라벨 프레임워크에만 사용할 수 있는 패키지들도 있습니다. 이러한 패키지들은 라라벨 응용 프로그램의 기능을 사용하는 라우트, 컨트롤러, 뷰, 설정들을 가질 것입니다. 이 가이드는 라라벨에 특화된 패키지의 개발을 주로 설명합니다.

All Laravel packages are distributed via [Packagist](http://packagist.org) and [Composer](http://getcomposer.org), so learning about these wonderful PHP package distribution tools is essential. 모든 라라벨 패키지는 [Packagist](http://packagist.org) 와 [Composer](http://getcomposer.org)를 통해서 배포되기 때문에 PHP 패키지 배포 도구에 대해 아고 있는 것이 중요합니다. 

<a name="views"></a>
## Views
## 뷰

Your package's internal structure is entirely up to you; however, typically each package will contain one or more [service providers](/docs/{{version}}/providers). The service provider contains any [service container](/docs/{{version}}/container) bindings, as well as instructions as to where package configuration, views, and translation files are located.
패키지의 내부 구조는 여러분이 구성하기 나름입니다. 그러나 일반적으로 각 패키지는 하나 이상의 [서비스 프로바이더](/docs/{{version}}/providers)를 가지고 있습니다. 서비스 프로바이더는 [service container](/docs/{{version}}/container) 바인딩을 설정하고, 동시에 패키지의 설정, 뷰, 언어 파일의 위치를 지정합니다.

### Views
### 뷰 파일

Package views are typically referenced using a double-colon "namespace" syntax:
일반적으로 패키지의 뷰는 더블 콜론을 사용하여 ”네임 스페이스”를 지정합니다.

	return view('package::view.name');

All you need to do is tell Laravel where the views for a given namespace are located. For example, if your package is named "courier", you might add the following to your service provider's `boot` method: 여러분이 해야할 것은 주어진 네임스페이스에 해당하는 뷰 파일들이 어디에 있는지 라라벨에 알려주는 것입니다.  예를들어 여러분의 패키지 이름이 “courier” 라고 할 때 서비스 프로바이더의 `boot` 메소드에 다음과 같이 뷰 파일의 위치를 설정할 수 있습니다. 

	public function boot()
	{
		$this->loadViewsFrom(__DIR__.'/path/to/views', 'courier');
	}

Now you may load your package views using the following syntax:
이제 패키지에서 다음처럼 뷰파일을 로드 할 수 있습니다. 

	return view('courier::view.name');

When you use the `loadViewsFrom` method, Laravel actually registers **two** locations for your views: one in the application's `resources/views/vendor` directory and one in the directory you specify. 여러분이 `loadViewsFrom` 메소드를 사용할 때, 라라벨은 실제로 뷰을 위한 **두개의** 위치를 등록합니다. 하나는 애플리케이션의 `resources/views/vendor` 디렉토리 이고, 다른 하나는 여러분이 지정한 디렉토리 입니다. So, using our `courier` example: when requesting a package view, Laravel will first check if a custom version of the view has been provided by the developer in `resources/views/vendor/courier`. 앞의 `courier` 예제를 생각해 보면, 패키지의 뷰가 요청될 때 라라벨은 먼저 `resources/views/vendor/courier` 디렉토리에 파일이 준비되어 있는지 확인합니다. Then, if the view has not been customized, Laravel will search the package view directory you specified in your call to `loadViewsFrom`. 이때 뷰를 확인하지 못하면 여러분이 `loadViewsFrom`를 통해서 지정한 디렉토리에서 패키지 뷰를 찾게 될 것입니다. This makes it easy for end-users to customize / override your package's views. 이러한 방법은 사용자가 여러분의 패키지 뷰를 수정하거나, 재정의 하기 쉽게 해줍니다. 

#### Publishing Views
#### 뷰 퍼블리싱

To publish your package's views to the `resources/views/vendor` directory, you should use the `publishes` method from the `boot` method of your service provider:
패키지의 뷰를 `resources/views/vendor` 디렉토리에 퍼블리싱하려면 서비스 프로바이더의 `boot` 메소드에서 `publishes` 메소드를 사용하면 됩니다. 

	public function boot()
	{
		$this->loadViewsFrom(__DIR__.'/path/to/views', 'courier');

		$this->publishes([
			__DIR__.'/path/to/views' => base_path('resources/views/vendor/courier'),
		]);
	}

Now, when users of your package execute Laravel's `vendor:publish` command, your views directory will be copied to the specified location. 이제 패키지를 이용하는 사용자가 라라벨의 `vendor:public` 명령어를 실행하면 패키지 뷰 디렉토리가 지정된 위치로 복사 될 것입니다. 

If you would like to overwrite existing files, use the `--force` switch:
파일이 있을 때 덮어 씌우려면 명령어에 `--force` 옵션을 주면 됩니다. 

	php artisan vendor:publish --force

> **Note 참고 :** You may use the `publishes` method to publish **any** type of file to any location you wish. `publishes` 메소드는 **어떤** 유형의 파일이라도 원하는 위치로 파일을 퍼블리싱 할 수 있습니다.

<a name="translations"></a>
## Translations
## 언어파일

Package translation files are typically referenced using a double-colon syntax:
일반적으로 패키지의 언어파일은 더블 콜론을 사용하여 ”네임 스페이스”를 지정합니다.

	return trans('package::file.line');

All you need to do is tell Laravel where the translations for a given namespace are located. For example, if your package is named "courier", you might add the following to your service provider's `boot` method:  여러분이 해야할 것은 주어진 네임스페이스에 해당하는 언어 파일들이 어디에 있는지 라라벨에 알려주는 것입니다.  예를들어 여러분의 패키지 이름이 “courier” 라고 할 때 서비스 프로바이더의 `boot` 메소드에 다음과 같이 언어 파일의 위치를 설정할 수 있습니다. 

	public function boot()
	{
		$this->loadTranslationsFrom(__DIR__.'/path/to/translations', 'courier');
	}

Note that within your `translations` folder, you would have further directories for each language, such as `en`, `es`, `ru`, etc. 여러분의 `언어 파일` 폴더 안에 `en`, `es`, `ru`와 같이 각 언어별로 하위 디렉토리로 나뉘어있을 필요가 있다는 점에 유의하십시오.

Now you may load your package translations using the following syntax:
이제 여러분의 패키지 언어 파일을 다음과 같이 로드 할 수 있습니다. 

	return trans('courier::file.line');

<a name="configuration"></a>
## Configuration
## 설정

Typically, you will want to publish your package's configuration file to the application's own `config` directory. This will allow users of your package to easily override your default configuration options. 일반적으로 여러분은 패키지의 설정 파일을 애플리케이션의 `config` 디렉토리에 퍼블리싱 하기를 원할 것입니다. 이것은 여러분의 패키지의 사용자가 패키지 설정 파일의 기본 옵션들을 손쉽게 수정할 수 있도록 해줍니다. 

To publish a configuration file, just use the `publishes` method from the `boot` method of your service provider: 설정 파일들을 퍼블리싱 하기 위해서는 서비스 프로바이더의 `boot` 메소드에서 `publishes` 메소드를 사용하면 됩니다. 

	$this->publishes([
		__DIR__.'/path/to/config/courier.php' => config_path('courier.php'),
	]);

Now, when users of your package execute Laravel's `vendor:publish` command, your file will be copied to the specified location. 이제 패키지를 이용하는 사용자가 라라벨의 `vendor:public` 명령어를 실행하면 패키지 설정 파일이 지정된 위치로 복사 될 것입니다. Of course, once your configuration has been published, it can be accessed like any other configuration file: 설정 파일이 한번 복사되고 나면 다른 설정 파일과 마찬가지로 손쉽게 엑세스 할 수 있습니다. 

	$value = config('courier.option');

You may also choose to merge your own package configuration file with the application's copy. 여러분은 또한 패키지 설정 파일이 애플리케이션의 설정 파일에 합쳐지도록 할 수도 있습니다. This allows your users to include only the options they actually want to override in the published copy of the configuration. 이렇게 하면 퍼블리싱된 설정 파일의 복사에서 사용자가 실제로 재 정의 하기를 원하는 옵션만을 포함 하도록 할 수 있습니다. To merge the configurations, use the `mergeConfigFrom` method within your service provider's `register` method: 설정 파일을 합치려면 서비스 프로바이더의 `register` 메소드안에서 `mergeConfigFrom` 메소드를 사용하면 됩니다. 

	$this->mergeConfigFrom(
		__DIR__.'/path/to/config/courier.php', 'courier'
	);

<a name="public-assets"></a>
## Public Assets
## Public 의 Asset 파일들. 

Your packages may have assets such as JavaScript, CSS, and images. 여러분의 패키지가 JavaScript, CSS 그리고 이미지 파일들 처럼 asset 파일들을 가지고 있을 수 있습니다. To publish assets, use the `publishes` method from your service provider's `boot` method. 이 파일들을 퍼블리싱 하기 위해서는 서비스 프로바이더의 `boot` 메소드에서 `publishes` 메소드를 사용하면 됩니다. In this example, we will also add a "public" asset group tag. 다음 예제에서는 "public" asset 그룹 태그를 추가로 지정하고 있습니다.

	$this->publishes([
		__DIR__.'/path/to/assets' => public_path('vendor/courier'),
	], 'public');

Now, when your package's users execute the `vendor:publish` command, your files will be copied to the specified location. 이제 패키지를 이용하는 사용자가 라라벨의 `vendor:public` 명령어를 실행하면 패키지 설정 파일이 지정된 위치로 복사 될 것입니다. Since you typically will need to overwrite the assets every time the package is updated, you may use the `--force` flag:   일반적으로 패키지가 업데이트 될 때에는 asset 파일들을 덮어써야 하기 때문에 이때에는 `--force` 플래그를 사용하면 됩니다. 

	php artisan vendor:publish --tag=public --force

If you would like to make sure your public assets are always up-to-date, you can add this command to the `post-update-cmd` list in your `composer.json` file. Public 의 asset 파일들을 항상 안전하게 업데이트하려면 이 명령어들을 `composer.json` 파일의 `post-update-cmd` 목록에 추가하는 것이 좋습니다. 

<a name="publishing-file-groups"></a>
## Publishing File Groups
## 파일 그룹 퍼블리싱하기

You may want to publish groups of files separately. 파일 그룹별로 분할하여 퍼블리싱 할 수 있습니다. For instance, you might want your users to be able to publish your package's configuration files and asset files separately. 예를 들어, 사용자 패키지의 설정 파일 및 asset 파일을 별도로 사용할 수 있도록 하고자하는 경우입니다. You can do this by 'tagging' them:  이런경우 이 파일들을 ‘태깅’함으로써 할 수 있습니다. 

	// Publish a config file
	$this->publishes([
		__DIR__.'/../config/package.php' => config_path('package.php')
	], 'config');

	// Publish your migrations
	$this->publishes([
		__DIR__.'/../database/migrations/' => database_path('/migrations')
	], 'migrations');

You can then publish these files separately by referencing their tag like so: 이제 태그를 지정 하면 파일을 분할하여 퍼블리싱 할 수 있습니다.

	php artisan vendor:publish --provider="Vendor\Providers\PackageServiceProvider" --tag="config"

<a name="routing"></a>
## Routing
## 라우팅

To load a routes file for your package, simply `include` it from within your service provider's `boot` method. 패키지에서 라우트 파일을 로딩 하기 위해서는 서비스 프로바이더의 `boot` 메소드 안에서 간단하게 `include`를 사용하면 됩니다. 

#### Including A Routes File From A Service Provider
#### 서비스 프로바이더에서 라우트 파일 로드하기

	public function boot()
	{
		include __DIR__.'/../../routes.php';
	}

> **Note 주의:** If your package is using controllers, you will need to make sure they are properly configured in your `composer.json` file's auto-load section. 여러분의 패키지가 컨트롤러를 사용하고 있다면 `composer.json` 파일의 설정을 통해서 오토로드가 될 수 있어야 합니다. 