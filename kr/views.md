# Views 뷰

- [Basic Usage 기본 사용법](#basic-usage)
- [View Composers 뷰 컴포저](#view-composers)

<a name="basic-usage"></a>
## Basic Usage 기본 사용법

Views contain the HTML served by your application, and serve as a convenient method of separating your controller and domain logic from your presentation logic. 뷰는 응용 프로그램에서 제공하는 HTML을 포함하고 프리젠 테이션 로직 컨트롤러 및 도메인 로직을 분리하는 편리한 방법을 제공합니다. Views are stored in the `resources/views` directory. 뷰파일들은 `resources/views` 디렉토리에 위치합니다. 

A simple view looks like this:
간단한 뷰는 다음과 같습니다. 

	<!-- View stored in resources/views/greeting.php -->

	<html>
		<body>
			<h1>Hello, <?php echo $name; ?></h1>
		</body>
	</html>

The view may be returned to the browser like so:
뷰는 다음과 같이 브라우저로 보내집니다. 

	Route::get('/', function()
	{
		return view('greeting', ['name' => 'James']);
	});

As you can see, the first argument passed to the `view` helper corresponds to the name of the view file in the `resources/views` directory.  보는바와 같이 `view` 헬퍼 함수에 전달하는 첫번째 인자는 `resources/views` 디렉토리에 있는 파일의 이름이 됩니다. The second argument passed to helper is an array of data that should be made available to the view. 두번째 전달 인자는 뷰에서 사용하기위한 데이터의 배열입니다. 

Of course, views may also be nested within sub-directories of the `resources/views` directory. 당연하게도 뷰는 `resources/views` 디렉토리의 중첩된 서브 디렉토리를 구성할 수 있습니다. For example, if your view is stored at `resources/views/admin/profile.php`, it should be returned like so: 예를들어 뷰파일이 `resources/views/admin/profile.php` 처럼 저장되었다면 다음처럼 호출해야 합니다. 

	return view('admin.profile', $data);

#### Passing Data To Views 뷰에 데이터 전달하기

	// Using conventional approach
	$view = view('greeting')->with('name', 'Victoria');

	// Using Magic Methods
	$view = view('greeting')->withName('Victoria');

In the example above, the variable `$name` is made accessible to the view and contains `Victoria`. 위 예제의 경우 뷰에서는 `$name` 변수에 `Victoria`라는 값을 확인할 수 있습니다.

If you wish, you may pass an array of data as the second parameter to the `view` helper:
필요한 경우에 `view` 헬퍼 함수에 두번째 인자로 데이터 배열을 전달할 수도 있습니다. 

	$view = view('greetings', $data);
	
When passing information in this manner, `$data` should be an array with key/value pairs. Inside your view, you can then access each value using it's corresponding key, like `{{ $key }}` (assuming `$data['key']` exists).

이러한 방식으로 정보를 전달할 때,`$data`는 키/값으로 구성된 배열이어야 합니다. 뷰 안에서 여러분은 `{{ $key }}` 와 같이 각각의 키에 해당하는 값에 엑세스 할 수 있습니다. (`$data['key']`는 존재한다고 가정합니다. )

#### Sharing Data With All Views 모든 뷰에서 데이터 공유하기

Occasionally, you may need to share a piece of data with all views that are rendered by your application. 때때로 애플리케이션에서 표시하는 모든 뷰에서 데이터를 공유할 필요가 있을 수도 있습니다. You have several options: the `view` helper, the `Illuminate\Contracts\View\Factory` [contract](/docs/{{version}}/contracts), or a wildcard [view composer](#view-composers). 이 경우 몇가지의 옵션이 있습니다. `view` 헬퍼 함수를 사용하거나 `Illuminate\Contracts\View\Factory` [contract](/docs/{{version}}/contracts)를 이용하는 법, 또는 와일드 카드의 [view composer](#view-composers)를 통하는 방법입니다. 

For example, using the `view` helper:
`view` 헬퍼함수를 이용하는 예제입니다. 

	view()->share('data', [1, 2, 3]);

You may also use the `View` facade:
`View` 파사드를 사용할 수도 있습니다. 

	View::share('data', [1, 2, 3]);

Typically, you would place calls to the `share` method within a service provider's `boot` method. You are free to add them to the `AppServiceProvider` or generate a separate service provider to house them. 일반적으로 `share` 메소드는 서비스 프로바이더의 `boot` 메소드 안에서 호출합니다.  `AppServiceProvider` 에서 편하게 추가할수도 있고, 다른 별도의 서비스 프로바이더를 생성하고 구성할 수도 있습니다. 

> **Note 참고:** When the `view` helper is called without arguments, it returns an implementation of the `Illuminate\Contracts\View\Factory` contract. `view` 헬퍼 함수를 전달 인자 없이 호출하는 경우에는 반환값은 `Illuminate\Contracts\View\Factory` contract의 구현체가 됩니다. 

#### Determining If A View Exists 뷰가 존재하는지 판단하기

If you need to determine if a view exists, you may use the `exists` method:
뷰 파일이 존재하는 판단해야될 필요가 있다면 `exists` 메소드를 사용하면 됩니다. 

	if (view()->exists('emails.customer'))
	{
		//
	}

#### Returning A View From A File Path 파일 패스로 부터 view 반환

If you wish, you may generate a view from a fully-qualified file path:
필요하다면 절대경로를 기반으로 뷰를 생성할 수도 있습니다. 

	return view()->file($pathToFile, $data);

<a name="view-composers"></a>
## View Composers 뷰 컴포저

View composers are callbacks or class methods that are called when a view is rendered. 뷰 컴포저는 뷰가 렌더링 될 때 호출되는 콜백 또는 클래스 메소드입니다. If you have data that you want to be bound to a view each time that view is rendered, a view composer organizes that logic into a single location. 만약 뷰가 렌더링 될 때마다 뷰에 전달해야할 데이터를 가지고 있다면 뷰 컴포저는 해당 로직을 한곳에서 구성할수 있게해줍니다. 

#### Defining A View Composer 뷰 컴포저 정의하기

Let's organize our view composers within a [service provider](/docs/{{version}}/providers). 뷰 컴포저를 [서비스 프로바이더](/docs/{{version}}/providers)를 통해서 구성해 봅시다. We'll use the `View` facade to access the underlying `Illuminate\Contracts\View\Factory` contract implementation:
 `Illuminate\Contracts\View\Factory` contract 구현체에 엑세스 하기 위해서 `View` 파사드를 사용할 것입니다.

	<?php namespace App\Providers;

	use View;
	use Illuminate\Support\ServiceProvider;

	class ComposerServiceProvider extends ServiceProvider {

		/**
		 * Register bindings in the container.
		 *
		 * @return void
		 */
		public function boot()
		{
			// Using class based composers...
			View::composer('profile', 'App\Http\ViewComposers\ProfileComposer');

			// Using Closure based composers...
			View::composer('dashboard', function($view)
			{

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

> **Note참고:** Laravel does not include a default directory for view composers. 라라벨은 뷰 컴포저를 위한 디렉토리를 기본적으로 포함하고 있지는 않습니다. You are free to organize them however you wish. 편하신대로 구성하면 됩니다. For example, you could create an `App\Http\ViewComposers` directory. 예를 들어 `App\Http\ViewComposers` 디렉토리를 새롭게 생성할 수 있습니다. 

Remember, you will need to add the service provider to the `providers` array in the `config/app.php` configuration file. 기억해야 될 부분은 생성한 서비스 프로바이더를 `config/app.php` 설정 파일의 `providers` 배열에 추가해야한다는 것입니다. 

Now that we have registered the composer, the `ProfileComposer@compose` method will be executed each time the `profile` view is being rendered. 이제 뷰 컴포저를 등록했다면 `profile` 뷰가 렌더링 될 때마다 `ProfileComposer@compose` 메소드가 실행될 것입니다. So, let's define the composer class: 이제 컴포저 클래스를 정의해봅시다. 

	<?php namespace App\Http\ViewComposers;

	use Illuminate\Contracts\View\View;
	use Illuminate\Users\Repository as UserRepository;

	class ProfileComposer {

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

Just before the view is rendered, the composer's `compose` method is called with the `Illuminate\Contracts\View\View` instance. 뷰가 렌더링되기 전에 뷰컴포저의 `composer` 메소드가 `Illuminate\Contracts\View\View` 인스턴스와 함께 호출됩니다.  You may use the `with` method to bind data to the view. 데이터를 전달하기 위해서 `with` 메소드를 사용할 수 있습니다. 

> **Note참고:** All view composers are resolved via the [service container](/docs/{{version}}/container), so you may type-hint any dependencies you need within a composer's constructor. 모든 뷰 컴포저의 의존성 주입은 [service container](/docs/{{version}}/container), 를 통해서 이루어 집니다. 그렇기 때문에 필요한 객체의 경우 뷰 컴포저의 생성자에서 타입힌트를 지정한 형태로 지정하면 됩니다. 

#### Wildcard View Composers 와일드카드 뷰컴포저

The `composer` method accepts the `*` character as a wildcard, so you may attach a composer to all views like so: `composer` 메소드는 `*` 와일드 캐릭터로 인자를 받을 수 있는데 이렇게 하면 모든 뷰에 뷰컴포저를 지정하게 됩니다. 

	View::composer('*', function($view)
	{
		//
	});

#### Attaching A Composer To Multiple Views 컴포저에 다수의 뷰들 지정하기 

You may also attach a view composer to multiple views at once:
뷰 컴포저에 뷰를 지정할 때 한번에 여러 뷰를 지정할 수 있습니다. 

	View::composer(['profile', 'dashboard'], 'App\Http\ViewComposers\MyViewComposer');

#### Defining Multiple Composers 다수의 컴포저 정의하기 

You may use the `composers` method to register a group of composers at the same time:
`composers` 메소드를 사용하면 한번에 컴포저를 그룹지어 등록할 수 있습니다. 

	View::composers([
		'App\Http\ViewComposers\AdminComposer' => ['admin.index', 'admin.profile'],
		'App\Http\ViewComposers\UserComposer' => 'user',
		'App\Http\ViewComposers\ProductComposer' => 'product'
	]);

### View Creators 뷰 크리에이터

View **creators** work almost exactly like view composers; however, they are fired immediately when the view is instantiated. 뷰 **크리에이터**는 뷰 컴포저와 거의 비슷하게 동작합니다; 하지만 뷰 크리에이터는 뷰가 인스턴스화 되는 즉시 실행됩니다. To register a view creator, use the `creator` method:
뷰 크리에이터를 등록하기 위해서는 `creator` 메소드를 사용합니다. 

	View::creator('profile', 'App\Http\ViewCreators\ProfileCreator');
