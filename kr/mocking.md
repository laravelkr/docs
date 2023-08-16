# Mocking
# Mocking-모의 객체

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Mocking Objects](#mocking-objects)
- [객체 Mocking](#mocking-objects)
- [Mocking Facades](#mocking-facades)
- [파사드 Mocking](#mocking-facades)
    - [Facade Spies](#facade-spies)
    - [파사드 Spies](#facade-spies)
- [Interacting With Time](#interacting-with-time)
- [시간 다루기](#interacting-with-time)

## Introduction
## 시작하기

When testing Laravel applications, you may wish to "mock" certain aspects of your application so they are not actually executed during a given test. For example, when testing a controller that dispatches an event, you may wish to mock the event listeners so they are not actually executed during the test. This allows you to only test the controller's HTTP response without worrying about the execution of the event listeners since the event listeners can be tested in their own test case.

라라벨 애플리케이션을 테스트할 때, 주어진 테스트 중에 실행되지 않도록 애플리케이션의 특정 부분을 "mock"하고 싶을 수 있습니다. 예를 들어, 이벤트를 발송하는 컨트롤러를 테스트할 때, 이벤트 리스너를 'mock' 해서 테스트 중에 실제로 실행되지 않도록 할 수 있습니다. 이렇게 하면 이벤트 리스너는 자체적인 테스트 케이스에서 테스트할 수 있으므로 컨트롤러의 HTTP 응답만 테스트하면 되므로 이벤트 리스너의 실행에 대해 걱정할 필요가 없습니다.


Laravel provides helpful methods for mocking events, jobs, and other facades out of the box. These helpers primarily provide a convenience layer over Mockery so you do not have to manually make complicated Mockery method calls.

라라벨은 이벤트, 작업 및 파사드를 쉽게 모의(mock)를 할 수 있는 유용한 메서드를 제공합니다. 이러한 헬퍼들은 대부분 Mockery를 기반으로 구현되어 있어서 복잡한 Mockery 메서드 호출을 수동으로 처리할 필요 없이 간편하게 사용할 수 있도록 편의성을 제공합니다.

<a name="mocking-objects"></a>
## Mocking Objects
## Mocking 객체

When mocking an object that is going to be injected into your application via Laravel's [service container](/docs/{{version}}/container), you will need to bind your mocked instance into the container as an `instance` binding. This will instruct the container to use your mocked instance of the object instead of constructing the object itself:

라라벨 서비스 컨테이너를 통하여 여러분의 애플리케이션에 주입되는 객체를 moking할 때에는, [서비스 컨테이너](/docs/{{version}}/container)의 `instance` 바인딩을 사용해서 mocking한 인스턴스를 등록할 필요가 있습니다. 이는 컨테이너에 객체를 생성하는 대신에 mocking 된 객체를 사용하도록 알려줍니다.

    use App\Service;
    use Mockery;
    use Mockery\MockInterface;

    public function test_something_can_be_mocked(): void
    {
        $this->instance(
            Service::class,
            Mockery::mock(Service::class, function (MockInterface $mock) {
                $mock->shouldReceive('process')->once();
            })
        );
    }

In order to make this more convenient, you may use the `mock` method that is provided by Laravel's base test case class. For example, the following example is equivalent to the example above:

보다 편하게 사용하기 위해서, 라라벨의 기본 테스트 케이스 클래스가 제공하는 `mock` 메소드를 사용할 수도 있습니다. 예를 들어 다음 예는 위의 예와 동일합니다.

    use App\Service;
    use Mockery\MockInterface;

    $mock = $this->mock(Service::class, function (MockInterface $mock) {
        $mock->shouldReceive('process')->once();
    });

You may use the `partialMock` method when you only need to mock a few methods of an object. The methods that are not mocked will be executed normally when called:

객체의 몇 가지 메소드만 mocking해야 할 때 `partialMock` 메소드를 사용할 수 있습니다. mocking되지 않은 메소드는 다음과 같이 호출 될 때 정상적으로 실행됩니다.

    use App\Service;
    use Mockery\MockInterface;

    $mock = $this->partialMock(Service::class, function (MockInterface $mock) {
        $mock->shouldReceive('process')->once();
    });

Similarly, if you want to [spy](http://docs.mockery.io/en/latest/reference/spies.html) on an object, Laravel's base test case class offers a `spy` method as a convenient wrapper around the `Mockery::spy` method. Spies are similar to mocks; however, spies record any interaction between the spy and the code being tested, allowing you to make assertions after the code is executed:

비슷하게, 객체를 [spy](http://docs.mockery.io/en/latest/reference/spies.html) 하고 싶다면 Laravel의 기본 테스트 케이스 클래스는 `Mockery::spy` 메소드를 둘러싼 편리한 래퍼로서 `spy` 메소드를 제공합니다. spy는 mock과 비슷합니다. 그러나 spy는 spy와 테스트 중인 코드 사이의 모든 상호 작용을 기록하므로 코드가 실행된 후 검증(assertions)을 할 수 있습니다.

    use App\Service;

    $spy = $this->spy(Service::class);

    // ...

    $spy->shouldHaveReceived('process');

<a name="mocking-facades"></a>
## Mocking Facades
## 파사드 Mocking

Unlike traditional static method calls, [facades](/docs/{{version}}/facades) (including [real-time facades](/docs/{{version}}/facades#real-time-facades)) may be mocked. This provides a great advantage over traditional static methods and grants you the same testability that you would have if you were using traditional dependency injection. When testing, you may often want to mock a call to a Laravel facade that occurs in one of your controllers. For example, consider the following controller action:

기존의 정적 메서드 호출과 달리 실시간 [파사드](/docs/{{version}}/facades) ([실시간 파사드](/docs/{{version}}/facades#real-time-facades) 포함)를 mock할 수 있습니다. 이는 기존의 정적 메서드에 비해 큰 이점을 제공하고 기존 의존성 주입을 사용하는 경우와 동일한 테스트 가능성을 제공합니다. 테스트할 때 컨트롤러 중 하나에서 발생하는 Laravel 파사드에 대한 호출을 mock하고 싶을 수 있습니다. 예를 들어 다음 컨트롤러 작업을 고려하십시오.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Support\Facades\Cache;

    class UserController extends Controller
    {
        /**
         * Retrieve a list of all users of the application.
         */
        public function index(): array
        {
            $value = Cache::get('key');

            return [
                // ...
            ];
        }
    }

We can mock the call to the `Cache` facade by using the `shouldReceive` method, which will return an instance of a [Mockery](https://github.com/padraic/mockery) mock. Since facades are actually resolved and managed by the Laravel [service container](/docs/{{version}}/container), they have much more testability than a typical static class. For example, let's mock our call to the `Cache` facade's `get` method:

[Mockery](https://github.com/padraic/mockery) mock 인스턴스를 반환하는 `shouldReceive` 메서드를 사용하여 `Cache` 파사드에 대한 호출을 모의할 수 있습니다. 파사드는 실제로 Laravel [서비스 컨테이너](/docs/{{version}}/container)에 의해 해결되고 관리되기 때문에 일반적인 정적 클래스보다 테스트 가능성이 훨씬 높습니다. 예를 들어, `Cache` 파사드의 `get` 메소드에 대한 호출을 mock 해보겠습니다.

    <?php

    namespace Tests\Feature;

    use Illuminate\Support\Facades\Cache;
    use Tests\TestCase;

    class UserControllerTest extends TestCase
    {
        public function test_get_index(): void
        {
            Cache::shouldReceive('get')
                        ->once()
                        ->with('key')
                        ->andReturn('value');

            $response = $this->get('/users');

            // ...
        }
    }

> **Warning**  
> You should not mock the `Request` facade. Instead, pass the input you desire into the [HTTP testing methods](/docs/{{version}}/http-tests) such as `get` and `post` when running your test. Likewise, instead of mocking the `Config` facade, call the `Config::set` method in your tests.

> **Warning**  
> `Request` 파사드를 mock해서는 안 됩니다. 대신 테스트를 실행할 때 `get` 및 `post`와 같은 [HTTP 테스트 방법](/docs/{{version}}/http-tests)에 원하는 입력을 전달하세요. 마찬가지로, `Config` 파사드를 mocking하는 대신 테스트에서 `Config::set` 메소드를 호출하세요.

<a name="facade-spies"></a>
### Facade Spies
### 파사드 Spies

If you would like to [spy](http://docs.mockery.io/en/latest/reference/spies.html) on a facade, you may call the `spy` method on the corresponding facade. Spies are similar to mocks; however, spies record any interaction between the spy and the code being tested, allowing you to make assertions after the code is executed:

파사드에서 [spy](http://docs.mockery.io/en/latest/reference/spies.html) 하려면 해당 파사드에서 `spy` 메서드를 호출하면 됩니다. spy는 mock과 비슷합니다. 그러나 spy는 spy와 테스트 중인 코드 사이의 모든 상호 작용을 기록하므로 코드가 실행된 후 검증을 할 수 있습니다.

    use Illuminate\Support\Facades\Cache;

    public function test_values_are_be_stored_in_cache(): void
    {
        Cache::spy();

        $response = $this->get('/');

        $response->assertStatus(200);

        Cache::shouldHaveReceived('put')->once()->with('name', 'Taylor', 10);
    }

<a name="interacting-with-time"></a>
## Interacting With Time
## 시간 다루기

When testing, you may occasionally need to modify the time returned by helpers such as `now` or `Illuminate\Support\Carbon::now()`. Thankfully, Laravel's base feature test class includes helpers that allow you to manipulate the current time:

테스트할 때 `now` 또는 `Illuminate\Support\Carbon::now()`와 같은 헬퍼가 반환한 시간을 수정해야 하는 경우가 있습니다. 고맙게도 라라벨의 기본 기능 테스트 클래스에는 현재 시간을 조작할 수 있는 헬퍼가 포함되어 있습니다.

    use Illuminate\Support\Carbon;

    public function test_time_can_be_manipulated(): void
    {
        // Travel into the future...
        $this->travel(5)->milliseconds();
        $this->travel(5)->seconds();
        $this->travel(5)->minutes();
        $this->travel(5)->hours();
        $this->travel(5)->days();
        $this->travel(5)->weeks();
        $this->travel(5)->years();

        // Travel into the past...
        $this->travel(-5)->hours();

        // Travel to an explicit time...
        $this->travelTo(now()->subHours(6));

        // Return back to the present time...
        $this->travelBack();
    }

You may also provide a closure to the various time travel methods. The closure will be invoked with time frozen at the specified time. Once the closure has executed, time will resume as normal:

다양한 시간 조작 메소드에 클로저를 제공할 수도 있습니다. 클로저는 지정된 시간에서 시간이 멈춘 채로 호출됩니다. 클로저가 실행된 후에는 시간이 정상적으로 재개됩니다.

    $this->travel(5)->days(function () {
        // Test something five days into the future...
    });
    
    $this->travelTo(now()->subDays(10), function () {
        // Test something during a given moment...
    });

The `freezeTime` method may be used to freeze the current time. Similarly, the `freezeSecond` method will freeze the current time but at the start of the current second:

`freezeTime` 메서드는 현재 시간을 고정할 때 사용됩니다. 마찬가지로, `freezeSecond` 메서드는 현재 시간을 고정하지만, 현재 초의 시작 부분에서 고정합니다.


    use Illuminate\Support\Carbon;

    // Freeze time and resume normal time after executing closure...
    $this->freezeTime(function (Carbon $time) {
        // ...
    });

    // Freeze time at the current second and resume normal time after executing closure...
    $this->freezeSecond(function (Carbon $time) {
        // ...
    })

As you would expect, all of the methods discussed above are primarily useful for testing time sensitive application behavior, such as locking inactive posts on a discussion forum:

위의 방법들은 대개 시간 제한이 있는 애플리케이션 동작을 테스트하는 데 유용하며, 예를 들어 토론 포럼에서 비활성화된 게시물을 자동으로 잠그는 등의 작업에 적합합니다.

    use App\Models\Thread;
    
    public function test_forum_threads_lock_after_one_week_of_inactivity()
    {
        $thread = Thread::factory()->create();
        
        $this->travel(1)->week();
        
        $this->assertTrue($thread->isLockedByInactivity());
    }
