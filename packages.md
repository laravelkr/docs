# 패키지 개발(Package Development)

- [소개](#introduction)
- [뷰](#views)
- [언어파일](#translations)
- [설정](#configuration)
- [Public Assets ](#public-assets)
- [Publishing File Groups ](#publishing-file-groups)
- [라우팅](#routing)

<a name="introduction"></a>
## 소개

패키지를 개발하는 것은 라라벨에 기능을 추가하는 주요한 방법입니다. 패키지는 예를 들어, 날짜 라이브러리인 [Carbon](https://github.com/briannesbitt/Carbon)이나 BDD(행위 주도 개발) 테스트 프레임워크인 [Behat](https://github.com/Behat/Behat)과 같이 어느 것이든 될 수 있습니다.

물론 다양한 종류의 패키지가 존재합니다. 라라벨 뿐만 아니라 다른 어떤 프레임워크와도 작동할 수 있는 독립적인 패키지도 있습니다. Carbon과 Behat 은 이런 독립적으로 동작하는 패키지들의 예입니다. 이러한 패키지들을 라라벨과 사용하려면 `composer.json`파일에 추가하면 됩니다.

반대로, 라라벨 프레임워크에만 사용할 수 있는 패키지들도 있습니다. 이러한 패키지들은 라라벨 응용 프로그램의 기능을 사용하는 라우트, 컨트롤러, 뷰, 설정들을 가질 것입니다. 이 가이드는 라라벨에 특화된 패키지의 개발을 주로 설명합니다.

모든 라라벨 패키지는 [Packagist](http://packagist.org)와 [Composer](http://getcomposer.org)를 통해서 배포되기 때문에 PHP 패키지 배포 도구에 대해 알고 있는 것이 중요합니다.

<!--chak-comment-패키지-개발(Package-Development)-소개-->

<a name="views"></a>
## 뷰

패키지의 내부 구조는 여러분이 구성하기 나름입니다. 그러나 일반적으로 각 패키지는 하나 이상의 [서비스 프로바이더](/docs/5.0/providers)를 가지고 있습니다. 서비스 프로바이더는 [서비스 컨테이너](/docs/5.0/container) 바인딩을 설정하고, 동시에 패키지의 설정, 뷰, 언어 파일의 위치를 지정합니다.

### 뷰 파일

일반적으로 패키지의 뷰는 더블 콜론을 사용하여 ”네임 스페이스”를 지정합니다:

	return view('package::view.name');

여러분이 해야 할 것은 주어진 네임스페이스에 해당하는 뷰 파일들이 어디에 있는지 라라벨에 알려주는 것입니다. 예를 들어, 여러분의 패키지 이름이 “courier” 라고 할 때 서비스 프로바이더의 `boot` 메소드에 다음과 같이 뷰 파일의 위치를 설정할 수 있습니다.

	public function boot()
	{
		$this->loadViewsFrom(__DIR__.'/path/to/views', 'courier');
	}

이제 패키지에서 다음처럼 뷰파일을 로드 할 수 있습니다:

	return view('courier::view.name');

여러분이 `loadViewsFrom` 메소드를 사용할 때, 라라벨은 실제로 뷰을 위한 **두 개의** 위치를 등록합니다. 하나는 어플리케이션의 `resources/views/vendor` 디렉토리 이고, 다른 하나는 여러분이 지정한 디렉토리입니다. 앞의 `courier` 예제를 생각해 보면, 패키지의 뷰가 요청될 때 라라벨은 먼저 `resources/views/vendor/courier` 디렉토리에 파일이 준비되어 있는지 확인합니다. 이때 뷰를 확인하지 못하면 여러분이 `loadViewsFrom`를 통해서 지정한 디렉토리에서 패키지 뷰를 찾게 될 것입니다. 이러한 방법은 사용자가 여러분의 패키지 뷰를 수정하거나, 재정의 하기 쉽게 해줍니다.

#### 뷰 퍼블리싱

패키지의 뷰를 `resources/views/vendor` 디렉토리에 퍼블리싱하려면 서비스 프로바이더의 `boot` 메소드에서 `publishes` 메소드를 사용하면 됩니다:

	public function boot()
	{
		$this->loadViewsFrom(__DIR__.'/path/to/views', 'courier');

		$this->publishes([
			__DIR__.'/path/to/views' => base_path('resources/views/vendor/courier'),
		]);
	}

이제 패키지를 이용하는 사용자가 라라벨의 `vendor:public` 명령어를 실행하면 패키지 뷰 디렉토리가 지정된 위치로 복사 될 것입니다:

파일이 있을 때 덮어 씌우려면 명령어에 `--force` 옵션을 주면 됩니다:

	php artisan vendor:publish --force

> **참고 :** `publishes` 메소드는 **어떤** 유형의 파일이라도 원하는 위치로 파일을 퍼블리싱 할 수 있습니다.

<!--chak-comment-패키지-개발(Package-Development)-뷰-->

<a name="translations"></a>
## 언어파일

일반적으로 패키지의 언어파일은 더블 콜론을 사용하여 ”네임 스페이스”를 지정합니다:

	return trans('package::file.line');

여러분이 해야 할 것은 주어진 네임스페이스에 해당하는 언어 파일들이 어디에 있는지 라라벨에 알려주는 것입니다.  예를 들어, 여러분의 패키지 이름이 “courier” 라고 할 때 서비스 프로바이더의 `boot` 메소드에 다음과 같이 언어 파일의 위치를 설정할 수 있습니다:

	public function boot()
	{
		$this->loadTranslationsFrom(__DIR__.'/path/to/translations', 'courier');
	}

여러분의 `언어 파일` 폴더 안에 `en`, `es`, `ru`와 같이 각 언어별로 하위 디렉토리로 나뉘어있을 필요가 있다는 점에 유의하십시오.

이제 여러분의 패키지 언어 파일을 다음과 같이 로드 할 수 있습니다:

	return trans('courier::file.line');

<!--chak-comment-패키지-개발(Package-Development)-언어파일-->

<a name="configuration"></a>
## 설정

일반적으로 여러분은 패키지의 설정 파일을 어플리케이션의 `config` 디렉토리에 퍼블리싱 하기를 원할 것입니다. 이것은 여러분의 패키지의 사용자가 패키지 설정 파일의 기본 옵션들을 손쉽게 수정할 수 있도록 해줍니다.

설정 파일들을 퍼블리싱 하기 위해서는 서비스 프로바이더의 `boot` 메소드에서 `publishes` 메소드를 사용하면 됩니다:

	$this->publishes([
		__DIR__.'/path/to/config/courier.php' => config_path('courier.php'),
	]);

이제 패키지를 이용하는 사용자가 라라벨의 `vendor:public` 명령어를 실행하면 패키지 설정 파일이 지정된 위치로 복사 될 것입니다. 설정 파일이 한번 복사되고 나면 다른 설정 파일과 마찬가지로 손쉽게 엑세스 할 수 있습니다:

	$value = config('courier.option');

여러분은 또한, 패키지 설정 파일이 어플리케이션의 설정 파일에 합쳐지도록 할 수도 있습니다. 이렇게 하면 퍼블리싱된 설정 파일의 복사에서 사용자가 실제로 재 정의 하기를 원하는 옵션만을 포함 하도록 할 수 있습니다. 설정 파일을 합치려면 서비스 프로바이더의 `register` 메소드안에서 `mergeConfigFrom` 메소드를 사용하면 됩니다:

	$this->mergeConfigFrom(
		__DIR__.'/path/to/config/courier.php', 'courier'
	);

<!--chak-comment-패키지-개발(Package-Development)-설정-->

<a name="public-assets"></a>
## Public의 Asset 파일들

여러분의 패키지가 JavaScript, CSS 그리고 이미지 파일들처럼 asset 파일들을 가지고 있을 수 있습니다. 이 파일들을 퍼블리싱 하기 위해서는 서비스 프로바이더의 `boot` 메소드에서 `publishes` 메소드를 사용하면 됩니다. 다음 예제에서는 "public" asset 그룹 태그를 추가로 지정하고 있습니다.

	$this->publishes([
		__DIR__.'/path/to/assets' => public_path('vendor/courier'),
	], 'public');

이제 패키지를 이용하는 사용자가 라라벨의 `vendor:public` 명령어를 실행하면 패키지 설정 파일이 지정된 위치로 복사 될 것입니다. 일반적으로 패키지가 업데이트 될 때에는 asset 파일들을 덮어써야 하기 때문에 이때에는 `--force` 플래그를 사용하면 됩니다:

	php artisan vendor:publish --tag=public --force

Public의 asset 파일들을 항상 안전하게 업데이트하려면 이 명령어를 `composer.json` 파일의 `post-update-cmd` 목록에 추가하는 것이 좋습니다.

<!--chak-comment-패키지-개발(Package-Development)-Public-의-Asset-파일들-->

<a name="publishing-file-groups"></a>
## 파일 그룹 퍼블리싱하기

파일 그룹별로 분할하여 퍼블리싱 할 수 있습니다. 예를 들어, 사용자 패키지의 설정 파일 및 asset 파일을 별도로 사용할 수 있도록 하고자하는 경우입니다. 이런경우 이 파일들을 ‘태깅’함으로써 할 수 있습니다:

	// Publish a config file
	$this->publishes([
		__DIR__.'/../config/package.php' => config_path('package.php')
	], 'config');

	// Publish your migrations
	$this->publishes([
		__DIR__.'/../database/migrations/' => database_path('/migrations')
	], 'migrations');

이제 태그를 지정 하면 파일을 분할하여 퍼블리싱 할 수 있습니다:

	php artisan vendor:publish --provider="Vendor\Providers\PackageServiceProvider" --tag="config"

<!--chak-comment-패키지-개발(Package-Development)-파일-그룹-퍼블리싱하기-->

<a name="routing"></a>
## 라우팅

패키지에서 라우트 파일을 로딩 하기 위해서는 서비스 프로바이더의 `boot` 메소드 안에서 간단하게 `include`를 사용하면 됩니다.

#### 서비스 프로바이더에서 라우트 파일 로드하기

	public function boot()
	{
		include __DIR__.'/../../routes.php';
	}

> **주의:** 여러분의 패키지가 컨트롤러를 사용하고 있다면 `composer.json` 파일의 설정을 통해서 오토로드가 될 수 있어야 합니다.

<!--chak-comment-패키지-개발(Package-Development)-라우팅-->
