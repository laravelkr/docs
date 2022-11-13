# Starter Kits
# 스타터 킷

- [Introduction](#introduction)
- [소개하기](#introduction)
- [Laravel Breeze](#laravel-breeze)
- [라라벨 Breeze](#laravel-breeze)
    - [Installation](#laravel-breeze-installation)
	- [설치하기](#laravel-breeze-installation)
    - [Breeze & Blade](#breeze-and-blade)
    - [Breeze & Blade](#breeze-and-blade)
    - [Breeze & React / Vue](#breeze-and-inertia)
    - [Breeze & React / Vue](#breeze-and-inertia)
    - [Breeze & Next.js / API](#breeze-and-next)
    - [Breeze & Next.js / API](#breeze-and-next)	
- [Laravel Jetstream](#laravel-jetstream)
- [라라벨 Jetstream](#laravel-jetstream)

<a name="introduction"></a>
## Introduction
## 소개하기

To give you a head start building your new Laravel application, we are happy to offer authentication and application starter kits. These kits automatically scaffold your application with the routes, controllers, and views you need to register and authenticate your application's users.  

새로운 라라벨 애플리케이션 구축을 빠르게 시작할 수 있도록 인증 및 애플리케이션 스타터 킷을 제공할 수 있게 되어 기쁩니다. 이 킷는 애플리케이션 사용자를 등록하고 인증하는데 필요한 라우트, 컨트롤러, 뷰를 자동으로 스캐폴딩합니다.

While you are welcome to use these starter kits, they are not required. You are free to build your own application from the ground up by simply installing a fresh copy of Laravel. Either way, we know you will build something great!  

이 스타터 킷을 반드시 사용해야 하는 것은 아닙니다. 라라벨의 새 복사본을 설치하면 여러분만의 애플리케이션을 처음부터 자유롭게 만들 수 있습니다. 저희는 어느 쪽이든 여러분들이 훌륭한 것을 만들 수 있다는 것을 알고 있습니다.

<a name="laravel-breeze"></a>
## Laravel Breeze
## 라라벨 Breeze

[Laravel Breeze](https://github.com/laravel/breeze) is a minimal, simple implementation of all of Laravel's [authentication features](/docs/{{version}}/authentication), including login, registration, password reset, email verification, and password confirmation. Laravel Breeze's default view layer is made up of simple [Blade templates](/docs/{{version}}/blade) styled with [Tailwind CSS](https://tailwindcss.com). Or, Breeze can scaffold your application using Vue or React and [Inertia](https://inertiajs.com).

[라라벨 Breeze](https://github.com/laravel/breeze)는 로그인, 가입, 비밀번호 재설정, 이메일 인증, 비밀번호 확인을 포함한 라라벨의 [인증 기능](/docs/{{version}}/authentication)을 간단하게 구현한 것입니다. 라라벨 Breeze의 기본 뷰 레이어(View Layer)는 [Tailwind CSS](https://tailwindcss.com)을 사용하여 간단한 [블레이드 템플릿](/docs/{{version}}/blade)으로 구성되어 있습니다. 또는 브리즈는 Vue 나 React 와 [Inertia](https://inertiajs.com)를 사용해서 애플리케이션을 스캐폴딩할 수 있습니다.

Breeze provides a wonderful starting point for beginning a fresh Laravel application and is also great choice for projects that plan to take their Blade templates to the next level with [Laravel Livewire](https://laravel-livewire.com).  

Breeze는 새로운 라라벨 애플리케이션을 시작하기 좋은 출발점을 제공하며 [라라벨 Livewire](https://laravel-livewire.com)을 통해 블레이드 템플릿을 한 단계 끌어올리는 프로젝트에도 훌륭한 선택입니다.

<img src="https://laravel.com/img/docs/breeze-register.png">

#### Laravel Bootcamp
#### 라라벨 부트캠프

If you're new to Laravel, feel free to jump into the [Laravel Bootcamp](https://bootcamp.laravel.com). The Laravel Bootcamp will walk you through building your first Laravel application using Breeze. It's a great way to get a tour of everything the Laravel and Breeze have to offer.

여러분이 라라벨을 처음 접하신다면 [라라벨 부트캠프](https://bootcamp.laravel.com)를 해보세요. 라라벨 부트캠프는 Breeze를 사용하여 첫 번째 라라벨 애플리케이션을 구축하는 과정을 안내합니다. 라라벨과 Breeze가 제공하는 모든 것을 둘러볼 수 있는 좋은 방법입니다.

<a name="laravel-breeze-installation"></a>
### Installation
### 설치하기

First, you should [create a new Laravel application](/docs/{{version}}/installation), configure your database, and run your [database migrations](/docs/{{version}}/migrations). Once you have created a new Laravel application, you may install Laravel Breeze using Composer:

먼저, [새로운 라라벨 애플리케이션을 만든 후](/docs/{{version}}/installation) 데이터베이스를 구성한 다음 [데이터베이스 마이그레이션](/docs/{{version}}/migrations)을 해야 합니다. 새 라라벨 애플리케이션을 만든 후에는 아래와 같이 컴포저 (Composer)명령어를 사용하여 라라벨 Breeze를 설치할 수 있습니다. 

```shell
composer require laravel/breeze --dev
```

Once Breeze is installed, you may scaffold your application using one of the Breeze "stacks" discussed in the documentation below.

Breeze가 설치되면 여러분은 아래에 설명되는 "스택들" 중 하나를 이용하여 애플리케이션을 스캐폴딩할 수 있습니다.

<a name="breeze-and-blade"></a>
### Breeze & Blade
### Breeze & Blade

After Composer has installed the Laravel Breeze package, you may run the `breeze:install` Artisan command. This command publishes the authentication views, routes, controllers, and other resources to your application. Laravel Breeze publishes all of its code to your application so that you have full control and visibility over its features and implementation.

컴포저를 통해 라라벨 Breeze 패키지를 설치했다면 `breeze:install` 명령어를 아티즌 (Artisan)으로 실행할 수 있습니다. 이 명령어는 인증 뷰, 라우트, 컨트롤러, 에셋들을 여러분의 애플리케이션에 게시합니다. 라라벨 Breeze는 모든 코드를 애플리케이션에 게시하므로 기능과 구현을 자유롭게 변경할 수 있습니다. 

The default Breeze "stack" is the Blade stack, which utilizes simple [Blade templates](/docs/{{version}}/blade) to render your application's frontend. The Blade stack may be installed by invoking the `breeze:install` command with no other additional arguments. After Breeze's scaffolding is installed, you should also compile your application's frontend assets:

Breeze의 기본 "스택"은 애플리케이션의 프론트엔드를 렌더링하는데 사용하기 위해 간단한 [블레이드 템플릿](/docs/{{version}}/blade)을 활용하는 블레이드 스택입니다. 블레이드 스택은 `breeze:install` 커맨드를 아무 추가 인자 없이 실행하면 설치됩니다. Breeze 스캐폴딩을 설치한 후에는 프론트엔드 에셋을 컴파일 해야 합니다.

```shell
php artisan breeze:install

php artisan migrate
npm install
npm run dev
```

Next, you may navigate to your application's `/login` or `/register` URLs in your web browser. All of Breeze's routes are defined within the `routes/auth.php` file.  

이제 웹 브라우저에서 애플리케이션의 `/login` 또는 `/register` 경로로 접속할 수 있을 것입니다. Breeze의 모든 라우트는 `routes/auth.php` 파일에 정의되어 있습니다.

> **Warning**
> To learn more about compiling your application's CSS and JavaScript, check out the Laravel's [Vite documentation](/docs/{{version}}/vite#running-vite).  

> **Warning**
> 애플리케이션의 CSS 및 JavaScript 파일을 컴파일 하는 방법에 대해 자세히 알아보려면 라라벨의 [Vite 문서](/docs/{{version}}/vite#running-vite)를 확인하여 주십시오.

<a name="breeze-and-inertia"></a>
### Breeze & React / Vue
### Breeze & React / Vue

Laravel Breeze also offers React and Vue scaffolding via an [Inertia](https://inertiajs.com) frontend implementation. Inertia allows you to build modern, single-page React and Vue applications using classic server-side routing and controllers.

라라벨 Breeze는 [Inertia](https://inertiajs.com) 프론트엔드 구현을 통해 React 와 Vue 스캐폴딩도 제공합니다. Inertia는 전통적인 서버사이드 라우팅과 컨트롤러를 사용해서 현대적인 싱글 페이지 React 와 Vue 애플리케이션을 만들 수 있게 해줍니다.

Inertia lets you enjoy the frontend power of React and Vue combined with the incredible backend productivity of Laravel and lightning-fast [Vite](https://vitejs.dev) compilation. To use an Inertia stack, specify `vue` or `react` as your desired stack when executing the `breeze:install` Artisan command. After Breeze's scaffolding is installed, you should also compile your application's frontend assets:

Inertia는 라라벨의 끝내주는 백엔드 생산성과 번개같이 빠른 컴파일과 결합된 React와 Vue의 프론트엔드 파워를 즐길 수 있게 해줍니다. Inertia 스택을 사용하려면 `breeze:install` 아티즌 커맨드를 실행할 때 원하는 스택으로 `vue` 나 `react`를 지정해주면 됩니다. Breeze 스캐폴딩을 설치한 후에는 프론트엔드 에셋을 컴파일 해야 합니다.

```shell
php artisan breeze:install vue

# 또는...

php artisan breeze:install react

php artisan migrate
npm install
npm run dev
```

Next, you may navigate to your application's `/login` or `/register` URLs in your web browser. All of Breeze's routes are defined within the `routes/auth.php` file.

설치가 되고나면 웹브라우저에서 `/login` 이나 `/register` URL을 탐색해볼 수 있습니다. Breeze의 모든 라우트는 `routes/auth.php` 파일에 정의되어 있습니다.

<a name="server-side-rendering"></a>
#### Server-Side Rendering
#### 서버사이드 렌더링

If you would like Breeze to scaffold support for [Inertia SSR](https://inertiajs.com/server-side-rendering), you may provide the `ssr` option when invoking the `breeze:install` command:

[Inertia SSR](https://inertiajs.com/server-side-rendering)를 지원하도록 Breeze를 스캐폴드하고 싶으면 `breeze:install` 명령을 실행할 때 `ssr` 옵션을 주면 됩니다.

```shell
php artisan breeze:install vue --ssr
php artisan breeze:install react --ssr
```

<a name="breeze-and-next"></a>
### Breeze & Next.js / API
### Breeze & Next.js / API

Laravel Breeze can also scaffold an authentication API that is ready to authenticate modern JavaScript applications such as those powered by [Next](https://nextjs.org), [Nuxt](https://nuxtjs.org), and others. To get started, specify the `api` stack as your desired stack when executing the `breeze:install` Artisan command:  

라라벨 Breeze는 [Next](https://nextjs.org), [Nuxt](https://nuxtjs.org)에서 제공하는 최신 자바스크립트 애플리케이션을 인증할 준비가 된 인증 API를 설치할 수도 있습니다. 시작하려면 아티즌 명령어 `breeze:install` 뒤에 `api`를 입력하십시오.

```shell
php artisan breeze:install api

php artisan migrate
```

During installation, Breeze will add a `FRONTEND_URL` environment variable to your application's `.env` file. This URL should be the URL of your JavaScript application. This will typically be `http://localhost:3000` during local development. In addition, you should ensure that your `APP_URL` is set to `http://localhost:8000`, which is the default URL used by the `serve` Artisan command.

설치가 진행되는 동안 `.env` 파일에 새로운 환경 변수 `FRONTEND_URL`를 추가해 주십시오. 등록한 주소는 자바스크립트 애플리케이션의 주소여야 합니다. 대부분의 로컬 개발 환경에서는 `http://localhost:3000`로 설정되어 있습니다. 이에 더해, `serve` 아티즌 명령어가 기본 URL로 사용하는 `APP_URL` 이 `http://localhost:8000` 으로 설정되어있어야 합니다.

<a name="next-reference-implementation"></a>
#### Next.js Reference Implementation
#### Next.js 참조 구현

Finally, you are ready to pair this backend with the frontend of your choice. A Next reference implementation of the Breeze frontend is [available on GitHub](https://github.com/laravel/breeze-next). This frontend is maintained by Laravel and contains the same user interface as the traditional Blade and Inertia stacks provided by Breeze.  

이제, 라라벨과 프론트엔드를 연결할 준비가 다 되었습니다. Breeze를 활용하는 Next.js에 대한 참고 구현은 [깃허브](https://github.com/laravel/breeze-next)에서 볼 수 있습니다. 이 프론트엔드는 라라벨에서 유지 보수하며 Breeze에서 제공하는 기존 Blade 및 Inertia 와 동일한 유저 인터페이스를 포함합니다.

<a name="laravel-jetstream"></a>
## Laravel Jetstream
## 라라벨 Jetstream

While Laravel Breeze provides a simple and minimal starting point for building a Laravel application, Jetstream augments that functionality with more robust features and additional frontend technology stacks. **For those brand new to Laravel, we recommend learning the ropes with Laravel Breeze before graduating to Laravel Jetstream.**  

라라벨 Breeze는 라라벨 애플리케이션을 구축하기 위한 간단하고 최소한의 출발점을 제공하지만 Jetstream은 보다 강력한 기능과 추가적인 프론트엔드 기술로 무장되어 있습니다. **라라벨을 처음 접하신다면 라라벨 Jetstream을 시작하기 전에 라라벨 Breeze를 통해 라라벨의 작동 방식을 익히시는 것이 좋습니다.**

Jetstream provides a beautifully designed application scaffolding for Laravel and includes login, registration, email verification, two-factor authentication, session management, API support via Laravel Sanctum, and optional team management. Jetstream is designed using [Tailwind CSS](https://tailwindcss.com) and offers your choice of [Livewire](https://laravel-livewire.com) or [Inertia](https://inertiajs.com) driven frontend scaffolding.  

Jetstream은 라라벨용으로 아름답게 디자인 된 애플리케이션 스캐폴딩을 제공하며 로그인, 가입, 이메일 인증, 2차 인증, 세션 관리, 라라벨 Sanctum을 통한 API 지원, 팀 관리 기능이 포함되어 있습니다. Jetstream은 [Tailwind CSS](https://tailwindcss.com)을 사용하도록 설계 되었으며 프론트엔드로 [Livewire](https://laravel-livewire.com) 또는 [Inertia](https://inertiajs.com)를 사용할 수 있습니다.

Complete documentation for installing Laravel Jetstream can be found within the [official Jetstream documentation](https://jetstream.laravel.com/2.x/introduction.html).  

라라벨 Jetstream 설치에 대한 문서는 [공식 Jetstream 문서](https://jetstream.laravel.com/2.x/introduction.html)에서 찾을 수 있습니다. 
