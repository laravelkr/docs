# Mocking-모의 객체

- [시작하기](#introduction)
- [Mocking 객체](#mocking-objects)
- [Mocking 파사드](#mocking-facades)
    - [파사드 Spies](#facade-spies)
- [Bus Fake](#bus-fake)
    - [Job 체인](#bus-job-chains)
    - [Job 배치](#job-batches)
- [Event Fake](#event-fake)
    - [Scoped Event Fakes](#scoped-event-fakes)
- [HTTP Fake](#http-fake)
- [Mail Fake](#mail-fake)
- [Notification Fake](#notification-fake)
- [Queue Fake](#queue-fake)
    - [Job 체인](#job-chains)
- [Storage Fake](#storage-fake)
- [시간과의 상호작용](#interacting-with-time)

## 시작하기

라라벨 애플리케이션을 테스팅할 때, 애플리케이션의 특정 부분을 "mock"하여 주어진 테스트가 진행될 때 실제로 실행되지 않도록 할 수 있습니다. 예를 들어 이벤트를 발생시키는 컨트롤러를 테스트 할 때, 이벤트 리스너가 테스트 중에 실제로 실행되지 않도록 mock할 수 있습니다. 이렇게하면 여러분은 이벤트 리스너는 자체 테스트 케이스에서 테스트 할 수 있고, 따라서 이벤트 리스너가 실행될것에 대한 걱정을 하지 않고, 컨트롤러의 HTTP response-응답만 테스트할 수 있습니다.

라라벨은 기본적으로 이벤트, job 그리고 파사드에 대한 mock 헬퍼를 제공합니다. 이 헬퍼들은 주로 Mockery에서 작동하는 편리한 레이어를 제공하고 있기 때문에, 수동으로 복잡한 Mockery 메소드를 호출할 필요가 없습니다. 여러분의 고유한 mock 이나 spy 객체를 만드는데 자유롭게 [Mockery](http://docs.mockery.io/en/latest/) 또는 PHPUnit을 사용할 수 있습니다.

## Mocking 객체

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

보다 편하게 사용하기 위해서, 라라벨의 기본 테스트 케이스 클래스가 제공하는 `mock` 메소드를 사용할 수도 있습니다. 예를 들어 다음 예는 위의 예와 동일합니다.

    use App\Service;
    use Mockery\MockInterface;

    $this->mock(Service::class, function (MockInterface $mock) {
        $mock->shouldReceive('process')->once();
    });

객체의 몇 가지 메소드만 mocking해야 할 때 `partialMock` 메소드를 사용할 수 있습니다. mocking되지 않은 메소드는 다음과 같이 호출 될 때 정상적으로 실행됩니다.

    use App\Service;
    use Mockery\MockInterface;

    $this->partialMock(Service::class, function (MockInterface $mock) {
        $mock->shouldReceive('process')->once();
    });

비슷하게, 객체를 [spy](http://docs.mockery.io/en/latest/reference/spies.html) 하고 싶다면 Laravel의 기본 테스트 케이스 클래스는 `Mockery::spy` 메소드를 둘러싼 편리한 래퍼로서 `spy` 메소드를 제공합니다. spy는 mock과 비슷합니다. 그러나 spy는 spy와 테스트 중인 코드 사이의 모든 상호 작용을 기록하므로 코드가 실행된 후 검증(assertions)을 할 수 있습니다.

    use App\Service;

    $spy = $this->spy(Service::class);

    // ...

    $spy->shouldHaveReceived('process');

## Mocking 파사드

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

> {note} `Request` 파사드를 mock해서는 안 됩니다. 대신 테스트를 실행할 때 `get` 및 `post`와 같은 [HTTP 테스트 방법](/docs/{{version}}/http-tests)에 원하는 입력을 전달하세요. 마찬가지로, `Config` 파사드를 mocking하는 대신 테스트에서 `Config::set` 메소드를 호출하세요.

### Facade Spies

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

job을 처리하는 코드를 테스트할 때 일반적으로 주어진 job이 처리되었지만 실제로 job을 queue에 넣거나 실행하지는 않았다고 검증하려고 합니다. job 실행이 일반적으로 별도의 테스트 클래스에서 테스트될 수 있기 때문입니다.

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

주어진 "실제 테스트"를 통과하는 job이 처리되었음을 확인하기 위해 closure를 `assertDispatched` 또는 `assertNotDispatched`로 전달할 수 있습니다. 주어진 실제 테스트를 통과하는 적어도 하나의 job이 처리되면 검증이 성공할 것입니다. 예를 들어 특정 주문에 대해 job이 처리되었다고 검증할 수 있습니다.

    Bus::assertDispatched(function (ShipOrder $job) use ($order) {
        return $job->order->id === $order->id;
    });

### Job 체인

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

위의 예에서 볼 수 있듯이 연결된 job의 배열은 job의 클래스 이름의 배열일 수 있습니다. 그러나 실제 job 인스턴스의 배열을 제공할 수도 있습니다. 그렇게 할 때 Laravel은 job 인스턴스가 동일한 클래스에 있고 애플리케이션에서 전달한 연결 job의 동일한 속성 값을 갖도록 합니다.

    Bus::assertChained([
        new ShipOrder,
        new RecordShipment,
        new UpdateInventory,
    ]);

### Job 배치

`Bus` 파사드의 `assertBatched` 메소드는 [Job 배치](/docs/{{version}}/queues#job-batching)가 처리되었음을 확인하는 데 사용할 수 있습니다. `assertBatched` 메소드에 주어진 클로저는 `Illuminate\Bus\PendingBatch`의 인스턴스를 수신하며, 이는 배치 내의 작업을 검사하는 데 사용할 수 있습니다.

    use Illuminate\Bus\PendingBatch;
    use Illuminate\Support\Facades\Bus;

    Bus::assertBatched(function (PendingBatch $batch) {
        return $batch->name == 'import-csv' &&
               $batch->jobs->count() === 10;
    });

## Event Fake

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

주어진 "실제 테스트"를 통과하는 이벤트가 전달되었는지 확인하기 위해 클로저를 `assertDispatched` 또는 `assertNotDispatched`로 전달할 수 있습니다. 주어진 실제 테스트를 통과하는 하나 이상의 이벤트가 전달되면 검증이 성공합니다.

    Event::assertDispatched(function (OrderShipped $event) use ($order) {
        return $event->order->id === $order->id;
    });

이벤트 리스너가 주어진 이벤트를 수신하고 있다고 단순히 검증하고 싶다면 `assertListening` 메소드를 사용할 수 있습니다.

    Event::assertListening(
        OrderShipped::class,
        SendShipmentNotification::class
    );


> {note} `Event::fake()`를 호출하면, 모든 이벤트 리스너가 실행되지 않게됩니다. 따라서 모델의 `creating` 이벤트 중에 UUID를 생성하는 것과 같이 이벤트에 의존하는 모델 팩토리를 사용하는 테스트의 경우에는, **팩토리를 사용한 다음에** `Event::fake()`를 호출해야 합니다.

#### 이벤트의 일부를 Fake 시키기

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

`Http` 파사드의 `fake` 메소드를 사용하면 HTTP 클라이언트가 요청이있을 때 스텁 / 더미 응답을 반환하도록 지시 할 수 있습니다. Fake HTTP 요청에 대한 자세한 내용은 [HTTP 클라이언트 테스트 문서](/docs/{{version}}/http-client#testing)를 참조하세요.

## Mail Fake

Mail 파사드의 fake 메서드를 사용하여 메일이 전송되는 것을 방지할 수 있습니다. 일반적으로 메일을 보내는 것은 실제로 테스트하는 코드와 관련이 없습니다. 아마도 Laravel이 주어진 mailable객체를 보냈다고 검증하는 것으로 충분할 것입니다.

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

백그라운드에서 메일을 보내기 위해서 queue를 사용했다면, `assertSent` 대신에 `assertQueued` 메소드를 사용해야 합니다.

    Mail::assertQueued(OrderShipped::class);

    Mail::assertNotQueued(OrderShipped::class);

    Mail::assertNothingQueued();

주어진 "실제 테스트"를 통과한 mailable객체가 전송되었음을 확인하기 위해 `assertSent` 또는 `assertNotSent` 메소드에 클로저를 전달할 수 있습니다. 주어진 실제 테스트를 통과하는 적어도 하나의 mailable객체가 전송되면 검증이 성공할 것입니다.

    Mail::assertSent(function (OrderShipped $mail) use ($order) {
        return $mail->order->id === $order->id;
    });

`Mail` 파사드의 검증 메소드를 호출할 때, 제공된 클로저에 의해 받아들여진 mailable 인스턴스는 mailable객체의 수신자를 검사하기 위한 유용한 메소드를 노출합니다.

    Mail::assertSent(OrderShipped::class, function ($mail) use ($user) {
        return $mail->hasTo($user->email) &&
               $mail->hasCc('...') &&
               $mail->hasBcc('...');
    });

메일이 전송되지 않았는지 확인하는 방법에는 `assertNotSent`와 `assertNotQueued`가 있습니다. 때로는 **메일이 전송되지 않았거나 대기열에 추가되지 않았다고** 검증하고 싶을 수도 있습니다. 이를 위해 `assertNothingOutgoing` 및 `assertNotOutgoing` 메소드를 사용할 수 있습니다.

    Mail::assertNothingOutgoing();

    Mail::assertNotOutgoing(function (OrderShipped $mail) use ($order) {
        return $mail->order->id === $order->id;
    });

## Notification Fake

알림이 발송되는 것을 방지하기 위해서 `Notification ` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 [알림-notification](/docs/{{version}}/notifications)이 사용자에게 보내졌는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이 수행됩니다.

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

주어진 "실제 테스트"를 통과하는 알림이 전송되었음을 검증하기 위해 `assertSentTo` 또는 `assertNotSentTo` 메소드에 클로저를 전달할 수 있습니다. 주어진 실제 테스트를 통과하는 하나 이상의 알림이 전송되면 검증이 성공합니다.

    Notification::assertSentTo(
        $user,
        function (OrderShipped $notification, $channels) use ($order) {
            return $notification->order->id === $order->id;
        }
    );

#### 대상을 지정한 Notifications

테스트 중인 코드가 [대상을 지정한 notifications](/docs/{{version}}/notifications#on-demand-notifications)을 보내는 경우 알림이 `Illuminate\Notifications\AnonymousNotifiable` 인스턴스로 전송되었음을 검증해야 합니다.

    use Illuminate\Notifications\AnonymousNotifiable;

    Notification::assertSentTo(
        new AnonymousNotifiable, OrderShipped::class
    );

알림 검증 메서드에 대한 세 번째 인수로 클로저를 전달하면 대상을 지정한 notifications가 올바른 "경로" 주소로 전송되었는지 확인할 수 있습니다.

    Notification::assertSentTo(
        new AnonymousNotifiable,
        OrderShipped::class,
        function ($notification, $channels, $notifiable) use ($user) {
            return $notifiable->routes['mail'] === $user->email;
        }
    );

## Queue Fake

큐에 있는 job이 큐에 푸시되는 것을 방지하기 위해서 mocking 을 사용하는 대신, `Queue ` 파사드의 `fake` 메소드를 사용할 수 있습니다. 대기 중인 작업 자체가 다른 테스트 클래스에서 테스트될 수 있기 때문에 Laravel이 주어진 job을 대기열로 푸시하도록 지시받았다고 간단히 검증하는 것으로 충분합니다.

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

주어진 "실제 테스트"를 통과하는 job이 푸시되었다고 검증하기 위해 클로저를 `assertPushed` 또는 `assertNotPushed` 메서드에 전달할 수 있습니다. 주어진 실제 테스트를 통과하는 하나 이상의 job이 푸시되면 검증이 성공합니다.

    Queue::assertPushed(function (ShipOrder $job) use ($order) {
        return $job->order->id === $order->id;
    });

### Job 체인

`Queue` 파사드의 `assertPushedWithChain` 및 `assertPushedWithoutChain` 메소드를 사용하여 푸시된 job의 job 체인을 검사할 수 있습니다. `assertPushedWithChain` 메소드는 최초 작업을 첫 번째 인수로, 연결된 작업의 배열을 두 번째 인수로 허용합니다.

    use App\Jobs\RecordShipment;
    use App\Jobs\ShipOrder;
    use App\Jobs\UpdateInventory;
    use Illuminate\Support\Facades\Queue;

    Queue::assertPushedWithChain(ShipOrder::class, [
        RecordShipment::class,
        UpdateInventory::class
    ]);

위의 예에서 볼 수 있듯이 job 체인의 배열은 job의 클래스 이름의 배열일 수 있습니다. 그러나 실제 작업 인스턴스의 배열을 제공할 수도 있습니다. 그렇게 할 때 Laravel은 job 인스턴스가 동일한 클래스에 있고 애플리케이션에서 전달한 job 체인의 동일한 속성 값을 갖도록 합니다.

    Queue::assertPushedWithChain(ShipOrder::class, [
        new RecordShipment,
        new UpdateInventory,
    ]);

`assertPushedWithoutChain` 메소드를 사용하여 job 체인 없이 job이 푸시되었음을 확인할 수 있습니다.

    Queue::assertPushedWithoutChain(ShipOrder::class);

## Storage Fake

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

파일 업로드 테스트에 대한 자세한 내용은 [HTTP 테스트 문서의 파일 업로드 테스트하기](/docs/{{version}}/http-tests#testing-file-uploads)를 참조하세요.

> {tip} 기본적으로, `fake` 메소드는 임시 디렉토리에 있는 모든 파일 삭제합니다. 이 파일들을 유지하고자 한다면, 대신 "persistentFake" 메소드를 사용하십시오.

## 시간과의 상호작용

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
