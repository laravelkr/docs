# Mocking
# Mocking-모의 객체

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Mocking Objects](#mocking-objects)
- [Mocking 객체](#mocking-objects)
- [Mocking Facades](#mocking-facades)
- [Mocking 파사드](#mocking-facades)
    - [Facade Spies](#facade-spies)
    - [파사드 Spies](#facade-spies)
- [Bus Fake](#bus-fake)
- [Bus Fake](#bus-fake)
    - [Job Chains](#bus-job-chains)
    - [Job 체인](#bus-job-chains)
    - [Job Batches](#job-batches)
    - [Job 배치](#job-batches)
- [Event Fake](#event-fake)
- [Event Fake](#event-fake)
    - [Scoped Event Fakes](#scoped-event-fakes)
    - [Scoped Event Fakes](#scoped-event-fakes)
- [HTTP Fake](#http-fake)
- [HTTP Fake](#http-fake)
- [Mail Fake](#mail-fake)
- [Mail Fake](#mail-fake)
- [Notification Fake](#notification-fake)
- [Notification Fake](#notification-fake)
- [Queue Fake](#queue-fake)
- [Queue Fake](#queue-fake)
    - [Job Chains](#job-chains)
    - [Job 체인](#job-chains)
- [Storage Fake](#storage-fake)
- [Storage Fake](#storage-fake)
- [Interacting With Time](#interacting-with-time)
- [시간과의 상호작용](#interacting-with-time)

## Introduction
## 시작하기

When testing Laravel applications, you may wish to "mock" certain aspects of your application so they are not actually executed during a given test. For example, when testing a controller that dispatches an event, you may wish to mock the event listeners so they are not actually executed during the test. This allows you to only test the controller's HTTP response without worrying about the execution of the event listeners, since the event listeners can be tested in their own test case.

라라벨 애플리케이션을 테스팅할 때, 애플리케이션의 특정 부분을 "mock"하여 주어진 테스트가 진행될 때 실제로 실행되지 않도록 할 수 있습니다. 예를 들어 이벤트를 발생시키는 컨트롤러를 테스트 할 때, 이벤트 리스너가 테스트 중에 실제로 실행되지 않도록 mock할 수 있습니다. 이렇게하면 여러분은 이벤트 리스너는 자체 테스트 케이스에서 테스트 할 수 있고, 따라서 이벤트 리스너가 실행될것에 대한 걱정을 하지 않고, 컨트롤러의 HTTP response-응답만 테스트할 수 있습니다.

Laravel provides helpers for mocking events, jobs, and facades out of the box. These helpers primarily provide a convenience layer over Mockery so you do not have to manually make complicated Mockery method calls. You can also use [Mockery](http://docs.mockery.io/en/latest/) or PHPUnit to create your own mocks or spies.

라라벨은 기본적으로 이벤트, job 그리고 파사드에 대한 mock 헬퍼를 제공합니다. 이 헬퍼들은 주로 Mockery에서 작동하는 편리한 레이어를 제공하고 있기 때문에, 수동으로 복잡한 Mockery 메소드를 호출할 필요가 없습니다. 여러분의 고유한 mock 이나 spy 객체를 만드는데 자유롭게 [Mockery](http://docs.mockery.io/en/latest/) 또는 PHPUnit을 사용할 수 있습니다.

## Mocking Objects
## Mocking 객체

When mocking an object that is going to be injected into your application via Laravel's [service container](/docs/{{version}}/container), you will need to bind your mocked instance into the container as an `instance` binding. This will instruct the container to use your mocked instance of the object instead of constructing the object itself:

라라벨 서비스 컨테이너를 통하여 여러분의 애플리케이션에 주입되는 객체를 moking할 때에는, [서비스 컨테이너](/docs/{{version}}/container)의 `instance` 바인딩을 사용해서 mocking한 인스턴스를 등록할 필요가 있습니다. 이는 컨테이너에 객체를 생성하는 대신에 mocking 된 객체를 사용하도록 알려줍니다.

    use App\Service;
    use Mockery;
    use Mockery\MockInterface;

    public function test_something_can_be_mocked()
    {
        $this->instance(
            Service::class,
            Mockery::mock(Service::class, function (MockInterface $mock) {
                $mock->shouldReceive('process')->once();
            })
        );
    }

In order to make this more convenient, you may use the `mock` method, which is provided by Laravel's base test case class. For example, the following example is equivalent to the example above:

보다 편하게 사용하기 위해서, 라라벨의 기본 테스트 케이스 클래스가 제공하는 `mock` 메소드를 사용할 수도 있습니다. 예를 들어 다음 예는 위의 예와 동일합니다.

    use App\Service;
    use Mockery\MockInterface;

    $this->mock(Service::class, function (MockInterface $mock) {
        $mock->shouldReceive('process')->once();
    });

You may use the `partialMock` method when you only need to mock a few methods of an object. The methods that are not mocked will be executed normally when called:

객체의 몇 가지 메소드만 mocking해야 할 때 `partialMock` 메소드를 사용할 수 있습니다. mocking되지 않은 메소드는 다음과 같이 호출 될 때 정상적으로 실행됩니다.

    use App\Service;
    use Mockery\MockInterface;

    $this->partialMock(Service::class, function (MockInterface $mock) {
        $mock->shouldReceive('process')->once();
    });

Similarly, if you want to [spy](http://docs.mockery.io/en/latest/reference/spies.html) on an object, Laravel's base test case class offers a `spy` method as a convenient wrapper around the `Mockery::spy` method. Spies are similar to mocks; however, spies record any interaction between the spy and the code being tested, allowing you to make assertions after the code is executed:

비슷하게, 객체를 [spy](http://docs.mockery.io/en/latest/reference/spies.html) 하고 싶다면 Laravel의 기본 테스트 케이스 클래스는 `Mockery::spy` 메소드를 둘러싼 편리한 래퍼로서 `spy` 메소드를 제공합니다. spy는 mock과 비슷합니다. 그러나 spy는 spy와 테스트 중인 코드 사이의 모든 상호 작용을 기록하므로 코드가 실행된 후 검증(assertions)을 할 수 있습니다.

    use App\Service;

    $spy = $this->spy(Service::class);

    // ...

    $spy->shouldHaveReceived('process');

## Mocking Facades
## Mocking 파사드

Unlike traditional static method calls, [facades](/docs/{{version}}/facades) (including [real-time facades](/docs/{{version}}/facades#real-time-facades)) may be mocked. This provides a great advantage over traditional static methods and grants you the same testability that you would have if you were using traditional dependency injection. When testing, you may often want to mock a call to a Laravel facade that occurs in one of your controllers. For example, consider the following controller action:

기존의 정적 메서드 호출과 달리 실시간 [파사드](/docs/{{version}}/facades) ([실시간 파사드](/docs/{{version}}/facades#real-time-facades) 포함)를 mock할 수 있습니다. 이는 기존의 정적 메서드에 비해 큰 이점을 제공하고 기존 의존성 주입을 사용하는 경우와 동일한 테스트 가능성을 제공합니다. 테스트할 때 컨트롤러 중 하나에서 발생하는 Laravel 파사드에 대한 호출을 mock하고 싶을 수 있습니다. 예를 들어 다음 컨트롤러 작업을 고려하십시오.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Support\Facades\Cache;

    class UserController extends Controller
    {
        /**
         * Retrieve a list of all users of the application.
         *
         * @return \Illuminate\Http\Response
         */
        public function index()
        {
            $value = Cache::get('key');

            //
        }
    }

We can mock the call to the `Cache` facade by using the `shouldReceive` method, which will return an instance of a [Mockery](https://github.com/padraic/mockery) mock. Since facades are actually resolved and managed by the Laravel [service container](/docs/{{version}}/container), they have much more testability than a typical static class. For example, let's mock our call to the `Cache` facade's `get` method:

[Mockery](https://github.com/padraic/mockery) mock 인스턴스를 반환하는 `shouldReceive` 메서드를 사용하여 `Cache` 파사드에 대한 호출을 모의할 수 있습니다. 파사드는 실제로 Laravel [서비스 컨테이너](/docs/{{version}}/container)에 의해 해결되고 관리되기 때문에 일반적인 정적 클래스보다 테스트 가능성이 훨씬 높습니다. 예를 들어, `Cache` 파사드의 `get` 메소드에 대한 호출을 mock 해보겠습니다.

    <?php

    namespace Tests\Feature;

    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Support\Facades\Cache;
    use Tests\TestCase;

    class UserControllerTest extends TestCase
    {
        public function testGetIndex()
        {
            Cache::shouldReceive('get')
                        ->once()
                        ->with('key')
                        ->andReturn('value');

            $response = $this->get('/users');

            // ...
        }
    }

> {note} You should not mock the `Request` facade. Instead, pass the input you desire into the [HTTP testing methods](/docs/{{version}}/http-tests) such as `get` and `post` when running your test. Likewise, instead of mocking the `Config` facade, call the `Config::set` method in your tests.

> {note} `Request` 파사드를 mock해서는 안 됩니다. 대신 테스트를 실행할 때 `get` 및 `post`와 같은 [HTTP 테스트 방법](/docs/{{version}}/http-tests)에 원하는 입력을 전달하세요. 마찬가지로, `Config` 파사드를 mocking하는 대신 테스트에서 `Config::set` 메소드를 호출하세요.

### Facade Spies
### Facade Spies

If you would like to [spy](http://docs.mockery.io/en/latest/reference/spies.html) on a facade, you may call the `spy` method on the corresponding facade. Spies are similar to mocks; however, spies record any interaction between the spy and the code being tested, allowing you to make assertions after the code is executed:

파사드에서 [spy](http://docs.mockery.io/en/latest/reference/spies.html) 하려면 해당 파사드에서 `spy` 메서드를 호출하면 됩니다. spy는 mock과 비슷합니다. 그러나 spy는 spy와 테스트 중인 코드 사이의 모든 상호 작용을 기록하므로 코드가 실행된 후 검증을 할 수 있습니다

    use Illuminate\Support\Facades\Cache;

    public function test_values_are_be_stored_in_cache()
    {
        Cache::spy();

        $response = $this->get('/');

        $response->assertStatus(200);

        Cache::shouldHaveReceived('put')->once()->with('name', 'Taylor', 10);
    }

## Bus Fake
## Bus Fake

When testing code that dispatches jobs, you typically want to assert that a given job was dispatched but not actually queue or execute the job. This is because the job's execution can normally be tested in a separate test class.

job을 처리하는 코드를 테스트할 때 일반적으로 주어진 job이 처리되었지만 실제로 job을 queue에 넣거나 실행하지는 않았다고 검증하려고 합니다. job 실행이 일반적으로 별도의 테스트 클래스에서 테스트될 수 있기 때문입니다.

You may use the `Bus` facade's `fake` method to prevent jobs from being dispatched to the queue. Then, after executing the code under test, you may inspect which jobs the application attempted to dispatch using the `assertDispatched` and `assertNotDispatched` methods:

`Bus` 파사드의 `fake` 메서드를 사용하여 job이 queue로 전달되는 것을 방지할 수 있습니다. 그런 다음 테스트 중인 코드를 실행한 후 `assertDispatched` 및 `assertNotDispatched` 메소드를 사용하여 애플리케이션이 처리하려고 시도한 작업을 검사할 수 있습니다.


    <?php

    namespace Tests\Feature;

    use App\Jobs\ShipOrder;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Support\Facades\Bus;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_orders_can_be_shipped()
        {
            Bus::fake();

            // Perform order shipping...

            // Assert that a job was dispatched...
            Bus::assertDispatched(ShipOrder::class);

            // Assert a job was not dispatched...
            Bus::assertNotDispatched(AnotherJob::class);

            // Assert that a job was dispatched synchronously...
            Bus::assertDispatchedSync(AnotherJob::class);

            // Assert that a job was not dipatched synchronously...
            Bus::assertNotDispatchedSync(AnotherJob::class);

            // Assert that a job was dispatched after the response was sent...
            Bus::assertDispatchedAfterResponse(AnotherJob::class);

            // Assert a job was not dispatched after response was sent...
            Bus::assertNotDispatchedAfterResponse(AnotherJob::class);

            // Assert no jobs were dispatched...
            Bus::assertNothingDispatched();
        }
    }

You may pass a closure to the `assertDispatched` or `assertNotDispatched` methods in order to assert that a job was dispatched that passes a given "truth test". If at least one job was dispatched that passes the given truth test then the assertion will be successful. For example, you may wish to assert that a job was dispatched for a specific order:

주어진 "실제 테스트"를 통과하는 job이 처리되었음을 확인하기 위해 closure를 `assertDispatched` 또는 `assertNotDispatched`로 전달할 수 있습니다. 주어진 실제 테스트를 통과하는 적어도 하나의 job이 처리되면 검증이 성공할 것입니다. 예를 들어 특정 주문에 대해 job이 처리되었다고 검증할 수 있습니다.

    Bus::assertDispatched(function (ShipOrder $job) use ($order) {
        return $job->order->id === $order->id;
    });

### Job Chains
### Job 체인

The `Bus` facade's `assertChained` method may be used to assert that a [chain of jobs](/docs/{{version}}/queues#job-chaining) was dispatched. The `assertChained` method accepts an array of chained jobs as its first argument:

`Bus` 파사드의 `assertChained` 메소드는 [Job 체인](/docs/{{version}}/queues#job-chaining)이 dispatch되었음을 확인하는 데 사용할 수 있습니다. `assertChained` 메서드는 연결된 job의 배열을 첫 번째 인수로 허용합니다.

    use App\Jobs\RecordShipment;
    use App\Jobs\ShipOrder;
    use App\Jobs\UpdateInventory;
    use Illuminate\Support\Facades\Bus;

    Bus::assertChained([
        ShipOrder::class,
        RecordShipment::class,
        UpdateInventory::class
    ]);

As you can see in the example above, the array of chained jobs may be an array of the job's class names. However, you may also provide an array of actual job instances. When doing so, Laravel will ensure that the job instances are of the same class and have the same property values of the chained jobs dispatched by your application:

위의 예에서 볼 수 있듯이 연결된 job의 배열은 job의 클래스 이름의 배열일 수 있습니다. 그러나 실제 job 인스턴스의 배열을 제공할 수도 있습니다. 그렇게 할 때 Laravel은 job 인스턴스가 동일한 클래스에 있고 애플리케이션에서 전달한 연결 job의 동일한 속성 값을 갖도록 합니다.

    Bus::assertChained([
        new ShipOrder,
        new RecordShipment,
        new UpdateInventory,
    ]);

### Job Batches
### Job 배치

The `Bus` facade's `assertBatched` method may be used to assert that a [batch of jobs](/docs/{{version}}/queues#job-batching) was dispatched. The closure given to the `assertBatched` method receives an instance of `Illuminate\Bus\PendingBatch`, which may be used to inspect the jobs within the batch:

`Bus` 파사드의 `assertBatched` 메소드는 [Job 배치](/docs/{{version}}/queues#job-batching)가 처리되었음을 확인하는 데 사용할 수 있습니다. `assertBatched` 메소드에 주어진 클로저는 `Illuminate\Bus\PendingBatch`의 인스턴스를 수신하며, 이는 배치 내의 작업을 검사하는 데 사용할 수 있습니다.

    use Illuminate\Bus\PendingBatch;
    use Illuminate\Support\Facades\Bus;

    Bus::assertBatched(function (PendingBatch $batch) {
        return $batch->name == 'import-csv' &&
               $batch->jobs->count() === 10;
    });

## Event Fake
## Event Fake

When testing code that dispatches events, you may wish to instruct Laravel to not actually execute the event's listeners. Using the `Event` facade's `fake` method, you may prevent listeners from executing, execute the code under test, and then assert which events were dispatched by your application using the `assertDispatched`, `assertNotDispatched`, and `assertNothingDispatched` methods:

이벤트를 처리하는 코드를 테스트할 때 Laravel이 이벤트 리스너를 실제로 실행하지 않도록 지시할 수 있습니다. `Event` 파사드의 `fake` 메소드를 사용하면 리스너가 실행되는 것을 방지하고 테스트 중인 코드를 실행한 다음 `assertDispatched`, `assertNotDispatched` 및 `assertNothingDispatched` 메소드를 사용하여 애플리케이션에서 처리한 이벤트를 검증할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use App\Events\OrderFailedToShip;
    use App\Events\OrderShipped;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Support\Facades\Event;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        /**
         * Test order shipping.
         */
        public function test_orders_can_be_shipped()
        {
            Event::fake();

            // Perform order shipping...

            // Assert that an event was dispatched...
            Event::assertDispatched(OrderShipped::class);

            // Assert an event was dispatched twice...
            Event::assertDispatched(OrderShipped::class, 2);

            // Assert an event was not dispatched...
            Event::assertNotDispatched(OrderFailedToShip::class);

            // Assert that no events were dispatched...
            Event::assertNothingDispatched();
        }
    }

You may pass a closure to the `assertDispatched` or `assertNotDispatched` methods in order to assert that an event was dispatched that passes a given "truth test". If at least one event was dispatched that passes the given truth test then the assertion will be successful:

주어진 "실제 테스트"를 통과하는 이벤트가 전달되었는지 확인하기 위해 클로저를 `assertDispatched` 또는 `assertNotDispatched`로 전달할 수 있습니다. 주어진 실제 테스트를 통과하는 하나 이상의 이벤트가 전달되면 검증이 성공합니다.

    Event::assertDispatched(function (OrderShipped $event) use ($order) {
        return $event->order->id === $order->id;
    });

If you would simply like to assert that an event listener is listening to a given event, you may use the `assertListening` method:

이벤트 리스너가 주어진 이벤트를 수신하고 있다고 단순히 검증하고 싶다면 `assertListening` 메소드를 사용할 수 있습니다.

    Event::assertListening(
        OrderShipped::class,
        SendShipmentNotification::class
    );


> {note} After calling `Event::fake()`, no event listeners will be executed. So, if your tests use model factories that rely on events, such as creating a UUID during a model's `creating` event, you should call `Event::fake()` **after** using your factories.

> {note} `Event::fake()`를 호출하면, 모든 이벤트 리스너가 실행되지 않게됩니다. 따라서 모델의 `creating` 이벤트 중에 UUID를 생성하는 것과 같이 이벤트에 의존하는 모델 팩토리를 사용하는 테스트의 경우에는, **팩토리를 사용한 다음에** `Event::fake()`를 호출해야 합니다.

#### Faking A Subset Of Events
#### 이벤트의 일부를 Fake 시키기

If you only want to fake event listeners for a specific set of events, you may pass them to the `fake` or `fakeFor` method:

특정 이벤트에 대한 이벤트 리스너만 fake로 만들고 싶다면, 그것들을 `fake` 또는 `fakeFor` 메소드에 전달할 수 있습니다.

    /**
     * Test order process.
     */
    public function testOrderProcess()
    {
        Event::fake([
            OrderCreated::class,
        ]);

        $order = factory(Order::class)->create();

        Event::assertDispatched(OrderCreated::class);

        // Other events are dispatched as normal...
        $order->update([...]);
    }

### Scoped Event Fakes
### Scoped Event Fakes

If you only want to fake event listeners for a portion of your test, you may use the `fakeFor` method:

테스트의 일부분만 이벤트 리스너에게 fake 시키고 싶다면 `fakeFor` 메소드를 사용할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use App\Events\OrderCreated;
    use App\Models\Order;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Support\Facades\Event;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        /**
         * Test order process.
         */
        public function test_orders_can_be_processed()
        {
            $order = Event::fakeFor(function () {
                $order = Order::factory()->create();

                Event::assertDispatched(OrderCreated::class);

                return $order;
            });

            // Events are dispatched as normal and observers will run ...
            $order->update([...]);
        }
    }

## HTTP Fake
## HTTP Fake

The `Http` facade's `fake` method allows you to instruct the HTTP client to return stubbed / dummy responses when requests are made. For more information on faking outgoing HTTP requests, please consult the [HTTP Client testing documentation](/docs/{{version}}/http-client#testing).

`Http` 파사드의 `fake` 메소드를 사용하면 HTTP 클라이언트가 요청이있을 때 스텁 / 더미 응답을 반환하도록 지시 할 수 있습니다. Fake HTTP 요청에 대한 자세한 내용은 [HTTP 클라이언트 테스트 문서](/docs/{{version}}/http-client#testing)를 참조하세요.

## Mail Fake
## Mail Fake

You may use the Mail facade's fake method to prevent mail from being sent. Typically, sending mail is unrelated to the code you are actually testing. Most likely, it is sufficient to simply assert that Laravel was instructed to send a given mailable.

Mail 파사드의 fake 메서드를 사용하여 메일이 전송되는 것을 방지할 수 있습니다. 일반적으로 메일을 보내는 것은 실제로 테스트하는 코드와 관련이 없습니다. 아마도 Laravel이 주어진 mailable객체를 보냈다고 검증하는 것으로 충분할 것입니다.

After calling the `Mail` facade's `fake` method, you may then assert that [mailables](/docs/{{version}}/mail) were instructed to be sent to users and even inspect the data the mailables received:

`Mail` 파사드의 `fake` 메소드를 호출한 후 [mailable](/docs/{{version}}/mail)객체가 사용자에게 보내졌는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use App\Mail\OrderShipped;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Support\Facades\Mail;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_orders_can_be_shipped()
        {
            Mail::fake();

            // Perform order shipping...

            // Assert that no mailables were sent...
            Mail::assertNothingSent();

            // Assert that a mailable was sent...
            Mail::assertSent(OrderShipped::class);

            // Assert a mailable was sent twice...
            Mail::assertSent(OrderShipped::class, 2);

            // Assert a mailable was not sent...
            Mail::assertNotSent(AnotherMailable::class);
        }
    }

If you are queueing mailables for delivery in the background, you should use the `assertQueued` method instead of `assertSent`:

백그라운드에서 메일을 보내기 위해서 queue를 사용했다면, `assertSent` 대신에 `assertQueued` 메소드를 사용해야 합니다.

    Mail::assertQueued(OrderShipped::class);

    Mail::assertNotQueued(OrderShipped::class);

    Mail::assertNothingQueued();

You may pass a closure to the `assertSent` or `assertNotSent` methods in order to assert that a mailable was sent that passes a given "truth test". If at least one mailable was sent that passes the given truth test then the assertion will be successful:

주어진 "실제 테스트"를 통과한 mailable객체가 전송되었음을 확인하기 위해 `assertSent` 또는 `assertNotSent` 메소드에 클로저를 전달할 수 있습니다. 주어진 실제 테스트를 통과하는 적어도 하나의 mailable객체가 전송되면 검증이 성공할 것입니다.

    Mail::assertSent(function (OrderShipped $mail) use ($order) {
        return $mail->order->id === $order->id;
    });

When calling the `Mail` facade's assertion methods, the mailable instance accepted by the provided closure exposes helpful methods for examining the recipients of the mailable:

`Mail` 파사드의 검증 메소드를 호출할 때, 제공된 클로저에 의해 받아들여진 mailable 인스턴스는 mailable객체의 수신자를 검사하기 위한 유용한 메소드를 노출합니다.

    Mail::assertSent(OrderShipped::class, function ($mail) use ($user) {
        return $mail->hasTo($user->email) &&
               $mail->hasCc('...') &&
               $mail->hasBcc('...');
    });

You may have noticed that there are two methods for asserting that mail was not sent: `assertNotSent` and `assertNotQueued`. Sometimes you may wish to assert that no mail was sent **or** queued. To accomplish this, you may use the `assertNothingOutgoing` and `assertNotOutgoing` methods:

메일이 전송되지 않았는지 확인하는 방법에는 `assertNotSent`와 `assertNotQueued`가 있습니다. 때로는 **메일이 전송되지 않았거나 대기열에 추가되지 않았다고** 검증하고 싶을 수도 있습니다. 이를 위해 `assertNothingOutgoing` 및 `assertNotOutgoing` 메소드를 사용할 수 있습니다.

    Mail::assertNothingOutgoing();

    Mail::assertNotOutgoing(function (OrderShipped $mail) use ($order) {
        return $mail->order->id === $order->id;
    });

## Notification Fake
## Notification Fake

You may use the `Notification` facade's `fake` method to prevent notifications from being sent. You may then assert that [notifications](/docs/{{version}}/notifications) were sent to users and even inspect the data they received. When using fakes, assertions are made after the code under test is executed:

알림이 발송되는 것을 방지하기 위해서 `Notification ` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 [알림-notification](/docs/{{version}}/notifications)이 사용자에게 보내졌는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이 수행됩니다.

After calling the `Notification` facade's `fake` method, you may then assert that [notifications](/docs/{{version}}/notifications) were instructed to be sent to users and even inspect the data the notifications received:

`Notification` 파사드의 `fake` 메소드를 호출한 후 [알림-notification](/docs/{{version}}/notifications)이 사용자에게 전송되도록 지시하고 알림이 수신된 데이터를 검사할 수도 있다고 검증할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use App\Notifications\OrderShipped;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Support\Facades\Notification;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_orders_can_be_shipped()
        {
            Notification::fake();

            // Perform order shipping...

            // Assert that no notifications were sent...
            Notification::assertNothingSent();

            // Assert a notification was sent to the given users...
            Notification::assertSentTo(
                [$user], OrderShipped::class
            );

            // Assert a notification was not sent...
            Notification::assertNotSentTo(
                [$user], AnotherNotification::class
            );
        }
    }

You may pass a closure to the `assertSentTo` or `assertNotSentTo` methods in order to assert that a notification was sent that passes a given "truth test". If at least one notification was sent that passes the given truth test then the assertion will be successful:

주어진 "실제 테스트"를 통과하는 알림이 전송되었음을 검증하기 위해 `assertSentTo` 또는 `assertNotSentTo` 메소드에 클로저를 전달할 수 있습니다. 주어진 실제 테스트를 통과하는 하나 이상의 알림이 전송되면 검증이 성공합니다.

    Notification::assertSentTo(
        $user,
        function (OrderShipped $notification, $channels) use ($order) {
            return $notification->order->id === $order->id;
        }
    );

#### On-Demand Notifications
#### 대상을 지정한 Notifications

If the code you are testing sends [on-demand notifications](/docs/{{version}}/notifications#on-demand-notifications), you will need to assert that the notification was sent to an `Illuminate\Notifications\AnonymousNotifiable` instance:

테스트 중인 코드가 [대상을 지정한 notifications](/docs/{{version}}/notifications#on-demand-notifications)을 보내는 경우 알림이 `Illuminate\Notifications\AnonymousNotifiable` 인스턴스로 전송되었음을 검증해야 합니다.

    use Illuminate\Notifications\AnonymousNotifiable;

    Notification::assertSentTo(
        new AnonymousNotifiable, OrderShipped::class
    );

By passing a closure as the third argument to the notification assertion methods, you may determine if an on-demand notification was sent to the correct "route" address:

알림 검증 메서드에 대한 세 번째 인수로 클로저를 전달하면 대상을 지정한 notifications가 올바른 "경로" 주소로 전송되었는지 확인할 수 있습니다.

    Notification::assertSentTo(
        new AnonymousNotifiable,
        OrderShipped::class,
        function ($notification, $channels, $notifiable) use ($user) {
            return $notifiable->routes['mail'] === $user->email;
        }
    );

## Queue Fake
## Queue Fake

You may use the `Queue` facade's `fake` method to prevent queued jobs from being pushed to the queue. Most likely, it is sufficient to simply assert that Laravel was instructed to push a given job to the queue since the queued jobs themselves may be tested in another test class.

큐에 있는 job이 큐에 푸시되는 것을 방지하기 위해서 mocking 을 사용하는 대신, `Queue ` 파사드의 `fake` 메소드를 사용할 수 있습니다. 대기 중인 작업 자체가 다른 테스트 클래스에서 테스트될 수 있기 때문에 Laravel이 주어진 job을 대기열로 푸시하도록 지시받았다고 간단히 검증하는 것으로 충분합니다.

After calling the `Queue` facade's `fake` method, you may then assert that the application attempted to push jobs to the queue:

`Queue` 파사드의 `fake` 메소드를 호출한 후 애플리케이션이 작업을 큐에 푸시하려고 시도했다고 검증할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use App\Jobs\AnotherJob;
    use App\Jobs\FinalJob;
    use App\Jobs\ShipOrder;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Support\Facades\Queue;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_orders_can_be_shipped()
        {
            Queue::fake();

            // Perform order shipping...

            // Assert that no jobs were pushed...
            Queue::assertNothingPushed();

            // Assert a job was pushed to a given queue...
            Queue::assertPushedOn('queue-name', ShipOrder::class);

            // Assert a job was pushed twice...
            Queue::assertPushed(ShipOrder::class, 2);

            // Assert a job was not pushed...
            Queue::assertNotPushed(AnotherJob::class);
        }
    }

You may pass a closure to the `assertPushed` or `assertNotPushed` methods in order to assert that a job was pushed that passes a given "truth test". If at least one job was pushed that passes the given truth test then the assertion will be successful:

주어진 "실제 테스트"를 통과하는 job이 푸시되었다고 검증하기 위해 클로저를 `assertPushed` 또는 `assertNotPushed` 메서드에 전달할 수 있습니다. 주어진 실제 테스트를 통과하는 하나 이상의 job이 푸시되면 검증이 성공합니다.

    Queue::assertPushed(function (ShipOrder $job) use ($order) {
        return $job->order->id === $order->id;
    });

### Job Chains
### Job 체인

The `Queue` facade's `assertPushedWithChain` and `assertPushedWithoutChain` methods may be used to inspect the job chain of a pushed job. The `assertPushedWithChain` method accepts the primary job as its first argument and an array of chained jobs as its second argument:

`Queue` 파사드의 `assertPushedWithChain` 및 `assertPushedWithoutChain` 메소드를 사용하여 푸시된 job의 job 체인을 검사할 수 있습니다. `assertPushedWithChain` 메소드는 최초 작업을 첫 번째 인수로, 연결된 작업의 배열을 두 번째 인수로 허용합니다.

    use App\Jobs\RecordShipment;
    use App\Jobs\ShipOrder;
    use App\Jobs\UpdateInventory;
    use Illuminate\Support\Facades\Queue;

    Queue::assertPushedWithChain(ShipOrder::class, [
        RecordShipment::class,
        UpdateInventory::class
    ]);

As you can see in the example above, the array of chained jobs may be an array of the job's class names. However, you may also provide an array of actual job instances. When doing so, Laravel will ensure that the job instances are of the same class and have the same property values of the chained jobs dispatched by your application:

위의 예에서 볼 수 있듯이 job 체인의 배열은 job의 클래스 이름의 배열일 수 있습니다. 그러나 실제 작업 인스턴스의 배열을 제공할 수도 있습니다. 그렇게 할 때 Laravel은 job 인스턴스가 동일한 클래스에 있고 애플리케이션에서 전달한 job 체인의 동일한 속성 값을 갖도록 합니다.

    Queue::assertPushedWithChain(ShipOrder::class, [
        new RecordShipment,
        new UpdateInventory,
    ]);

You may use the `assertPushedWithoutChain` method to assert that a job was pushed without a chain of jobs:

`assertPushedWithoutChain` 메소드를 사용하여 job 체인 없이 job이 푸시되었음을 확인할 수 있습니다.

    Queue::assertPushedWithoutChain(ShipOrder::class);


## Storage Fake
## Storage Fake

The `Storage` facade's `fake` method allows you to easily generate a fake disk that, combined with the file generation utilities of the `Illuminate\Http\UploadedFile` class, greatly simplifies the testing of file uploads. For example:

`Storage` 파사드의 `fake` 메소드를 사용하면 `Illuminate\Http\UploadedFile` 클래스의 파일 생성과 결합된 가짜 디스크를 생성할 수 있어, 파일 업로드 테스트가 아주 쉬워집니다. 예를 들면:

    <?php

    namespace Tests\Feature;

    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Http\UploadedFile;
    use Illuminate\Support\Facades\Storage;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_albums_can_be_uploaded()
        {
            Storage::fake('photos');

            $response = $this->json('POST', '/photos', [
                UploadedFile::fake()->image('photo1.jpg'),
                UploadedFile::fake()->image('photo2.jpg')
            ]);

            // Assert one or more files were stored...
            Storage::disk('photos')->assertExists('photo1.jpg');
            Storage::disk('photos')->assertExists(['photo1.jpg', 'photo2.jpg']);

            // Assert one or more files were not stored...
            Storage::disk('photos')->assertMissing('missing.jpg');
            Storage::disk('photos')->assertMissing(['missing.jpg', 'non-existing.jpg']);
        }
    }

For more information on testing file uploads, you may consult the [HTTP testing documentation's information on file uploads](/docs/{{version}}/http-tests#testing-file-uploads).

파일 업로드 테스트에 대한 자세한 내용은 [HTTP 테스트 문서의 파일 업로드 테스트하기](/docs/{{version}}/http-tests#testing-file-uploads)를 참조하세요.

> {tip} By default, the `fake` method will delete all files in its temporary directory. If you would like to keep these files, you may use the "persistentFake" method instead.

> {tip} 기본적으로, `fake` 메소드는 임시 디렉토리에 있는 모든 파일 삭제합니다. 이 파일들을 유지하고자 한다면, 대신 "persistentFake" 메소드를 사용하십시오.

## Interacting With Time
## 시간과의 상호작용

When testing, you may occasionally need to modify the time returned by helpers such as `now` or `Illuminate\Support\Carbon::now()`. Thankfully, Laravel's base feature test class includes helpers that allow you to manipulate the current time:

테스트할 때 `now` 또는 `Illuminate\Support\Carbon::now()`와 같은 헬퍼가 반환한 시간을 수정해야 하는 경우가 있습니다. 고맙게도 Laravel의 기본 기능 테스트 클래스에는 현재 시간을 조작할 수 있는 헬퍼가 포함되어 있습니다.

    public function testTimeCanBeManipulated()
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
