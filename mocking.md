# Mocking-모의 객체

- [시작하기](#introduction)
- [Mocking 객체](#mocking-objects)
- [Bus Fake](#bus-fake)
- [Event Fake](#event-fake)
    - [Scoped Event Fakes](#scoped-event-fakes)
- [Mail Fake](#mail-fake)
- [Notification Fake](#notification-fake)
- [Queue Fake](#queue-fake)
- [Storage Fake](#storage-fake)
- [파사드](#mocking-facades)

<a name="introduction"></a>
## 시작하기

라라벨 애플리케이션을 테스팅할 때, 애플리케이션의 특정 부분을 "mock"하여 주어진 테스트가 진행될 때 실제로 실행되지 않도록 할 수 있습니다. 예를 들어 이벤트를 발생시키는 컨트롤러를 테스트 할 때, 이벤트 리스너가 테스트 중에 실제로 실행되지 않도록 mock할 수 있습니다. 이렇게하면 여러분은 이벤트 리스너는 자체 테스트 케이스에서 테스트 할 수 있고, 따라서 이벤트 리스너가 실행될것에 대한 걱정을 하지 않고, 컨트롤러의 HTTP response-응답만 테스트할 수 있습니다.

라라벨은 기본적으로 이벤트, job 그리고 파사드에 대한 mock 헬퍼를 제공합니다. 이 헬퍼들은 주로 Mockery에서 작동하는 편리한 레이어를 제공하고 있기 때문에, 수동으로 복잡한 Mockery 메소드를 호출할 필요가 없습니다. 여러분의 고유한 mock 이나 spy 객체를 만드는데 자유롭게 [Mockery](http://docs.mockery.io/en/latest/)나 PHPUnit을 사용할 수 있습니다.

<a name="mocking-objects"></a>
## Mocking 객체

라라벨 서비스 컨테이너를 통하여 여러분의 애플리케이션에 주입되는 객체를 moking할 때에는, 컨테이너의 `instance` 바인딩을 사용해서 mocking한 인스턴스를 등록할 필요가 있습니다. 이는 컨테이너에 객체를 생성하는 대신에 mocking 된 객체를 사용하도록 알려줍니다.

    use App\Service;
    use Mockery;

    $this->instance(Service::class, Mockery::mock(Service::class, function ($mock) {
        $mock->shouldReceive('process')->once();
    }));

보다 편하게 사용하기 위해서, 라라벨의 기본 테스트 케이스 클래스가 제공하는 `mock` 메소드를 사용할 수도 있습니다.

    use App\Service;

    $this->mock(Service::class, function ($mock) {
        $mock->shouldReceive('process')->once();
    });

객체의 몇 가지 메소드만 mocking해야 할 때 `partialMock` 메소드를 사용할 수 있습니다. mocking되지 않은 메소드는 다음과 같이 호출 될 때 정상적으로 실행됩니다.

    use App\Service;

    $this->partialMock(Service::class, function ($mock) {
        $mock->shouldReceive('process')->once();
    });

비슷하게, 객체를 감시하고 싶다면 Laravel의 기본 테스트 케이스 클래스는 `Mockery::spy` 메소드를 둘러싼 편리한 래퍼로서 `spy` 메소드를 제공합니다.

    use App\Service;

    $this->spy(Service::class, function ($mock) {
        $mock->shouldHaveReceived('process');
    });

<a name="bus-fake"></a>
## Bus Fake

mocking하는 대신에, `Bus` 파사드의 `fake` 메소드를 사용하여 실제 job이 처리되는 것을 방지할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다.

    <?php

    namespace Tests\Feature;

    use App\Jobs\ShipOrder;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Support\Facades\Bus;
    use Tests\TestCase;

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

모든 이벤트 리스너가 실행되는걸 방지하기 위해서 mocking 을 사용하는 대신, `Event` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 이벤트가 처리되었는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다.

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

<a name="scoped-event-fakes"></a>
### Scoped Event Fakes

테스트의 일부분 만 이벤트 리스너에게 fake 시키고 싶다면 `fakeFor` 메소드를 사용할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use App\Events\OrderCreated;
    use App\Order;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Support\Facades\Event;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Tests\TestCase;

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

메일이 발송되는 것을 방지하기 위해서 `Mail` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 [mailable](/docs/{{version}}/mail)객체가 사용자에게 보내졌는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다.

    <?php

    namespace Tests\Feature;

    use App\Mail\OrderShipped;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Support\Facades\Mail;
    use Tests\TestCase;

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

백그라운드에서 메일을 보내기 위해서 queue를 사용했다면, `assertSent` 대신에 `assertQueued` 메소드를 사요해야 합니다.

    Mail::assertQueued(...);
    Mail::assertNotQueued(...);

<a name="notification-fake"></a>
## Notification Fake

알림이 발송되는 것을 방지하기 위해서 `Notification ` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 [알림-notification](/docs/{{version}}/notifications)이 사용자에게 보내졌는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다.

    <?php

    namespace Tests\Feature;

    use App\Notifications\OrderShipped;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Notifications\AnonymousNotifiable;
    use Illuminate\Support\Facades\Notification;
    use Tests\TestCase;

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

            // Assert Notification::route() method sent notification to the correct user...
            Notification::assertSentTo(
                new AnonymousNotifiable,
                OrderShipped::class,
                function ($notification, $channels, $notifiable) use ($user) {
                    return $notifiable->routes['mail'] === $user->email;
                }
            );
        }
    }

<a name="queue-fake"></a>
## Queue Fake

큐를 통해서 job이 실행되는 것을 방지하기 위해서 mocking 을 사용하는 대신, `Queue ` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 job이 큐에 추가되었는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다.

    <?php

    namespace Tests\Feature;

    use App\Jobs\ShipOrder;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Support\Facades\Queue;
    use Tests\TestCase;

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

`Storage` 파사드의 `fake` 메소드를 사용하면 `UploadedFile` 클래스의 파일 생성과 결합된 가짜 디스크를 생성할 수 있어, 파일 업로드 테스트가 아주 쉬워집니다. 예를 들면:

    <?php

    namespace Tests\Feature;

    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Http\UploadedFile;
    use Illuminate\Support\Facades\Storage;
    use Tests\TestCase;

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

> {tip} 기본적으로, `fake` 메소드는 임시 디렉토리에 있는 모든 파일 삭제합니다. 이 파일들을 유지하고자 한다면, 대신 "persistentFake" 메소드를 사용하십시오.

<a name="mocking-facades"></a>
## 파사드

전통적인 스태틱 메소드의 호출과 다르게, [파사드](/docs/{{version}}/facades)는 mock이 가능합니다. 이는 이전의 전통적인 스태틱 메소드와 대비해 훨씬 큰 이점을 제공하며 의존성 주입을 사용하는 경우와 동일한 테스트 가능성을 가질 수 있습니다. 테스트를 수행할 때 곧잘 컨트롤러 안에서의 파사드를 호출을 mock하고 싶을 것입니다. 예를 들어 다음의 컨트롤러 액션을 살펴보겠습니다.

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

[Mockery](https://github.com/padraic/mockery) mock을 돌려주는 인스턴스를 반환하는 `shouldReceive` 메소드를 사용하여 `Cache` 파사드 호출을 mock 할 수 있습니다. 파사드는 실제로 라라벨의 [서비스 프로바이더](/docs/{{version}}/container)에 의해서 의존성이 해결되기 때문에, 일반적인 스태틱 클래스보다 테스트가 용이합니다.

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

> {note} 여러분은 `Request` 파사드를 mock 해서는 안됩니다. 대신에, 테스트를 실행할 때 여러분이 원하는 입력을 `get` 과 `post` 와 같은 HTTP 헬퍼 메소드에 전달하십시오. 마찬가지로, `Config` 파사드를 mocking 하는 대신에, 테스트에서 `Config::set` 메소드를 호출하십시오.
