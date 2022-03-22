# Views
# 뷰-Views

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Creating & Rendering Views](#creating-and-rendering-views)
- [뷰파일 생성 & 렌더](#creating-and-rendering-views)
  - [Nested View Directories](#nested-view-directories)
  - [중첩된 뷰 디렉토리](#nested-view-directories)
  - [Creating The First Available View](#creating-the-first-available-view)
  - [사용가능한 첫 뷰 만들기](#creating-the-first-available-view)
  - [Determining If A View Exists](#determining-if-a-view-exists)
  - [뷰가 있는지 확인하기](#determining-if-a-view-exists)
- [Passing Data To Views](#passing-data-to-views)
- [뷰에 데이터 전달하기](#passing-data-to-views)
  - [Sharing Data With All Views](#sharing-data-with-all-views)
  - [모든 뷰에서 데이터 공유하기](#sharing-data-with-all-views)
- [View Composers](#view-composers)
- [뷰 컴포저](#view-composers)
  - [View Creators](#view-creators)
  - [뷰 작성자](#view-creators)
- [Optimizing Views](#optimizing-views)
- [뷰 최적화](#optimizing-views)

<a name="introduction"></a>
## Introduction
## 시작하기

Of course, it's not practical to return entire HTML documents strings directly from your routes and controllers. Thankfully, views provide a convenient way to place all of our HTML in separate files. Views separate your controller / application logic from your presentation logic and are stored in the `resources/views` directory. A simple view might look something like this:

물론 route와 controller에서 직접 전체 HTML 문서 문자열을 반환하는 것은 현실적이지 않습니다. 고맙게도 뷰-View는 모든 HTML을 별도의 파일에 배치할 수 있는 편리한 방법을 제공합니다. 뷰-View는 컨트롤러 / 애플리케이션 로직을 프레젠테이션 로직과 분리하고 `resources/views` 디렉토리에 저장됩니다. 간단한 뷰-View 파일의 경우 다음처럼 보일 것입니다.

```blade
<!-- View stored in resources/views/greeting.blade.php -->

<html>
    <body>
        <h1>Hello, {{ $name }}</h1>
    </body>
</html>
```

Since this view is stored at `resources/views/greeting.blade.php`, we may return it using the global `view` helper like so:

뷰-View 파일이 `resources/views/greeting.blade.php`에 저장되어 있으므로, 다음과 같이 전역 `view` 헬퍼를 사용하여 반환할 수 있습니다.

    Route::get('/', function () {
        return view('greeting', ['name' => 'James']);
    });

> {tip} Looking for more information on how to write Blade templates? Check out the full [Blade documentation](/docs/{{version}}/blade) to get started.

> {tip} blade 템플릿 작성 방법에 대한 자세한 정보를 찾고 계십니까? 시작하려면 전체 [blade 문서](/docs/{{version}}/blade)를 확인하세요.

<a name="creating-and-rendering-views"></a>
## Creating & Rendering Views
## 뷰파일 생성 & 렌더

You may create a view by placing a file with the `.blade.php` extension in your application's `resources/views` directory. The `.blade.php` extension informs the framework that the file contains a [Blade template](/docs/{{version}}/blade). Blade templates contain HTML as well as Blade directives that allow you to easily echo values, create "if" statements, iterate over data, and more.

애플리케이션의 `resources/views` 디렉토리에 확장자가 `.blade.php`인 파일을 배치하여 뷰-View 파일을 생성할 수 있습니다. `.blade.php` 확장자는 파일에 [blade 템플릿](/docs/{{version}}/blade)이 포함되어 있음을 프레임워크에 알립니다. blade 템플릿에는 HTML과 blade 지시문이 포함되어 있어 값을 쉽게 출력하고, "if" 문을 만들고, 데이터를 반복하는 등의 작업을 수행할 수 있습니다.

Once you have created a view, you may return it from one of your application's routes or controllers using the global `view` helper:

뷰-View 파일을 생성하고 나면, 전역 `view` 헬퍼를 사용하여 애플리케이션의 route 또는 controller 중 하나에서 뷰-View를 반환할 수 있습니다.

    Route::get('/', function () {
        return view('greeting', ['name' => 'James']);
    });

Views may also be returned using the `View` facade:

뷰-View는 `View` 파사드를 사용하여 반환될 수도 있습니다.

    use Illuminate\Support\Facades\View;

    return View::make('greeting', ['name' => 'James']);

As you can see, the first argument passed to the `view` helper corresponds to the name of the view file in the `resources/views` directory. The second argument is an array of data that should be made available to the view. In this case, we are passing the `name` variable, which is displayed in the view using [Blade syntax](/docs/{{version}}/blade).

보이는 바와 같이 `view` 헬퍼 함수에 전달하는 첫번째 인자는 `resources/views` 디렉토리에 있는 파일의 이름이 됩니다. 두번째 인자는 뷰-View에서 사용될 데이터의 배열입니다. 이 예제에서는 뷰-View에서 [Blade 문법](/docs/{{version}}/blade)을 통해서 보여지게 될 `name` 변수를 전달하고 있습니다.

<a name="nested-view-directories"></a>
### Nested View Directories
### 중첩된 뷰 디렉토리

Views may also be nested within subdirectories of the `resources/views` directory. "Dot" notation may be used to reference nested views. For example, if your view is stored at `resources/views/admin/profile.blade.php`, you may return it from one of your application's routes / controllers like so:

뷰-View는 `resources/views` 디렉토리의 중첩된 서브 디렉토리를 구성할 수 있습니다. 중첩된 뷰-View 파일을 참조하려면 "점"으로 구성된 표기법을 사용할 수 있습니다. 예를 들어 뷰-View 파일이 `resources/views/admin/profile.blade.php` 처럼 저장되었다면 route 또는 controller 중 하나에서 다음처럼 호출해야 합니다.

    return view('admin.profile', $data);

> {note} View directory names should not contain the `.` character.

> {note} View 디렉토리 이름에는 `.` 문자가 포함되지 않아야합니다.

<a name="creating-the-first-available-view"></a>
### Creating The First Available View
### 사용가능한 첫 뷰 만들기

Using the `View` facade's `first` method, you may create the first view that exists in a given array of views. This may be useful if your application or package allows views to be customized or overwritten:

`View` 파사드의 `first` 메서드를 사용하여 주어진 뷰-View 배열에 존재하는 첫 번째 뷰-View를 생성할 수 있습니다. 이것은 애플리케이션이나 패키지에서 뷰-View를 사용자 정의하거나 덮어쓸 수 있도록 허용하는 경우에 유용할 수 있습니다.

    use Illuminate\Support\Facades\View;

    return View::first(['custom.admin', 'admin'], $data);

<a name="determining-if-a-view-exists"></a>
### Determining If A View Exists
### 뷰가 있는지 확인하기

If you need to determine if a view exists, you may use the `View` facade. The `exists` method will return `true` if the view exists:

뷰파일이 존재하는지 확인해야 되는 경우, `View` 파사드를 사용할 수 있습니다. `exist` 메소드는 뷰 파일이 존재한다면 `true` 를 반환할 것입니다.

    use Illuminate\Support\Facades\View;

    if (View::exists('emails.customer')) {
        //
    }

<a name="passing-data-to-views"></a>
## Passing Data To Views
## 뷰에 데이터 전달하기

As you saw in the previous examples, you may pass an array of data to views to make that data available to the view:

이전 예에서 보았듯이, 데이터 배열을 뷰-View에 전달하여 해당 데이터를 뷰-View에서 사용할 수 있도록 할 수 있습니다.

    return view('greetings', ['name' => 'Victoria']);

When passing information in this manner, the data should be an array with key / value pairs. After providing data to a view, you can then access each value within your view using the data's keys, such as `<?php echo $name; ?>`.

이러한 방식으로 정보를 전달할 때, 데이터는 키/값 쌍이 있는 배열이어야 합니다. 뷰-View에 데이터를 제공한 후, 데이터의 키를 사용하여 뷰-View 내의 각 값에 액세스할 수 있습니다. `<?php echo $name; ?>` 처럼.

As an alternative to passing a complete array of data to the `view` helper function, you may use the `with` method to add individual pieces of data to the view. The `with` method returns an instance of the view object so that you can continue chaining methods before returning the view:

완전한 데이터 배열을 `view` 헬퍼 함수에 전달하는 대신, `with` 메서드를 사용하여 개별 데이터 조각을 뷰-View에 추가할 수 있습니다. `with` 메서드는 뷰-View 객체의 인스턴스를 반환하므로 뷰-View를 반환하기 전에 메서드를 계속 연결할 수 있습니다.

    return view('greeting')
                ->with('name', 'Victoria')
                ->with('occupation', 'Astronaut');

<a name="sharing-data-with-all-views"></a>
### Sharing Data With All Views
### 모든 뷰에서 데이터 공유하기

Occasionally, you may need to share data with all views that are rendered by your application. You may do so using the `View` facade's `share` method. Typically, you should place calls to the `share` method within a service provider's `boot` method. You are free to add them to the `App\Providers\AppServiceProvider` class or generate a separate service provider to house them:

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
## View Composers
## 뷰 컴포저

View composers are callbacks or class methods that are called when a view is rendered. If you have data that you want to be bound to a view each time that view is rendered, a view composer can help you organize that logic into a single location. View composers may prove particularly useful if the same view is returned by multiple routes or controllers within your application and always needs a particular piece of data.

뷰 컴포저는 뷰-View가 렌더링 될 때 호출되는 콜백 또는 클래스 메소드입니다. 만약 뷰-View가 렌더링 될 때마다 뷰-View에 전달해야할 데이터를 가지고 있다면 뷰 컴포저는 해당 로직을 한곳에서 구성할수 있게 도와줄 수 있습니다. 뷰 컴포저는 애플리케이션 내의 여러 route 또는 controller에서 동일한 뷰-View를 반환하고 항상 특정 데이터 조각이 필요한 경우에 특히 유용할 수 있습니다.

Typically, view composers will be registered within one of your application's [service providers](/docs/{{version}}/providers). In this example, we'll assume that we have created a new `App\Providers\ViewServiceProvider` to house this logic.

일반적으로 뷰 컴포저는 애플리케이션의 [서비스 프로바이더](/docs/{{version}}/providers) 중 하나에 등록됩니다. 이 예에서 우리는 이 로직을 수용하기 위해 새로운 `App\Providers\ViewServiceProvider`를 생성했다고 가정합니다.

We'll use the `View` facade's `composer` method to register the view composer. Laravel does not include a default directory for class based view composers, so you are free to organize them however you wish. For example, you could create an `app/View/Composers` directory to house all of your application's view composers:

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

> {note} Remember, if you create a new service provider to contain your view composer registrations, you will need to add the service provider to the `providers` array in the `config/app.php` configuration file.

> {note} 기억할 것은 여러분이 뷰 컴포저를 등록하기 위한 새로운 서비스 프로바이더를 생성했다면, `config/app.php` 설정 파일의 `providers` 배열에 이 서비스 프로바이더를 추가해야 한다는 것입니다.

Now that we have registered the composer, the `compose` method of the `App\View\Composers\ProfileComposer` class will be executed each time the `profile` view is being rendered. Let's take a look at an example of the composer class:

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

As you can see, all view composers are resolved via the [service container](/docs/{{version}}/container), so you may type-hint any dependencies you need within a composer's constructor.

보시다시피, 모든 뷰 컴포저는 [서비스 컨테이너](/docs/{{version}}/container)를 통해 해결되므로, 뷰 컴포저의 생성자 내에서 필요한 종속성을 입력할 수 있습니다.

<a name="attaching-a-composer-to-multiple-views"></a>
#### Attaching A Composer To Multiple Views
#### 컴포저를 다수의 뷰에 적용하기

You may attach a view composer to multiple views at once by passing an array of views as the first argument to the `composer` method:

`composer` 메소드의 첫번째 인자로 뷰 파일들의 배열을 전달하여, 뷰 컴포저가 적용될 다수의 뷰 파일들을 지정할 수 있습니다.

    use App\Views\Composers\MultiComposer;

    View::composer(
        ['profile', 'dashboard'],
        MultiComposer::class
    );

The `composer` method also accepts the `*` character as a wildcard, allowing you to attach a composer to all views:

`composer` 메소드는 또한 `*` 와일드 캐릭터로 인자를 받을 수 있는데, 이렇게 하면 모든 뷰에 뷰컴포저를 지정하게 됩니다.

    View::composer('*', function ($view) {
        //
    });

<a name="view-creators"></a>
### View Creators
## 뷰 크리에이터

View "creators" are very similar to view composers; however, they are executed immediately after the view is instantiated instead of waiting until the view is about to render. To register a view creator, use the `creator` method:

뷰 "크리에이터"는 뷰 컴포저와 거의 비슷하게 동작합니다. 하지만 뷰 크리에이터는 뷰-View가 렌더링 되기를 기다리는 대신 인스턴스화 된 다음에 바로 실행됩니다. 뷰 크리에이터를 등록하기 위해서는 `creator` 메소드를 사용하면 됩니다.

    use App\View\Creators\ProfileCreator;
    use Illuminate\Support\Facades\View;

    View::creator('profile', ProfileCreator::class);

<a name="optimizing-views"></a>
## Optimizing Views
## 뷰 최적화

By default, Blade template views are compiled on demand. When a request is executed that renders a view, Laravel will determine if a compiled version of the view exists. If the file exists, Laravel will then determine if the uncompiled view has been modified more recently than the compiled view. If the compiled view either does not exist, or the uncompiled view has been modified, Laravel will recompile the view.

기본적으로 블레이드 템플릿 뷰-View를 불러올때 컴파일이 됩니다. 뷰-View를 렌더링하는 리퀘스트가 실행되면 라라벨은 뷰-View가 컴파일 된 버젼이 존재하는지 확인합니다. 컴파일 된 파일이 존재하면 라라벨은 컴파일되지 않은 뷰-View가 컴파일된 뷰-View보다 최근에 수정 되었는지를 판단 합니다. 컴파일된 뷰-View가 존재 하지 않거나 컴파일 되지 않은 뷰-View가 수정된 경우, 라라벨은 뷰-View를 다시 컴파일 합니다.

Compiling views during the request may have a small negative impact on performance, so Laravel provides the `view:cache` Artisan command to precompile all of the views utilized by your application. For increased performance, you may wish to run this command as part of your deployment process:

리퀘스트 요청 중에 뷰-View를 컴파일하면 성능에 약간의 부정적인 영향을 줄 수 있으므로, 라라벨은 `view:cache` Artisan 명령을 제공하여 응용 프로그램에서 사용하는 모든 뷰-View를 사전 컴파일 합니다. 성능 향상을 위해 다음의 명령어를 당신의 개발 과정에서 실행 할수 있습니다.

```shell
php artisan view:cache
```

You may use the `view:clear` command to clear the view cache:

다음의 명령어로 뷰-View 캐시를 지울수 있습니다.

```shell
php artisan view:clear
```
