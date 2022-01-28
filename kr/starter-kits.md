# 스타터 킷

- [소개하기](#introduction)
- [라라벨 Breeze](#laravel-breeze)
    - [설치하기](#laravel-breeze-installation)
    - [Breeze & Inertia](#breeze-and-inertia)
    - [Breeze & Next.js / API](#breeze-and-next)
- [라라벨 Jetstream](#laravel-jetstream)

<a name="introduction"></a>
## 소개하기

새로운 라라벨 애플리케이션 구축을 빠르게 시작할 수 있도록 인증 및 애플리케이션 스타터 킷을 제공할 수 있게 되어 기쁩니다. 이 킷는 애플리케이션 사용자를 등록하고 인증하는데 필요한 라우트, 컨트롤러, 뷰를 자동으로 스캐폴딩합니다.

이 스타터 킷을 반드시 사용해야 하는 것은 아닙니다. 라라벨의 새 복사본을 설치하면 여러분만의 애플리케이션을 처음부터 자유롭게 만들 수 있습니다. 저희는 어느 쪽이든 여러분들이 훌륭한 것을 만들 수 있다는 것을 알고 있습니다.

<a name="laravel-breeze"></a>
## 라라벨 Breeze

[라라벨 Breeze](https://github.com/laravel/breeze)는 로그인, 가입, 비밀번호 재설정, 이메일 인증, 비밀번호 확인을 포함한 라라벨의 [인증 기능](/docs/{{version}}/authentication)을 간단하게 구현한 것입니다. 라라벨 Breeze의 기본 뷰 레이어(View Layer)는 [Tailwind CSS](https://tailwindcss.com)을 사용하여 간단한 [블레이드 템플릿](/docs/{{version}}/blade)으로 구성되어 있습니다.

Breeze는 새로운 라라벨 애플리케이션을 시작하기 좋은 출발점을 제공하며 [라라벨 Livewire](https://laravel-livewire.com)을 통해 블레이드 템플릿을 한 단계 끌어올리는 프로젝트에도 훌륭한 선택입니다.

<a name="laravel-breeze-installation"></a>
### 설치하기

먼저, [새로운 라라벨 애플리케이션을 만든 후](/docs/{{version}}/installation) 데이터베이스를 구성한 다음 [데이터베이스 마이크레이션](/docs/{{version}}/migrations)을 해야 합니다.

```bash
curl -s https://laravel.build/example-app | bash

cd example-app

php artisan migrate
```

새 라라벨 애플리케이션을 만든 후에는 아래와 같이 컴포저 (Composer)명령어를 사용하여 라라벨 Breeze를 설치할 수 있습니다. 

```bash
composer require laravel/breeze --dev
```

컴포저를 통해 라라벨 Breeze 패키지를 설치했다면 `breeze:install` 명령어를 아티즌 (Artisan)으로 실행할 수 있습니다. 이 명령어는 인증 뷰, 라우트, 컨트롤러, 에셋들을 여러분의 애플리케이션에 게시합니다. 라라벨 Breeze는 모든 코드를 애플리케이션에 게시하므로 기능과 구현을 자유롭게 변경할 수 있습니다. Breeze를 설치한 후에는 애플리케이션의 CSS 파일이 올바르게 작동될 수 있도록 CSS 파일을 컴파일 해야 합니다.  

```nothing
php artisan breeze:install

npm install
npm run dev
php artisan migrate
```

이제 웹 브라우저에서 애플리케이션의 `/login` 또는 `/register` 경로로 접속할 수 있을 것입니다. Breeze의 모든 라우트는 `routes/auth.php` 파일에 정의되어 있습니다.

> {tip} 애플리케이션의 CSS 및 JavaScript 파일을 컴파일 하는 방법에 대해 자세히 알아보려면 [라라벨 Mix 문서](/docs/{{version}}/mix#running-mix)를 확인하여 주십시오.

<a name="breeze-and-inertia"></a>
### Breeze & Inertia

라라벨 Breeze는 Vue 또는 React로 구동되는 [Inertia.js](https://inertiajs.com)에 대한 프론트엔드 구현도 제공합니다. Inertia를 사용하려면 다음과 같이 아티즌 명령어 `breeze:install` 뒤애 `vue` 또는 `react` 를 입력 하십시오.

```nothing
php artisan breeze:install vue

// 또는...

php artisan breeze:install react

npm install
npm run dev
php artisan migrate
```

<a name="breeze-and-next"></a>
### Breeze & Next.js / API

라라벨 Breeze는 [Next](https://nextjs.org), [Nuxt](https://nuxtjs.org)에서 제공하는 최신 자바스크립트 애플리케이션을 인증할 준비가 된 인증 API를 설치할 수도 있습니다. 시작하려면 아티즌 명령어 `breeze:install` 뒤에 `api`를 입력하십시오.

```nothing
php artisan breeze:install api

php artisan migrate
```

설치가 진행되는 동안 `.env` 파일에 새로운 환경 변수 `FRONTEND_URL`를 추가해 주십시오. 등록한 주소는 자바스크립트 애플리케이션의 주소여야 합니다. 대부분의 로컬 개발 환경에서는 `http://localhost:3000`로 설정되어 있습니다.

<a name="next-reference-implementation"></a>
#### Next.js 참조 구현

이제, 라라벨과 프론트엔드를 연결할 준비가 다 되었습니다. Breeze를 활용하는 Next.js에 대한 참고 구현은 [깃허브](https://github.com/laravel/breeze-next)에서 볼 수 있습니다. 이 프론트엔드는 라라벨에서 유지 보수하며 Breeze에서 제공하는 기존 Blade 및 Inertia 와 동일한 유저 인터페이스를 포함합니다.

<a name="laravel-jetstream"></a>
## 라라벨 Jetstream

라라벨 Breeze는 라라벨 애플리케이션을 구축하기 위한 간단하고 최소한의 출발점을 제공하지만 Jetstream은 보다 강력한 기능과 추가적인 프론트엔드 기술로 무장되어 있습니다. **라라벨을 처음 접하신다면 라라벨 Jetstream을 시작하기 전에 라라벨 Breeze를 통해 라라벨의 작동 방식을 익히시는 것이 좋습니다.**

Jetstream은 라라벨용으로 아름답게 디자인 된 애플리케이션 스캐폴딩을 제공하며 로그인, 가입, 이메일 인증, 2차 인증, 세션 관리, 라라벨 Sanctum을 통한 API 지원, 팀 관리 기능이 포함되어 있습니다. Jetstream은 [Tailwind CSS](https://tailwindcss.com)을 사용하도록 설계 되었으며 프론트엔드로 [Livewire](https://laravel-livewire.com) 또는 [Inertia.js](https://inertiajs.com)를 사용할 수 있습니다.

라라벨 Jetstream 설치에 대한 문서는 [공식 Jetstream 문서](https://jetstream.laravel.com/2.x/introduction.html)에서 찾을 수 있습니다. 