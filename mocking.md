# Mocking
# Mocking - 모의 객체

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Mocking Objects](#mocking-objects)
- [Mocking 객체](#mocking-objects)
- [Bus Fake](#bus-fake)
- [Bus Fake](#bus-fake)
- [Event Fake](#event-fake)
- [Event Fake](#event-fake)
    - [Scoped Event Fakes](#scoped-event-fakes)
    - [Scoped Event Fakes](#scoped-event-fakes)
- [Mail Fake](#mail-fake)
- [Mail Fake](#mail-fake)
- [Notification Fake](#notification-fake)
- [Notification Fake](#notification-fake)
- [Queue Fake](#queue-fake)
- [Queue Fake](#queue-fake)
- [Storage Fake](#storage-fake)
- [Storage Fake](#storage-fake)
- [Facades](#mocking-facades)
- [파사드](#mocking-facades)

<a name="introduction"></a>
## Introduction
## 시작하기

When testing Laravel applications, you may wish to "mock" certain aspects of your application so they are not actually executed during a given test. For example, when testing a controller that dispatches an event, you may wish to mock the event listeners so they are not actually executed during the test. This allows you to only test the controller's HTTP response without worrying about the execution of the event listeners, since the event listeners can be tested in their own test case.

라라벨 애플리케이션을 테스트할 때, 주어진 테스트가 진행되는 동안 실제로 실행되지 않도록 애플리케이션의 특정 부분을 "mock" 할 수 있습니다. 예를 들어 이벤트를 전달하는 컨트롤러를 테스트 할 때, 테스트 중에 이벤트 리스너가 실제로 실행되지 않도록 mock 할 수 있습니다. 이벤트 리스너는 자체 테스트 케이스에서 테스트 할 수 있어서 이벤트 리스너가 실행되는 걱정없이 컨트롤러의 HTTP 응답만 테스트할 수 있습니다.

Laravel provides helpers for mocking events, jobs, and facades out of the box. These helpers primarily provide a convenience layer over Mockery so you do not have to manually make complicated Mockery method calls. You can also use [Mockery](http://docs.mockery.io/en/latest/) or PHPUnit to create your own mocks or spies.

라라벨은 이벤트, job 그리고 파사드에 대한 mock 헬퍼를 제공합니다. 이 헬퍼들은 주로 Mockery 보다 편리한 계층을 제공하기 때문에, 복잡한 Mockery 메소드를 수동으로 호출할 필요가 없습니다. [Mockery](http://docs.mockery.io/en/latest/)나 PHPUnit을 사용하여 자신만의 mock이나 spy를 생성할 수 있습니다.

<a name="mocking-objects"></a>
## Mocking Objects
## Mocking 객체

When mocking an object that is going to be injected into your application via Laravel's service container, you will need to bind your mocked instance into the container as an `instance` binding. This will instruct the container to use your mocked instance of the object instead of constructing the object itself:

라라벨의 서비스 컨테이너를 통해 애플리케이션에 주입될 객체를 moking할 때는, `인스턴스(instance)` 바인딩으로 컨테이너에 mocking한 인스턴스를 바인딩 해야 합니다. 컨테이너 자체에 객체 자체를 구성하는 대신 mocking 한 객체 인스턴스를 사용하도록 지시합니다.

    use Mockery;
    use App\Service;

    $this->instance(Service::class, Mockery::mock(Service::class, function ($mock) {
        $mock->shouldReceive('process')->once();
    }));

In order to make this more convenient, you may use the `mock` method, which is provided by Laravel's base test case class:

보다 편하게 사용하기 위해서, 라라벨의 기본 테스트 케이스 클래스에서 제공하는 `mock` 메소드를 사용할 수 있습니다.

    use App\Service;

    $this->mock(Service::class, function ($mock) {
        $mock->shouldReceive('process')->once();
    });

Similarly, if you want to spy on an object, Laravel's base test case class offers a `spy` method as a convenient wrapper around the `Mockery::spy` method:

마찬가지로 객체를 감시하고 싶다면 Laravel의 기본 테스트 케이스 클래스는 `Mockery::spy` 메소드 주위에 편리한 래퍼로 `spy` 메소드를 제공합니다.

    use App\Service;

    $this->spy(Service::class, function ($mock) {
        $mock->shouldHaveReceived('process');
    });

<a name="bus-fake"></a>
## Bus Fake

As an alternative to mocking, you may use the `Bus` facade's `fake` method to prevent jobs from being dispatched. When using fakes, assertions are made after the code under test is executed:

mocking의 대안으로, `Bus` 파사드의 `fake` 메소드를 사용하여 작업이 실행되는 것을 방지할 수 있습니다. fake를 사용하면 테스트 중인 코드가 실행 된 후 검증(assertion)이 수행됩니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use App\Jobs\ShipOrder;
    use Illuminate\Support\Facades\Bus;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;

    class ExampleTest extends TestCase
    {
        public function testOrderShipping()
        {
            Bus::fake();

            // Perform order shipping...

            Bus::assertDispatched(ShipOrder::class, function ($job) use ($order) {
                return $job->order->id === $order->id;
            });

            // Assert a job was not dispatched...
            Bus::assertNotDispatched(AnotherJob::class);
        }
    }

<a name="event-fake"></a>
## Event Fake
## Event Fake

As an alternative to mocking, you may use the `Event` facade's `fake` method to prevent all event listeners from executing. You may then assert that events were dispatched and even inspect the data they received. When using fakes, assertions are made after the code under test is executed:

 mocking의 대안으로, `Event` 파사드의 `fake` 메소드를 사용하여 모든 이벤트 리스너가 실행되는 것을 방지할 수 있습니다. 이벤트가 처리되었는지 확인하고 수신받은 데이터를 검사할 수도 있습니다. fake를 사용하면, 테스트 중인 코드가 실행된 뒤에 검증이(assertions) 수행됩니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use App\Events\OrderShipped;
    use App\Events\OrderFailedToShip;
    use Illuminate\Support\Facades\Event;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;

    class ExampleTest extends TestCase
    {
        /**
         * Test order shipping.
         */
        public function testOrderShipping()
        {
            Event::fake();

            // Perform order shipping...

            Event::assertDispatched(OrderShipped::class, function ($e) use ($order) {
                return $e->order->id === $order->id;
            });

            // Assert an event was dispatched twice...
            Event::assertDispatched(OrderShipped::class, 2);

            // Assert an event was not dispatched...
            Event::assertNotDispatched(OrderFailedToShip::class);
        }
    }

> {note} After calling `Event::fake()`, no event listeners will be executed. So, if your tests use model factories that rely on events, such as creating a UUID during a model's `creating` event, you should call `Event::fake()` **after** using your factories.

> {note} `Event::fake()`를 호출하면, 모든 이벤트 리스너가 실행되지 않습니다. 따라서 테스트가 모델의 `creating` 이벤트 중에 UUID를 생성하는 것과 같이 이벤트에 의존하는 모델 팩토리를 사용한다면, 팩토리를 사용한 **다음에** `Event::fake()`를 호출해야 합니다.

#### Faking A Subset Of Events
#### 이벤트의 일부를 Fake 시키기

If you only want to fake event listeners for a specific set of events, you may pass them to the `fake` or `fakeFor` method:

fake 하려는 특정 이벤트 세트에 대한 이벤트 리스너를 `fake`나 `fakeFor` 메소드에 전달할 수 있습니다.

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

<a name="scoped-event-fakes"></a>
### Scoped Event Fakes
### Scoped Event Fakes

If you only want to fake event listeners for a portion of your test, you may use the `fakeFor` method:

테스트의 일부만 이벤트 리스너로 fake 하려면, `fakeFor` 메소드를 사용할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use App\Order;
    use Tests\TestCase;
    use App\Events\OrderCreated;
    use Illuminate\Support\Facades\Event;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;

    class ExampleTest extends TestCase
    {
        /**
         * Test order process.
         */
        public function testOrderProcess()
        {
            $order = Event::fakeFor(function () {
                $order = factory(Order::class)->create();

                Event::assertDispatched(OrderCreated::class);

                return $order;
            });

            // Events are dispatched as normal and observers will run ...
            $order->update([...]);
        }
    }

<a name="mail-fake"></a>
## Mail Fake
## Mail Fake

You may use the `Mail` facade's `fake` method to prevent mail from being sent. You may then assert that [mailables](/docs/{{version}}/mail) were sent to users and even inspect the data they received. When using fakes, assertions are made after the code under test is executed:

메일이 발송되는 것을 방지하기 위해서 `Mail` 파사드의 `fake` 메소드를 사용할 수 있습니다. 그런 다음 [mailables](/docs/{{version}}/mail)이 사용자에게 보내졌는지 검증하고 수신받은 데이터를 검사할 수도 있습니다. fake를 사용하면, 테스트 중인 코드가 실행된 뒤에 검증(assertions)이 수행됩니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use App\Mail\OrderShipped;
    use Illuminate\Support\Facades\Mail;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;

    class ExampleTest extends TestCase
    {
        public function testOrderShipping()
        {
            Mail::fake();

            // Assert that no mailables were sent...
            Mail::assertNothingSent();

            // Perform order shipping...

            Mail::assertSent(OrderShipped::class, function ($mail) use ($order) {
                return $mail->order->id === $order->id;
            });

            // Assert a message was sent to the given users...
            Mail::assertSent(OrderShipped::class, function ($mail) use ($user) {
                return $mail->hasTo($user->email) &&
                       $mail->hasCc('...') &&
                       $mail->hasBcc('...');
            });

            // Assert a mailable was sent twice...
            Mail::assertSent(OrderShipped::class, 2);

            // Assert a mailable was not sent...
            Mail::assertNotSent(AnotherMailable::class);
        }
    }

If you are queueing mailables for delivery in the background, you should use the `assertQueued` method instead of `assertSent`:

백그라운드에서 메일 발송을 위해 queue를 사용한다면, `assertSent` 대신에 `assertQueued` 메소드를 사용해야 합니다.

    Mail::assertQueued(...);
    Mail::assertNotQueued(...);

<a name="notification-fake"></a>
## Notification Fake
## Notification Fake

You may use the `Notification` facade's `fake` method to prevent notifications from being sent. You may then assert that [notifications](/docs/{{version}}/notifications) were sent to users and even inspect the data they received. When using fakes, assertions are made after the code under test is executed:

공지가 발송되는 것을 방지하기 위해서 `Notification ` 파사드의 `fake` 메소드를 사용할 수 있습니다. [공지(notification)](/docs/{{version}}/notifications)가 사용자에게 보내졌는지 검증하고 수신된 데이터를 검사할 수 있습니다. fake를 사용하면, 테스트 중인 코드가 실행된 뒤에 검증(assertions)이 수행됩니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use App\Notifications\OrderShipped;
    use Illuminate\Support\Facades\Notification;
    use Illuminate\Notifications\AnonymousNotifiable;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;

    class ExampleTest extends TestCase
    {
        public function testOrderShipping()
        {
            Notification::fake();

            // Assert that no notifications were sent...
            Notification::assertNothingSent();

            // Perform order shipping...

            Notification::assertSentTo(
                $user,
                OrderShipped::class,
                function ($notification, $channels) use ($order) {
                    return $notification->order->id === $order->id;
                }
            );

            // Assert a notification was sent to the given users...
            Notification::assertSentTo(
                [$user], OrderShipped::class
            );

            // Assert a notification was not sent...
            Notification::assertNotSentTo(
                [$user], AnotherNotification::class
            );

            // Assert a notification was sent via Notification::route() method...
            Notification::assertSentTo(
                new AnonymousNotifiable, OrderShipped::class
            );
        }
    }

<a name="queue-fake"></a>
## Queue Fake
## Queue Fake

As an alternative to mocking, you may use the `Queue` facade's `fake` method to prevent jobs from being queued. You may then assert that jobs were pushed to the queue and even inspect the data they received. When using fakes, assertions are made after the code under test is executed:

mocking 을 사용하는 대신에, job이 큐잉되는 것을 방지하기 위해 `Queue ` 파사드의 `fake` 메소드를 사용할 수 있습니다. job이 큐에 추가되었는지 검증하고 수신된 데이터를 검사할 수 있습니다. fake를 사용하면, 테스트 중인 코드가 실행된 뒤에 검증(assertions) 수행됩니다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use App\Jobs\ShipOrder;
    use Illuminate\Support\Facades\Queue;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;

    class ExampleTest extends TestCase
    {
        public function testOrderShipping()
        {
            Queue::fake();

            // Assert that no jobs were pushed...
            Queue::assertNothingPushed();

            // Perform order shipping...

            Queue::assertPushed(ShipOrder::class, function ($job) use ($order) {
                return $job->order->id === $order->id;
            });

            // Assert a job was pushed to a given queue...
            Queue::assertPushedOn('queue-name', ShipOrder::class);

            // Assert a job was pushed twice...
            Queue::assertPushed(ShipOrder::class, 2);

            // Assert a job was not pushed...
            Queue::assertNotPushed(AnotherJob::class);

            // Assert a job was pushed with a specific chain...
            Queue::assertPushedWithChain(ShipOrder::class, [
                AnotherJob::class,
                FinalJob::class
            ]);
        }
    }

<a name="storage-fake"></a>
## Storage Fake
## Storage Fake

The `Storage` facade's `fake` method allows you to easily generate a fake disk that, combined with the file generation utilities of the `UploadedFile` class, greatly simplifies the testing of file uploads. For example:

`Storage` 파사드의 `fake` 메소드는 `UploadedFile` 클래스의 파일 생성 유틸리티와 결합된 가짜 디스크를 쉽게 생성할 수 있어서, 파일 업로드 테스트가 매우 단순화 됩니다. 예를 들면,

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use Illuminate\Http\UploadedFile;
    use Illuminate\Support\Facades\Storage;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;

    class ExampleTest extends TestCase
    {
        public function testAlbumUpload()
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

> {tip} By default, the `fake` method will delete all files in its temporary directory. If you would like to keep these files, you may use the "persistentFake" method instead.

> {tip} 기본적으로, `fake` 메소드는 임시 디렉토리에 있는 모든 파일 삭제합니다. 이 파일들을 유지하려면, "persistentFake" 메소드를 대신해서 사용할 수 있습니다.

<a name="mocking-facades"></a>
## Facades
## 파사드

Unlike traditional static method calls, [facades](/docs/{{version}}/facades) may be mocked. This provides a great advantage over traditional static methods and grants you the same testability you would have if you were using dependency injection. When testing, you may often want to mock a call to a Laravel facade in one of your controllers. For example, consider the following controller action:

전통적인 정적 메소드의 호출과 다르게, [파사드](/docs/{{version}}/facades)는 mock을 할 수 있습니다. 기존의 전통적인 정적 메소드보다 큰 이점을 제공하며, 의존성 주입을 사용하는 경우와 동일한 테스트 가능성을 제공합니다. 테스트할 때 종종 컨트롤러 중 하나에서 라라벨 파사드에 대한 호출을 mock하고 싶을 것입니다. 예를 들어 다음의 컨트롤러 액션을 고려하십시오.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Support\Facades\Cache;

    class UserController extends Controller
    {
        /**
         * Show a list of all users of the application.
         *
         * @return Response
         */
        public function index()
        {
            $value = Cache::get('key');

            //
        }
    }

We can mock the call to the `Cache` facade by using the `shouldReceive` method, which will return an instance of a [Mockery](https://github.com/padraic/mockery) mock. Since facades are actually resolved and managed by the Laravel [service container](/docs/{{version}}/container), they have much more testability than a typical static class. For example, let's mock our call to the `Cache` facade's `get` method:

[Mockery](https://github.com/padraic/mockery) mock의 인스턴스를 반환하는 `shouldReceive` 메소드를 사용하여 `Cache` 파사드에 대한 호출을 mock 할 수 있습니다. 파사드는 실제로 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에 의해 결정되고 관리된 이후로, 일반적인 정적 클래스보다 테스트 능력이 훨씬 많습니다. 예를 들어, `Cache` 파사드의 `get` 메소드에 대한 호출을 mocking 해 봅시다.

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use Illuminate\Support\Facades\Cache;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;

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

> {note} You should not mock the `Request` facade. Instead, pass the input you desire into the HTTP helper methods such as `get` and `post` when running your test. Likewise, instead of mocking the `Config` facade, call the `Config::set` method in your tests.

> {note} `Request` 파사드를 mock 해서는 안됩니다. 대신에, 테스트를 실행할 때 원하는 입력을 `get` 과 `post` 와 같은 HTTP 헬퍼 메소드에 전달하십시오. 마찬가지로, `Config` 파사드를 mocking 하는 대신에, 테스트에서 `Config::set` 메소드를 호출하십시오.
