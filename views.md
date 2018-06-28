# 뷰-Views

- [기본적인 사용법](#basic-usage)
    - [뷰에 데이터 전달하기](#passing-data-to-views)
    - [모든 뷰에서 데이터 공유하기](#sharing-data-with-all-views)
- [뷰 컴포저](#view-composers)

<a name="basic-usage"></a>
## 기본적인 사용법

뷰는 애플리케이션에서 제공하는 HTML 로 구성되어 있으며, 컨트롤러 / 애플리케이션 로직을 프리젠테이션 로직에서 분리하는 역할을 수행합니다. 뷰파일들은 `resources/views` 디렉토리에 위치합니다.

간단한 뷰 파일은 다음과 같은 형태입니다.

    <!-- View stored in resources/views/greeting.php -->

    <html>
        <body>
            <h1>Hello, <?php echo $name; ?></h1>
        </body>
    </html>

이 뷰파일이 `resources/views/greeting.php`로 저장되어 있다면, 여러분은 `view` 헬퍼 함수를 사용하여 반환할 수 있습니다.

    Route::get('/', function () {
        return view('greeting', ['name' => 'James']);
    });

보이는 바와 같이 `view` 헬퍼 함수에 전달하는 첫번째 인자는 `resources/views` 디렉토리에 있는 파일의 이름이 됩니다. 두번째 인자는 뷰에서 사용하기위한 데이터들의 배열입니다. 이 예제에서 뷰파일의 `echo` 에서 사용되는 변수로 활용하기 위한 `name` 변수를 전달하고 있습니다.

당연하게도 뷰는 `resources/views` 디렉토리의 중첩된 서브 디렉토리를 구성할 수 있습니다. 중첩된 뷰 파일을 참조하려면 "점"으로 구성된 표기법을 사용할 수 있습니다. 예를 들어 뷰파일이 `resources/views/admin/profile.php` 처럼 저장되었다면 다음처럼 호출해야 합니다.

    return view('admin.profile', $data);

#### 뷰파일이 존재하는지 확인하기

뷰파일이 존재하는지 확인해야 된다면, 인자 없이 `view` 헬퍼를 호출 한 다음에 `exists` 메소드를 사용하면 됩니다. 이 메소드는 뷰 파일이 디스크에 존재한다면 `true` 를 반환할 것입니다.

    if (view()->exists('emails.customer')) {
        //
    }

`view` 헬퍼가 인자 없이 호출 될때에는, `Illuminate\Contracts\View\Factory`의 인스턴스가 반환되기 때문에, 팩토리의 메소드를 자유롭게 사용할 수 있습니다.

<a name="view-data"></a>
### 뷰 데이터

<a name="passing-data-to-views"></a>
#### 뷰에 데이터 전달하기

이전 예제에서 확인하였듯이, 뷰에 데이터의 배열을 전달할 수 있습니다.

    return view('greetings', ['name' => 'Victoria']);

이러한 방식으로 정보를 전달할 때,`$data`는 키/값으로 구성된 배열이어야 합니다. 뷰 안에서 여러분은 `<?php echo $key; ?>` 와 같이 각각의 키에 해당하는 값에 엑세스 할 수 있습니다. 데이터를 `view` 헬퍼 함수에 전달하는 대신 `with` 메소드를 사용하여 뷰에 전달할 데이터를 개별적으로 추가 할 수도 있습니다.

    return view('greeting')->with('name', 'Victoria');

<a name="sharing-data-with-all-views"></a>
#### 모든 뷰파일에서 데이터 공유하기

때때로 애플리케이션에서 표시하는 모든 뷰에서 데이터를 공유할 필요가 있을 수도 있습니다. 여러분은 뷰의 팩토리에서 `share` 메소드를 사용하면 됩니다. 일반적으로 이 코드는 서비스 프로바이더의 `boot` 메소드에 구성해 놓아야 합니다. 여러분은 `AppServiceProvider`에 이 코드를 구성해 놓거나, 다른 분리된 서비스 프로바이더를 생성할 수도 있습니다.

    <?php

    namespace App\Providers;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            view()->share('key', 'value');
        }

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

<a name="view-composers"></a>
## 뷰 컴포저

뷰 컴포저는 뷰가 렌더링 될 때 호출되는 콜백 또는 클래스 메소드입니다. 만약 뷰가 렌더링 될 때마다 뷰에 전달해야할 데이터를 가지고 있다면 뷰 컴포저는 해당 로직을 한곳에서 구성할수 있게 도와줄 수 있습니다.

뷰 컴포저를 [서비스 프로바이더](/docs/{{version}}/providers)를 통해서 구성해 봅시다. `Illuminate\Contracts\View\Factory` contract 구현체에 엑세스 하기 위해서 `view` 헬퍼를 사용할 것입니다. 기억할 것은 라라벨은 뷰 컴포저를 위한 어떠한 기본적인 디렉토리도 포함하고 있지 않다는 것입니다. 여러분은 자유롭게 뷰 컴포저를 구성할 수 있습니다. 예를 들어 `App\Http\ViewComposers` 디렉토리를 새롭게 생성할 수 있습니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\ServiceProvider;

    class ComposerServiceProvider extends ServiceProvider
    {
        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function boot()
        {
            // Using class based composers...
            view()->composer(
                'profile', 'App\Http\ViewComposers\ProfileComposer'
            );

            // Using Closure based composers...
            view()->composer('dashboard', function ($view) {
                //
            });
        }

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

기억할 것은 여러분이 뷰 컴포저를 등록하기 위한 새로운 서비스 프로바이더를 생성했다면, `config/app.php` 설정 파일의 `providers` 배열에 이 서비스 프로바이더를 추가해야 한다는 것입니다.

이제 뷰 컴포저를 등록했다면 `profile` 뷰가 렌더링 될 때마다 `ProfileComposer@compose` 메소드가 실행될 것입니다. 이제 컴포저 클래스를 정의해봅시다.

    <?php

    namespace App\Http\ViewComposers;

    use Illuminate\View\View;
    use App\Repositories\UserRepository;

    class ProfileComposer
    {
        /**
         * The user repository implementation.
         *
         * @var UserRepository
         */
        protected $users;

        /**
         * Create a new profile composer.
         *
         * @param  UserRepository  $users
         * @return void
         */
        public function __construct(UserRepository $users)
        {
            // Dependencies automatically resolved by service container...
            $this->users = $users;
        }

        /**
         * Bind data to the view.
         *
         * @param  View  $view
         * @return void
         */
        public function compose(View $view)
        {
            $view->with('count', $this->users->count());
        }
    }

뷰가 렌더링되기 전에 뷰컴포저의 `composer` 메소드가 `Illuminate\View\View` 인스턴스와 함께 호출됩니다. 데이터를 전달하기 위해서 `with` 메소드를 사용할 수 있습니다.

> **참고 :** 모든 뷰 컴포저의 의존성 주입은 [service container](/docs/{{version}}/container)를 통해서 이루어 집니다. 그렇기 때문에 필요한 객체의 경우 뷰 컴포저의 생성자에서 타입힌트를 지정한 형태로 지정하면 됩니다.

#### 컴포저를 다수의 뷰에 적용하기

`composer` 메소드의 첫번째 인자로 뷰 파일들의 배열을 전달하여, 뷰 컴포저가 적용될 다수의 뷰 파일들을 지정할 수 있습니다.

    view()->composer(
        ['profile', 'dashboard'],
        'App\Http\ViewComposers\MyViewComposer'
    );

`composer` 메소드는 `*` 와일드 캐릭터로 인자를 받을 수 있는데, 이렇게 하면 모든 뷰에 뷰컴포저를 지정하게 됩니다.

    view()->composer('*', function ($view) {
        //
    });

### 뷰 크리에이터

뷰 **크리에이터**는 뷰 컴포저와 거의 비슷하게 동작합니다. 하지만 뷰 크리에이터는 뷰가 렌더링 되기를 기다리는 대신 인스턴스화 되는 즉시 실행됩니다. 뷰 크리에이터를 등록하기 위해서는 `creator` 메소드를 사용하면 됩니다.

    view()->creator('profile', 'App\Http\ViewCreators\ProfileCreator');
