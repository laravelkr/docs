# Views
# 뷰-Views

- [Creating Views](#creating-views)
- [뷰 생성하기](#creating-views)
- [Passing Data To Views](#passing-data-to-views)
- [뷰에 데이터 전달하기](#passing-data-to-views)
    - [Sharing Data With All Views](#sharing-data-with-all-views)
    - [모든 뷰에서 데이터 공유하기](#sharing-data-with-all-views)
- [View Composers](#view-composers)
- [뷰 컴포저](#view-composers)
- [Optimizing Views](#optimizing-views)
- [뷰 최적화](#optimizing-views)

<a name="creating-views"></a>
## Creating Views
## 뷰 생성하기

> {tip} Looking for more information on how to write Blade templates? Check out the full [Blade documentation](/docs/{{version}}/blade) to get started.

> {tip} 블레이드 템플릿을 작성하는 방법에 대한 자세한 정보는 [블레이드 문서](/docs/{{version}}/blade)를 확인하십시오.

Views contain the HTML served by your application and separate your controller / application logic from your presentation logic. Views are stored in the `resources/views` directory. A simple view might look something like this:

뷰는 애플리케이션에서 제공하는 HTML 로 구성되어 있으며, 컨트롤러 / 애플리케이션 로직을 프리젠테이션 로직에서 분리하는 역할을 수행합니다. 뷰파일들은 `resources/views` 디렉토리에 위치합니다. 간단한 뷰의 경우 다음처럼 보일 것입니다.

    <!-- View stored in resources/views/greeting.blade.php -->

    <html>
        <body>
            <h1>Hello, {{ $name }}</h1>
        </body>
    </html>

Since this view is stored at `resources/views/greeting.blade.php`, we may return it using the global `view` helper like so:

이 뷰파일이 `resources/views/greeting.blade.php`로 저장되어 있다면, 여러분은 `view` 헬퍼를 사용하여 반환할 수 있습니다.

    Route::get('/', function () {
        return view('greeting', ['name' => 'James']);
    });

As you can see, the first argument passed to the `view` helper corresponds to the name of the view file in the `resources/views` directory. The second argument is an array of data that should be made available to the view. In this case, we are passing the `name` variable, which is displayed in the view using [Blade syntax](/docs/{{version}}/blade).

보이는 바와 같이 `view` 헬퍼 함수에 전달하는 첫번째 인자는 `resources/views` 디렉토리에 있는 파일의 이름이 됩니다. 두번째 인자는 뷰에서 사용될 데이터의 배열입니다. 이 예제에서는 뷰에서 [블레이드 문법](/docs/{{version}}/blade)을 통해서 보여지게 될 `name` 변수를 전달하고 있습니다.

Views may also be nested within subdirectories of the `resources/views` directory. "Dot" notation may be used to reference nested views. For example, if your view is stored at `resources/views/admin/profile.blade.php`, you may reference it like so:

뷰는 `resources/views` 디렉토리의 중첩된 서브 디렉토리를 구성할 수 있습니다. 중첩된 뷰 파일을 참조하려면 "점"으로 구성된 표기법을 사용할 수 있습니다. 예를 들어 뷰파일이 `resources/views/admin/profile.blade.php` 처럼 저장되었다면 다음처럼 호출해야 합니다.

    return view('admin.profile', $data);

> {note} View directory names should not contain the `.` character.

> {note} View 디렉토리 이름에는 `.` 문자가 포함되지 않아야합니다.

#### Determining If A View Exists
#### 뷰파일이 존재하는지 확인하기

If you need to determine if a view exists, you may use the `View` facade. The `exists` method will return `true` if the view exists:

뷰파일이 존재하는지 확인해야 되는 경우, `View` 파사드를 사용할 수 있습니다. `exist` 메소드는 뷰 파일이 존재한다면 `true` 를 반환할 것입니다.

    use Illuminate\Support\Facades\View;

    if (View::exists('emails.customer')) {
        //
    }

#### Creating The First Available View
#### 먼저 사용가능한 뷰 파일 사용하기

Using the `first` method, you may create the first view that exists in a given array of views. This is useful if your application or package allows views to be customized or overwritten:

`first` 메소드를 사용하면, 주어진 배열에 있는 뷰 중에서 사용가능한 처음의 뷰를 사용 할 수 있습니다. 이는 애플리케이션 또는 패키지에서 뷰파일을 커스마이징 하거나, 덮어 쓸 필요가 있는 경우 유용합니다.

    return view()->first(['custom.admin', 'admin'], $data);

You may also call this method via the `View` [facade](/docs/{{version}}/facades):

`View` [파사드](/docs/{{version}}/facades)를 통해서도 이 메소드를 호출할 수 있습니다.

    use Illuminate\Support\Facades\View;

    return View::first(['custom.admin', 'admin'], $data);

<a name="passing-data-to-views"></a>
## Passing Data To Views
## 뷰에 데이터 전달하기

As you saw in the previous examples, you may pass an array of data to views:

이전 예제에서 확인하였듯이, 뷰에 데이터의 배열을 전달할 수 있습니다.

    return view('greetings', ['name' => 'Victoria']);

When passing information in this manner, the data should be an array with key / value pairs. Inside your view, you can then access each value using its corresponding key, such as `<?php echo $key; ?>`. As an alternative to passing a complete array of data to the `view` helper function, you may use the `with` method to add individual pieces of data to the view:

이러한 방식으로 정보를 전달할 때, 데이터는 키/값으로 구성된 배열이어야 합니다. 뷰 안에서 여러분은 `<?php echo $key; ?>` 와 같이 각각의 키에 해당하는 값에 엑세스 할 수 있습니다. 데이터를 `view` 헬퍼 함수에 전달하는 대신 `with` 메소드를 사용하여 뷰에 전달할 데이터를 개별적으로 추가 할 수도 있습니다.

    return view('greeting')->with('name', 'Victoria');

<a name="sharing-data-with-all-views"></a>
#### Sharing Data With All Views
#### 모든 뷰파일에서 데이터 공유하기

Occasionally, you may need to share a piece of data with all views that are rendered by your application. You may do so using the view facade's `share` method. Typically, you should place calls to `share` within a service provider's `boot` method. You are free to add them to the `AppServiceProvider` or generate a separate service provider to house them:

때때로 애플리케이션에서 표시하는 모든 뷰에서 데이터를 공유할 필요가 있을 수도 있습니다. 여러분은 뷰 파사드의 `share` 메소드를 사용하면 됩니다. 일반적으로 이 코드는 서비스 프로바이더의 `boot` 메소드에 구성해 놓아야 합니다. 여러분은 `AppServiceProvider`에 이 코드를 구성해 놓거나, 다른 분리된 서비스 프로바이더를 생성할 수도 있습니다.

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
## View Composers
## 뷰 컴포저

View composers are callbacks or class methods that are called when a view is rendered. If you have data that you want to be bound to a view each time that view is rendered, a view composer can help you organize that logic into a single location.

뷰 컴포저는 뷰가 렌더링 될 때 호출되는 콜백 또는 클래스 메소드입니다. 만약 뷰가 렌더링 될 때마다 뷰에 전달해야할 데이터를 가지고 있다면 뷰 컴포저는 해당 로직을 한곳에서 구성할수 있게 도와줄 수 있습니다.

For this example, let's register the view composers within a [service provider](/docs/{{version}}/providers). We'll use the `View` facade to access the underlying `Illuminate\Contracts\View\Factory` contract implementation. Remember, Laravel does not include a default directory for view composers. You are free to organize them however you wish. For example, you could create an `app/Http/View/Composers` directory:

예를 들어, 뷰 컴포저를 [서비스 프로바이더](/docs/{{version}}/providers)를 통해서 구성해 봅시다. `Illuminate\Contracts\View\Factory` contract 구현체에 엑세스 하기 위해서 `View` 파사드를 사용할 것입니다. 기억할 것은 라라벨은 뷰 컴포저를 위한 어떠한 기본적인 디렉토리도 포함하고 있지 않다는 것입니다. 여러분은 자유롭게 뷰 컴포저를 구성할 수 있습니다. 예를 들어 `app/Http/View/Composers` 디렉토리를 새롭게 생성할 수 있습니다.

    <?php

    namespace App\Providers;

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
            View::composer(
                'profile', 'App\Http\View\Composers\ProfileComposer'
            );

            // Using Closure based composers...
            View::composer('dashboard', function ($view) {
                //
            });
        }
    }

> {note} Remember, if you create a new service provider to contain your view composer registrations, you will need to add the service provider to the `providers` array in the `config/app.php` configuration file.

> {note} 기억할 것은 여러분이 뷰 컴포저를 등록하기 위한 새로운 서비스 프로바이더를 생성했다면, `config/app.php` 설정 파일의 `providers` 배열에 이 서비스 프로바이더를 추가해야 한다는 것입니다.

Now that we have registered the composer, the `ProfileComposer@compose` method will be executed each time the `profile` view is being rendered. So, let's define the composer class:

이제 뷰 컴포저를 등록했다면 `profile` 뷰가 렌더링 될 때마다 `ProfileComposer@compose` 메소드가 실행될 것입니다. 이제 컴포저 클래스를 정의해봅시다.

    <?php

    namespace App\Http\View\Composers;

    use App\Repositories\UserRepository;
    use Illuminate\View\View;

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

Just before the view is rendered, the composer's `compose` method is called with the `Illuminate\View\View` instance. You may use the `with` method to bind data to the view.

뷰가 렌더링되기 전에 뷰컴포저의 `composer` 메소드가 `Illuminate\View\View` 인스턴스와 함께 호출됩니다. 데이터를 전달하기 위해서 `with` 메소드를 사용할 수 있습니다.

> {tip} All view composers are resolved via the [service container](/docs/{{version}}/container), so you may type-hint any dependencies you need within a composer's constructor.

> {tip} 모든 뷰 컴포저의 의존성 주입은 [service container](/docs/{{version}}/container) 를 통해서 이루어 집니다. 그렇기 때문에 필요한 객체의 경우 뷰 컴포저의 생성자에서 타입힌트를 지정한 형태로 지정하면 됩니다.

#### Attaching A Composer To Multiple Views
#### 컴포저를 다수의 뷰에 적용하기

You may attach a view composer to multiple views at once by passing an array of views as the first argument to the `composer` method:

`composer` 메소드의 첫번째 인자로 뷰 파일들의 배열을 전달하여, 뷰 컴포저가 적용될 다수의 뷰 파일들을 지정할 수 있습니다.

    View::composer(
        ['profile', 'dashboard'],
        'App\Http\View\Composers\MyViewComposer'
    );

The `composer` method also accepts the `*` character as a wildcard, allowing you to attach a composer to all views:

`composer` 메소드는 또한 `*` 와일드 캐릭터로 인자를 받을 수 있는데, 이렇게 하면 모든 뷰에 뷰컴포저를 지정하게 됩니다.

    View::composer('*', function ($view) {
        //
    });

#### View Creators
#### 뷰 크리에이터

View **creators** are very similar to view composers; however, they are executed immediately after the view is instantiated instead of waiting until the view is about to render. To register a view creator, use the `creator` method:

뷰 **크리에이터**는 뷰 컴포저와 거의 비슷하게 동작합니다. 하지만 뷰 크리에이터는 뷰가 렌더링 되기를 기다리는 대신 인스턴스화 된 다음에 바로 실행됩니다. 뷰 크리에이터를 등록하기 위해서는 `creator` 메소드를 사용하면 됩니다.

    View::creator('profile', 'App\Http\View\Creators\ProfileCreator');
    
<a name="optimizing-views"></a>
## Optimizing Views
## 뷰 최적화

By default, views are compiled on demand. When a request is executed that renders a view, Laravel will determine if a compiled version of the view exists. If the file exists, Laravel will then determine if the uncompiled view has been modified more recently than the compiled view. If the compiled view either does not exist, or the uncompiled view has been modified, Laravel will recompile the view.

기본적으로 뷰를 불러올때 컴파일이 됩니다. 뷰를 렌더링하는 리퀘스트가 실행되면 라라벨은 뷰가 컴파일 된 버젼이 존재하는지 확인합니다. 컴파일 된 파일이 존재하면 라라벨은 컴파일되지 않은 뷰가 컴파일된 뷰보다 최근에 수정 되었는지를 판단 합니다. 컴파일된 뷰가 존재 하지 않거나 컴파일 되지 않은 뷰가 수정된 경우, 라라벨은 뷰를 다시 컴파일 합니다.

Compiling views during the request negatively impacts performance, so Laravel provides the `view:cache` Artisan command to precompile all of the views utilized by your application. For increased performance, you may wish to run this command as part of your deployment process:

리퀘스트 요청중에 뷰를 컴파일하면 성능에 부정적인 영향을 미치므로 라라벨은 `view:cache` Artisan 명령을 제공하여 응용 프로그램에서 사용하는 모든 뷰를 사전 컴파일 합니다. 성능 향상을 위해 다음의 명령어를 당신의 개발 과정에서 실행 할수 있습니다.

    php artisan view:cache

You may use the `view:clear` command to clear the view cache:

다음의 명령어로 뷰 캐시를 지울수 있습니다.

    php artisan view:clear

Typically, you can assume that views will **never** be modified in your production environment. Therefore, you can usually disable the file modification checks Laravel makes to determine if views are expired by modifying the `expires` option within your `view` configuration file:

일반적으로 프로덕션 환경에서는 뷰가 **절대** 수정 되지 않는다고 가정 할 수 있습니다. 따라서 보통은 `view` 구성 파일내의 옵션을 수정하여 라라벨이 뷰가 만료 되었는지 확인하기 위해 파일 수정 검사를 비활성화 할 수 있습니다.

    'expires' => env('APP_ENV') !== 'production',

> {note} You must run `php artisan view:cache` during your deployment process to disable checking for expired views.

> {note} 만료된 뷰 확인을 비활성화 하려면 개발과정 중에 `php artisan view:cache` 실행해야 합니다.
