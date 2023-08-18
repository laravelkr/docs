# Frontend
# 프론트엔드

- [Introduction](#introduction)
- [소개](#introduction)
- [Using PHP](#using-php)
- [PHP 사용](#using-php)
    - [PHP & Blade](#php-and-blade)
    - [PHP & 블레이드](#php-and-blade)
    - [Livewire](#livewire)
    - [라이브와이어](#livewire)
    - [Starter Kits](#php-starter-kits)
    - [스타터 키트](#php-starter-kits)
- [Using Vue / React](#using-vue-react)
- [Vue / React 사용](#using-vue-react)
    - [Inertia](#inertia)
    - [Inertia](#inertia)
    - [Starter Kits](#inertia-starter-kits)
    - [스타터 키트](#inertia-starter-kits)
- [Bundling Assets](#bundling-assets)
- [에셋 번들링](#bundling-assets)

<a name="introduction"></a>
## Introduction
## 소개

Laravel is a backend framework that provides all of the features you need to build modern web applications, such as [routing](/docs/{{version}}/routing), [validation](/docs/{{version}}/validation), [caching](/docs/{{version}}/cache), [queues](/docs/{{version}}/queues), [file storage](/docs/{{version}}/filesystem), and more. However, we believe it's important to offer developers a beautiful full-stack experience, including powerful approaches for building your application's frontend.

라라벨은 라우팅 , 유효성 검사 , 캐싱 , 대기열 , 파일 스토리지 등과 같은 최신 웹 애플리케이션을 구축하는 데 필요한 모든 기능을 제공하는 백엔드 프레임워크입니다 . 그러나 우리는 개발자에게 애플리케이션의 프론트엔드를 구축하기 위한 강력한 접근 방식을 포함하여 아름다운 전체 스택 경험을 제공하는 것이 중요하다고 생각합니다.

There are two primary ways to tackle frontend development when building an application with Laravel, and which approach you choose is determined by whether you would like to build your frontend by leveraging PHP or by using JavaScript frameworks such as Vue and React. We'll discuss both of these options below so that you can make an informed decision regarding the best approach to frontend development for your application.

라라벨로 애플리케이션을 구축할 때 프론트엔드 개발을 처리하는 두 가지 주요 방법이 있으며, 어떤 접근 방식을 선택하는지는 PHP를 활용하여 프론트엔드를 구축할지 Vue 및 React와 같은 JavaScript 프레임워크를 사용하여 구축할지에 따라 결정됩니다. 응용 프로그램의 프론트엔드 개발에 대한 최상의 접근 방식에 대해 정보에 입각한 결정을 내릴 수 있도록 아래에서 이 두 가지 옵션에 대해 설명합니다.

<a name="using-php"></a>
## Using PHP
## PHP 사용

<a name="php-and-blade"></a>
### PHP & Blade
### PHP & 블레이드

In the past, most PHP applications rendered HTML to the browser using simple HTML templates interspersed with PHP `echo` statements which render data that was retrieved from a database during the request:

과거에 대부분의 PHP 애플리케이션은 요청 중에 데이터베이스에서 검색된 데이터를 렌더링하는 PHP `echo` 명령문이 산재된 간단한 HTML 템플릿을 사용하여 브라우저에 HTML을 렌더링했습니다.

```blade
<div>
    <?php foreach ($users as $user): ?>
        Hello, <?php echo $user->name; ?> <br />
    <?php endforeach; ?>
</div>
```

In Laravel, this approach to rendering HTML can still be achieved using [views](/docs/{{version}}/views) and [Blade](/docs/{{version}}/blade). Blade is an extremely light-weight templating language that provides convenient, short syntax for displaying data, iterating over data, and more:

Laravel에서 HTML을 렌더링하는 이 접근 방식은 여전히 [뷰](/docs/{{version}}/views) 와 [블레이드](/docs/{{version}}/blade)를 사용하여 달성할 수 있습니다. 블레이드는 데이터 표시, 데이터 반복 등을 위한 편리하고 짧은 구문을 제공하는 매우 가벼운 템플릿 언어입니다.

```blade
<div>
    @foreach ($users as $user)
        Hello, {{ $user->name }} <br />
    @endforeach
</div>
```

When building applications in this fashion, form submissions and other page interactions typically receive an entirely new HTML document from the server and the entire page is re-rendered by the browser. Even today, many applications may be perfectly suited to having their frontends constructed in this way using simple Blade templates.

이러한 방식으로 애플리케이션을 구축할 때 양식 제출 및 기타 페이지 상호 작용은 일반적으로 서버에서 완전히 새로운 HTML 문서를 수신하고 전체 페이지가 브라우저에 의해 다시 렌더링됩니다. 오늘날에도 많은 애플리케이션이 간단한 블레이드 템플릿을 사용하여 이러한 방식으로 프론트엔드를 구성하는 데 완벽하게 적합할 수 있습니다.

<a name="growing-expectations"></a>
#### Growing Expectations
#### 커져가는 기대

However, as user expectations regarding web applications have matured, many developers have found the need to build more dynamic frontends with interactions that feel more polished. In light of this, some developers choose to begin building their application's frontend using JavaScript frameworks such as Vue and React.

그러나 웹 애플리케이션에 대한 사용자의 기대치가 높아짐에 따라 많은 개발자가 보다 세련된 상호 작용을 통해 보다 동적인 프런트엔드를 구축할 필요가 있음을 알게 되었습니다. 이에 비추어 일부 개발자는 Vue 및 React와 같은 자바스크립트 프레임워크를 사용하여 애플리케이션의 프론트엔드 구축을 시작하기로 선택합니다.

Others, preferring to stick with the backend language they are comfortable with, have developed solutions that allow the construction of modern web application UIs while still primarily utilizing their backend language of choice. For example, in the [Rails](https://rubyonrails.org/) ecosystem, this has spurred the creation of libraries such as [Turbo](https://turbo.hotwired.dev/) [Hotwire](https://hotwired.dev/), and [Stimulus](https://stimulus.hotwired.dev/).

편안한 백엔드 언어를 선호하는 다른 사람들은 선택한 백엔드 언어를 주로 사용하면서 최신 웹 애플리케이션 UI를 구성할 수 있는 솔루션을 개발했습니다. 예를 들어, [레일즈](https://rubyonrails.org/) 생태계에서 이는 [Turbo](https://turbo.hotwired.dev/) [Hotwire](https://hotwired.dev/) 및 [Stimulus](https://stimulus.hotwired.dev/) 와 같은 라이브러리 생성에 박차를 가했습니다.

Within the Laravel ecosystem, the need to create modern, dynamic frontends by primarily using PHP has led to the creation of [Laravel Livewire](https://livewire.laravel.com) and [Alpine.js](https://alpinejs.dev/).

라라벨 생태계 내에서 주로 PHP를 사용하여 현대적이고 동적인 프론트엔드를 생성해야 하는 필요성으로 인해 라라벨 [라이브와이어](https://livewire.laravel.com) 및 [Alpine.js](https://alpinejs.dev/) 가 생성되었습니다 .

<a name="livewire"></a>
### Livewire
### Livewire

[Laravel Livewire](https://livewire.laravel.com) is a framework for building Laravel powered frontends that feel dynamic, modern, and alive just like frontends built with modern JavaScript frameworks like Vue and React.

[라라벨 Livewire](https://livewire.laravel.com)는 Vue 및 React와 같은 최신 자바스크립트 프레임워크로 구축된 프론트엔드와 마찬가지로 역동적이고 현대적이며 생생한 느낌을 주는 라라벨 기반 프론트엔드를 구축하기 위한 프레임워크입니다.

When using Livewire, you will create Livewire "components" that render a discrete portion of your UI and expose methods and data that can be invoked and interacted with from your application's frontend. For example, a simple "Counter" component might look like the following:

라이브와이어를 사용할 때 UI의 개별 부분을 렌더링하는 라이브와이어 "컴포넌트"를 만들고 애플리케이션의 프런트엔드에서 호출하고 상호 작용할 수 있는 메서드와 데이터를 노출합니다. 예를 들어 간단한 "카운터" 컴포넌트는 다음과 같을 수 있습니다.

```php
<?php

namespace App\Http\Livewire;

use Livewire\Component;

class Counter extends Component
{
    public $count = 0;

    public function increment()
    {
        $this->count++;
    }

    public function render()
    {
        return view('livewire.counter');
    }
}
```

And, the corresponding template for the counter would be written like so:

그리고 카운터에 해당하는 템플릿은 다음과 같이 작성됩니다.

```blade
<div>
    <button wire:click="increment">+</button>
    <h1>{{ $count }}</h1>
</div>
```

As you can see, Livewire enables you to write new HTML attributes such as `wire:click` that connect your Laravel application's frontend and backend. In addition, you can render your component's current state using simple Blade expressions.

보시다시피 라이브와이어를 사용하면 라라벨 애플리케이션의 프론트엔드와 백엔드를 연결하는 `wire:click`과 같은 새로운 HTML 속성을 작성할 수 있습니다. 또한 간단한 블레이드 표현식을 사용하여 컴포넌트의 현재 상태를 렌더링할 수 있습니다.

For many, Livewire has revolutionized frontend development with Laravel, allowing them to stay within the comfort of Laravel while constructing modern, dynamic web applications. Typically, developers using Livewire will also utilize [Alpine.js](https://alpinejs.dev/) to "sprinkle" JavaScript onto their frontend only where it is needed, such as in order to render a dialog window.

많은 사람들에게 라이브와이어는 라라벨과 함께 프론트엔드 개발에 혁명을 일으켜 라라벨의 편안함을 유지하면서 현대적이고 동적인 웹 애플리케이션을 구축할 수 있도록 했습니다. 일반적으로 라이브와이어를 사용하는 개발자는 [Alpine.js](https://alpinejs.dev/)를 사용하여 대화 창을 렌더링하는 것과 같이 필요한 경우에만 자바스크립트를 프론트엔드에 "뿌릴" 것입니다.

If you're new to Laravel, we recommend getting familiar with the basic usage of [views](/docs/{{version}}/views) and [Blade](/docs/{{version}}/blade). Then, consult the official [Laravel Livewire documentation](https://livewire.laravel.com/docs) to learn how to take your application to the next level with interactive Livewire components.

라라벨이 처음이라면 [뷰](/docs/{{version}}/views) 와 [블레이드](/docs/{{version}}/blade)의 기본 사용법에 익숙해지는 것이 좋습니다 . 그런 다음 공식 [라라벨 라이브와이어 설명서](https://livewire.laravel.com/docs)를 참조 하여 대화형 라이브와이어 컴포넌트를 사용하여 애플리케이션을 다음 단계로 끌어올리는 방법을 배우십시오.

<a name="php-starter-kits"></a>
### Starter Kits
### 스타터 키트

If you would like to build your frontend using PHP and Livewire, you can leverage our Breeze or Jetstream [starter kits](/docs/{{version}}/starter-kits) to jump-start your application's development. Both of these starter kits scaffold your application's backend and frontend authentication flow using [Blade](/docs/{{version}}/blade) and [Tailwind](https://tailwindcss.com) so that you can simply start building your next big idea.

PHP 및 라이브와이어를 사용하여 프런트엔드를 구축하려는 경우 Breeze 또는 Jetstream [스타터 키트](/docs/{{version}}/starter-kits) 를 활용하여 애플리케이션 개발을 바로 시작할 수 있습니다. 이 두 스타터 키트는 모두 [블레이드](/docs/{{version}}/blade) 및 [Tailwind](https://tailwindcss.com)를 사용하여 애플리케이션의 백엔드 및 프런트엔드 인증 흐름을 스캐폴딩 하므로 다음 아이디어 구축을 간단히 시작할 수 있습니다.

<a name="using-vue-react"></a>
## Using Vue / React
## 뷰 / 리액트 사용하기

Although it's possible to build modern frontends using Laravel and Livewire, many developers still prefer to leverage the power of a JavaScript framework like Vue or React. This allows developers to take advantage of the rich ecosystem of JavaScript packages and tools available via NPM.

라라벨 및 라이브와이어를 사용하여 최신 프론트엔드를 구축하는 것이 가능하지만 많은 개발자는 여전히 Vue 또는 React와 같은 자바스크립트 프레임워크의 기능을 활용하는 것을 선호합니다. 이를 통해 개발자는 NPM을 통해 사용할 수 있는 자바스크립트 패키지 및 도구의 풍부한 에코시스템을 활용할 수 있습니다.

However, without additional tooling, pairing Laravel with Vue or React would leave us needing to solve a variety of complicated problems such as client-side routing, data hydration, and authentication. Client-side routing is often simplified by using opinionated Vue / React frameworks such as [Nuxt](https://nuxtjs.org/) and [Next](https://nextjs.org/); however, data hydration and authentication remain complicated and cumbersome problems to solve when pairing a backend framework like Laravel with these frontend frameworks.

그러나 추가 도구 없이 라라벨을 Vue 또는 React와 페어링하면 클라이언트 측 라우팅, 데이터 수화 및 인증과 같은 다양한 복잡한 문제를 해결해야 합니다. 클라이언트 측 라우팅은 종종 [Nuxt](https://nuxtjs.org/) 및 [Next](https://nextjs.org/) 와 같은 독창적인 Vue/React 프레임워크를 사용하여 단순화됩니다. 그러나 데이터 수화 및 인증은 라라벨과 같은 백엔드 프레임워크를 이러한 프론트엔드 프레임워크와 페어링할 때 해결해야 할 복잡하고 성가신 문제로 남아 있습니다.

In addition, developers are left maintaining two separate code repositories, often needing to coordinate maintenance, releases, and deployments across both repositories. While these problems are not insurmountable, we don't believe it's a productive or enjoyable way to develop applications.

또한 개발자는 두 개의 별도 코드 리포지토리를 유지 관리해야 하므로 두 리포지토리에서 유지 관리, 릴리스 및 배포를 조정해야 하는 경우가 많습니다. 이러한 문제가 극복할 수 없는 것은 아니지만 이것이 애플리케이션을 개발하는 생산적이거나 즐거운 방법이라고는 생각하지 않습니다.

<a name="inertia"></a>
### Inertia
### Inertia

Thankfully, Laravel offers the best of both worlds. [Inertia](https://inertiajs.com) bridges the gap between your Laravel application and your modern Vue or React frontend, allowing you to build full-fledged, modern frontends using Vue or React while leveraging Laravel routes and controllers for routing, data hydration, and authentication — all within a single code repository. With this approach, you can enjoy the full power of both Laravel and Vue / React without crippling the capabilities of either tool.

고맙게도 라라벨은 두 가지 장점을 모두 제공합니다. [Inertia](https://inertiajs.com)는 라라벨 애플리케이션과 최신 Vue 또는 React 프론트엔드 사이의 격차를 해소하여 Vue 또는 React를 사용하여 본격적인 최신 프론트엔드를 구축하는 동시에 단일 코드 저장소 내에서 라우팅, 데이터 수화 및 인증을 위해 라라벨 라우트 및 컨트롤러를 활용합니다. 이 접근 방식을 사용하면 두 도구의 기능을 손상시키지 않고 라라벨과 Vue/React의 모든 기능을 즐길 수 있습니다.

After installing Inertia into your Laravel application, you will write routes and controllers like normal. However, instead of returning a Blade template from your controller, you will return an Inertia page:

라라벨 애플리케이션에 Inertia를 설치한 후에는 평소와 같이 라우트와 컨트롤러를 작성합니다. 그러나 컨트롤러에서 블레이드 템플릿을 반환하는 대신 Inertia 페이지를 반환합니다.

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\Models\User;
use Inertia\Inertia;
use Inertia\Response;

class UserController extends Controller
{
    /**
     * Show the profile for a given user.
     */
    public function show(string $id): Response
    {
        return Inertia::render('Users/Profile', [
            'user' => User::findOrFail($id)
        ]);
    }
}
```

An Inertia page corresponds to a Vue or React component, typically stored within the `resources/js/Pages` directory of your application. The data given to the page via the `Inertia::render` method will be used to hydrate the "props" of the page component:

Inertia 페이지는 일반적으로 애플리케이션의 `resources/js/Pages` 디렉토리에 저장되는 Vue 또는 React 컴포넌트에 해당 합니다. `Inertia::render` 메소드를 통해 페이지에 제공된 데이터는 페이지 컴포넌트의 "props"를 수화하는 데 사용됩니다.

```vue
<script setup>
import Layout from '@/Layouts/Authenticated.vue';
import { Head } from '@inertiajs/inertia-vue3';

const props = defineProps(['user']);
</script>

<template>
    <Head title="User Profile" />

    <Layout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Profile
            </h2>
        </template>

        <div class="py-12">
            Hello, {{ user.name }}
        </div>
    </Layout>
</template>
```

As you can see, Inertia allows you to leverage the full power of Vue or React when building your frontend, while providing a light-weight bridge between your Laravel powered backend and your JavaScript powered frontend.

보시다시피 Inertia를 사용하면 프론트엔드를 구축할 때 Vue 또는 React의 모든 기능을 활용할 수 있으며 라라벨 기반 백엔드와 자바스크립트 기반 프론트엔드 간에 가벼운 다리를 제공할 수 있습니다.

#### Server-Side Rendering
#### 서버 사이드 렌더링

If you're concerned about diving into Inertia because your application requires server-side rendering, don't worry. Inertia offers [server-side rendering support](https://inertiajs.com/server-side-rendering). And, when deploying your application via [Laravel Forge](https://forge.laravel.com), it's a breeze to ensure that Inertia's server-side rendering process is always running.

응용 프로그램에 서버 사이드 렌더링이 필요하기 때문에 Inertia에 뛰어드는 것이 걱정된다면 걱정하지 마십시오. Inertia는 [서버 사이드 렌더링 지원](https://inertiajs.com/server-side-rendering)을 제공합니다. 그리고 [라라벨 Forge](https://forge.laravel.com)를 통해 애플리케이션을 배포할 때 Inertia의 서버 사이드 렌더링 프로세스가 항상 실행되고 있는지 확인하는 것은 아주 쉽습니다.

<a name="inertia-starter-kits"></a>
### Starter Kits
### 스타터 키트

If you would like to build your frontend using Inertia and Vue / React, you can leverage our Breeze or Jetstream [starter kits](/docs/{{version}}/starter-kits#breeze-and-inertia) to jump-start your application's development. Both of these starter kits scaffold your application's backend and frontend authentication flow using Inertia, Vue / React, [Tailwind](https://tailwindcss.com), and [Vite](https://vitejs.dev) so that you can start building your next big idea.

Inertia 및 Vue/React를 사용하여 프런트엔드를 구축하려는 경우 Breeze 또는 Jetstream [스타터 키트](/docs/{{version}}/starter-kits#breeze-and-inertia)를 활용하여 애플리케이션 개발을 바로 시작할 수 있습니다. 이 두 스타터 키트는 모두 Inertia, Vue/React, [Tailwind](https://tailwindcss.com) 및 [Vite](https://vitejs.dev) 를 사용하여 애플리케이션의 백엔드 및 프런트엔드 인증 흐름을 스캐폴딩 하므로 다음 아이디어 구축을 시작할 수 있습니다.

<a name="bundling-assets"></a>
## Bundling Assets
## 에셋 번들링

Regardless of whether you choose to develop your frontend using Blade and Livewire or Vue / React and Inertia, you will likely need to bundle your application's CSS into production ready assets. Of course, if you choose to build your application's frontend with Vue or React, you will also need to bundle your components into browser ready JavaScript assets.

블레이드와 라이브와이어 또는 Vue / React와 Inertia를 사용하여 프론트엔드를 개발하기로 선택했는지 여부에 관계없이 애플리케이션의 CSS를 프로덕션 준비 자산에 번들로 묶어야 할 수 있습니다. 물론 Vue 또는 React를 사용하여 애플리케이션의 프론트엔드를 빌드하기로 선택한 경우 컴포넌트를 브라우저 지원 자바스크립트 자산에 번들로 묶어야 합니다.

By default, Laravel utilizes [Vite](https://vitejs.dev) to bundle your assets. Vite provides lightning-fast build times and near instantaneous Hot Module Replacement (HMR) during local development. In all new Laravel applications, including those using our [starter kits](/docs/{{version}}/starter-kits), you will find a `vite.config.js` file that loads our light-weight Laravel Vite plugin that makes Vite a joy to use with Laravel applications.

기본적으로 라라벨은 [Vite](https://vitejs.dev)를 사용 하여 자산을 묶습니다. Vite는 로컬 개발 중에 번개처럼 빠른 빌드 시간과 거의 즉각적인 HMR(핫 모듈 교체)을 제공합니다. [스타터 키트](/docs/{{version}}/starter-kits)를 사용하는 애플리케이션을 포함하여 모든 새로운 라라벨 애플리케이션에서 Vite를 라라벨 애플리케이션과 함께 사용하는 것을 즐겁게 만드는 경량 라라벨 Vite 플러그인을 로드하는 `vite.config.js` 파일을 찾을 수 있습니다.

The fastest way to get started with Laravel and Vite is by beginning your application's development using [Laravel Breeze](/docs/{{version}}/starter-kits#laravel-breeze), our simplest starter kit that jump-starts your application by providing frontend and backend authentication scaffolding.

라라벨과 Vite를 시작하는 가장 빠른 방법은 프론트엔드 및 백엔드 인증 스캐폴딩을 제공하여 애플리케이션을 빠르게 시작하는 가장 간단한 스타터 키트인 [라라벨 Breeze](/docs/{{version}}/starter-kits#laravel-breeze)를 사용하여 애플리케이션 개발을 시작하는 것입니다.

> **Note**  
> For more detailed documentation on utilizing Vite with Laravel, please see our [dedicated documentation on bundling and compiling your assets](/docs/{{version}}/vite).

> **Note**  
> Vite를 라라벨과 함께 활용하는 방법에 대한 자세한 내용은 [자산 번들링 및 컴파일에 대한 전용 문서](/docs/{{version}}/vite)를 참조하십시오 .
