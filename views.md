# 뷰-Views

- [시작하기](#introduction)
- [뷰파일 생성 & 렌더](#creating-and-rendering-views)
    - [중첩된 뷰 디렉토리](#nested-view-directories)
    - [사용가능한 첫 뷰 만들기](#creating-the-first-available-view)
    - [뷰가 있는지 확인하기](#determining-if-a-view-exists)
- [뷰에 데이터 전달하기](#passing-data-to-views)
    - [모든 뷰에서 데이터 공유하기](#sharing-data-with-all-views)
- [뷰 컴포저](#view-composers)
    - [뷰 작성자](#view-creators)
- [뷰 최적화](#optimizing-views)

<a name="introduction"></a>
## 시작하기

라우트와 컨트롤러에서 직접 전체 HTML 문자열을 반환하는 것은 현실적이지 않습니다. 다행히도 뷰-View는 HTML을 템플릿 파일로 관리할 수 있는 편리한 방법을 제공합니다. 뷰-View는 애플리케이션의 컨트롤러 로직을 프레젠테이션 로직과 분리하고 `resources/views` 디렉토리에 저장됩니다. 간단한 뷰-View 파일의 경우 다음처럼 보일 것입니다.

```html
<!-- View stored in resources/views/greeting.blade.php -->

<html>
    <body>
        <h1>Hello, {{ $name }}</h1>
    </body>
</html>
```

뷰-View 파일이 `resources/views/greeting.blade.php`에 저장되어 있으므로, 다음과 같이 전역 `view` 헬퍼를 사용하여 반환할 수 있습니다.

    Route::get('/', function () {
        return view('greeting', ['name' => 'James']);
    });

> {tip} blade 템플릿 작성 방법에 대한 자세한 정보를 찾고 계십니까? 시작하려면 전체 [blade 문서](/docs/{{version}}/blade)를 확인하세요.

<a name="creating-and-rendering-views"></a>
## 뷰파일 생성 & 렌더

애플리케이션의 `resources/views` 디렉토리에 확장자가 `.blade.php`인 파일을 배치하여 뷰-View 파일을 생성할 수 있습니다. `.blade.php` 확장자는 파일에 [blade 템플릿](/docs/{{version}}/blade)이 포함되어 있음을 프레임워크에 알립니다. blade 템플릿에는 HTML과 blade 지시문이 포함되어 있어 값을 쉽게 출력하고, "if" 문을 만들고, 데이터를 반복하는 등의 작업을 수행할 수 있습니다.

뷰-View 파일을 생성하고 나면, 전역 `view` 헬퍼를 사용하여 애플리케이션의 route 또는 controller 중 하나에서 뷰-View를 반환할 수 있습니다.

    Route::get('/', function () {
        return view('greeting', ['name' => 'James']);
    });

뷰-View는 `View` 파사드를 사용하여 반환될 수도 있습니다.

    use Illuminate\Support\Facades\View;

    return View::make('greeting', ['name' => 'James']);

보이는 바와 같이 `view` 헬퍼 함수에 전달하는 첫번째 인자는 `resources/views` 디렉토리에 있는 파일의 이름이 됩니다. 두번째 인자는 뷰-View에서 사용될 데이터의 배열입니다. 이 예제에서는 뷰-View에서 [Blade 문법](/docs/{{version}}/blade)을 통해서 보여지게 될 `name` 변수를 전달하고 있습니다.

<a name="nested-view-directories"></a>
### 중첩된 뷰 디렉토리

뷰-View는 `resources/views` 디렉토리의 중첩된 서브 디렉토리를 구성할 수 있습니다. 중첩된 뷰-View 파일을 참조하려면 "점"으로 구성된 표기법을 사용할 수 있습니다. 예를 들어 뷰-View 파일이 `resources/views/admin/profile.blade.php` 처럼 저장되었다면 route 또는 controller 중 하나에서 다음처럼 호출해야 합니다.

    return view('admin.profile', $data);

> {note} View 디렉토리 이름에는 `.` 문자가 포함되지 않아야합니다.

<a name="creating-the-first-available-view"></a>
### 사용가능한 첫 뷰 만들기

`View` 파사드의 `first` 메서드를 사용하여 주어진 뷰-View 배열에 존재하는 첫 번째 뷰-View를 생성할 수 있습니다. 이것은 애플리케이션이나 패키지에서 뷰-View를 사용자 정의하거나 덮어쓸 수 있도록 허용하는 경우에 유용할 수 있습니다.

    use Illuminate\Support\Facades\View;

    return View::first(['custom.admin', 'admin'], $data);

<a name="determining-if-a-view-exists"></a>
### 뷰가 있는지 확인하기

뷰파일이 존재하는지 확인해야 되는 경우, `View` 파사드를 사용할 수 있습니다. `exist` 메소드는 뷰 파일이 존재한다면 `true` 를 반환할 것입니다.

    use Illuminate\Support\Facades\View;

    if (View::exists('emails.customer')) {
        //
    }

<a name="passing-data-to-views"></a>
## 뷰에 데이터 전달하기

이전 예에서 보았듯이, 데이터 배열을 뷰-View에 전달하여 해당 데이터를 뷰-View에서 사용할 수 있도록 할 수 있습니다.

    return view('greetings', ['name' => 'Victoria']);

이러한 방식으로 정보를 전달할 때, 데이터는 키/값 쌍이 있는 배열이어야 합니다. 뷰-View에 데이터를 제공한 후, 데이터의 키를 사용하여 뷰-View 내의 각 값에 액세스할 수 있습니다. `<?php echo $name; ?>` 처럼.

완전한 데이터 배열을 `view` 헬퍼 함수에 전달하는 대신, `with` 메서드를 사용하여 개별 데이터 조각을 뷰-View에 추가할 수 있습니다. `with` 메서드는 뷰-View 객체의 인스턴스를 반환하므로 뷰-View를 반환하기 전에 메서드를 계속 연결할 수 있습니다.

    return view('greeting')
                ->with('name', 'Victoria')
                ->with('occupation', 'Astronaut');

<a name="sharing-data-with-all-views"></a>
### 모든 뷰에서 데이터 공유하기

경우에 따라, 애플리케이션에서 렌더링하는 모든 뷰-View 파일에 데이터를 공유해야 할 수도 있습니다. `View` 파사드의 `share` 메소드로 사용하여 공유할 수 있습니다. 일반적으로 서비스 공급자의 `boot` 메서드 내에서 `share` 메서드를 호출해야 합니다. `App\Providers\AppServiceProvider` 클래스에 추가하거나 이들을 수용할 별도의 서비스 제공자를 생성할 수 있습니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\View;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            //
        }

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            View::share('key', 'value');
        }
    }

<a name="view-composers"></a>
## 뷰 컴포저

뷰 컴포저는 뷰-View가 렌더링 될 때 호출되는 콜백 또는 클래스 메소드입니다. 만약 뷰-View가 렌더링 될 때마다 뷰-View에 전달해야할 데이터를 가지고 있다면 뷰 컴포저는 해당 로직을 한곳에서 구성할수 있게 도와줄 수 있습니다. 뷰 컴포저는 애플리케이션 내의 여러 route 또는 controller에서 동일한 뷰-View를 반환하고 항상 특정 데이터 조각이 필요한 경우에 특히 유용할 수 있습니다.

일반적으로 뷰 컴포저는 애플리케이션의 [서비스 프로바이더](/docs/{{version}}/providers) 중 하나에 등록됩니다. 이 예에서 우리는 이 로직을 수용하기 위해 새로운 `App\Providers\ViewServiceProvider`를 생성했다고 가정합니다.

뷰 컴포저를 등록하기 위해 `View` 파사드의 `composer` 메소드를 사용할 것입니다. Laravel은 클래스 기반 뷰 컴포저를 위한 기본 디렉토리를 포함하지 않으므로, 원하는 대로 구성할 수 있습니다. 예를 들어 `app/View/Composers` 디렉토리를 만들어 애플리케이션의 모든 뷰 컴포저를 저장할 수 있습니다.

    <?php

    namespace App\Providers;

    use App\View\Composers\ProfileComposer;
    use Illuminate\Support\Facades\View;
    use Illuminate\Support\ServiceProvider;

    class ViewServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            //
        }

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            // Using class based composers...
            View::composer('profile', ProfileComposer::class);

            // Using closure based composers...
            View::composer('dashboard', function ($view) {
                //
            });
        }
    }

> {note} 기억할 것은 여러분이 뷰 컴포저를 등록하기 위한 새로운 서비스 프로바이더를 생성했다면, `config/app.php` 설정 파일의 `providers` 배열에 이 서비스 프로바이더를 추가해야 한다는 것입니다.

뷰 컴포저를 등록했으므로 `profile` 뷰-View가 렌더링될 때마다 `App\View\Composers\ProfileComposer` 클래스의 `compose` 메서드가 실행됩니다. 뷰 컴포저 클래스의 예를 살펴보겠습니다.

    <?php

    namespace App\View\Composers;

    use App\Repositories\UserRepository;
    use Illuminate\View\View;

    class ProfileComposer
    {
        /**
         * The user repository implementation.
         *
         * @var \App\Repositories\UserRepository
         */
        protected $users;

        /**
         * Create a new profile composer.
         *
         * @param  \App\Repositories\UserRepository  $users
         * @return void
         */
        public function __construct(UserRepository $users)
        {
            // Dependencies are automatically resolved by the service container...
            $this->users = $users;
        }

        /**
         * Bind data to the view.
         *
         * @param  \Illuminate\View\View  $view
         * @return void
         */
        public function compose(View $view)
        {
            $view->with('count', $this->users->count());
        }
    }

보시다시피, 모든 뷰 컴포저는 [서비스 컨테이너](/docs/{{version}}/container)를 통해 해결되므로, 뷰 컴포저의 생성자 내에서 필요한 종속성을 입력할 수 있습니다.

<a name="attaching-a-composer-to-multiple-views"></a>
#### 컴포저를 다수의 뷰에 적용하기

`composer` 메소드의 첫번째 인자로 뷰 파일들의 배열을 전달하여, 뷰 컴포저가 적용될 다수의 뷰 파일들을 지정할 수 있습니다.

    use App\Views\Composers\MultiComposer;

    View::composer(
        ['profile', 'dashboard'],
        MultiComposer::class
    );

`composer` 메소드는 또한 `*` 와일드 캐릭터로 인자를 받을 수 있는데, 이렇게 하면 모든 뷰에 뷰컴포저를 지정하게 됩니다.

    View::composer('*', function ($view) {
        //
    });

<a name="view-creators"></a>
## 뷰 크리에이터

뷰 "크리에이터"는 뷰 컴포저와 거의 비슷하게 동작합니다. 하지만 뷰 크리에이터는 뷰-View가 렌더링 되기를 기다리는 대신 인스턴스화 된 다음에 바로 실행됩니다. 뷰 크리에이터를 등록하기 위해서는 `creator` 메소드를 사용하면 됩니다.

    use App\View\Creators\ProfileCreator;
    use Illuminate\Support\Facades\View;

    View::creator('profile', ProfileCreator::class);

<a name="optimizing-views"></a>
## 뷰 최적화

기본적으로 블레이드 템플릿 뷰-View를 불러올때 컴파일이 됩니다. 뷰-View를 렌더링하는 리퀘스트가 실행되면 라라벨은 뷰-View가 컴파일 된 버젼이 존재하는지 확인합니다. 컴파일 된 파일이 존재하면 라라벨은 컴파일되지 않은 뷰-View가 컴파일된 뷰-View보다 최근에 수정 되었는지를 판단 합니다. 컴파일된 뷰-View가 존재 하지 않거나 컴파일 되지 않은 뷰-View가 수정된 경우, 라라벨은 뷰-View를 다시 컴파일 합니다.

리퀘스트 요청 중에 뷰-View를 컴파일하면 성능에 약간의 부정적인 영향을 줄 수 있으므로, 라라벨은 `view:cache` Artisan 명령을 제공하여 응용 프로그램에서 사용하는 모든 뷰-View를 사전 컴파일 합니다. 성능 향상을 위해 다음의 명령어를 당신의 개발 과정에서 실행 할수 있습니다.

    php artisan view:cache

다음의 명령어로 뷰-View 캐시를 지울수 있습니다.

    php artisan view:clear
