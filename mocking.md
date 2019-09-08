# Mocking-모의 객체

- [시작하기](#introduction)
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

라라벨은 기본적으로 이벤트, job 그리고 파사드에 대한 mock 헬퍼를 제공합니다. 이 헬퍼들은 주로 Mockery에서 작동하는 편리한 레이어를 제공하고 있기 때문에, 수동으로 복잡한 Mockery 메소드를 호출할 필요가 없습니다. 물론, 여러분의 고유한 mock 이나 spy 객체를 만드는데 자유롭게 [Mockery](http://docs.mockery.io/en/latest/)나 PHPUnit을 사용할 수 있습니다.

<a name="bus-fake"></a>
## Bus Fake

mocking하는 대신에, `Bus` 파사드의 `fake` 메소드를 사용하여 실제 job이 처리되는 것을 방지할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다:

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

모든 이벤트 리스너가 실행되는걸 방지하기 위해서 mocking 을 사용하는 대신, `Event` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 이벤트가 처리되었는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다:

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

> {note} `Event::fake()`를 호출하면, 모든 이벤트 리스너가 실행되지 않게됩니다. 따라서 모델의 `creating` 이벤트 중에 UUID를 생성하는 것과 같이 이벤트에 의존하는 모델 팩토리를 사용하는 테스트의 경우에는, **팩토리를 사용한 다음에** `Event::fake()`를 호출해야 합니다.

<a name="scoped-event-fakes"></a>
### Scoped Event Fakes

테스트의 일부분 만 이벤트 리스너에게 fake 시키고 싶다면 `fakeFor` 메소드를 사용할 수 있습니다:

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

메일이 발송되는 것을 방지하기 위해서 `Mail` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 [mailable](/docs/{{version}}/mail)객체가 사용자에게 보내졌는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다:

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

백그라운드에서 메일을 보내기 위해서 queue를 사용했다면, `assertSent` 대신에 `assertQueued` 메소드를 사요해야 합니다:

    Mail::assertQueued(...);
    Mail::assertNotQueued(...);

<a name="notification-fake"></a>
## Notification Fake

알림이 발송되는 것을 방지하기 위해서 `Notification ` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 [알림-notification](/docs/{{version}}/notifications)이 사용자에게 보내졌는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다:

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use App\Notifications\OrderShipped;
    use Illuminate\Support\Facades\Notification;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;

    class ExampleTest extends TestCase
    {
        public function testOrderShipping()
        {
            Notification::fake();

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
        }
    }

<a name="queue-fake"></a>
## Queue Fake

큐를 통해서 job이 실행되는 것을 방지하기 위해서 mocking 을 사용하는 대신, `Queue ` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 job이 큐에 추가되었는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다:

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
        }
    }

<a name="storage-fake"></a>
## Storage Fake

`Storage` 파사드의 `fake` 메소드를 사용하면 `UploadedFile` 클래스의 파일 생성과 결합된 가짜 디스크를 생성할 수 있어, 파일 업로드 테스트가 아주 쉬워집니다. 예를 들면:

    <?php

    namespace Tests\Feature;

    use Tests\TestCase;
    use Illuminate\Http\UploadedFile;
    use Illuminate\Support\Facades\Storage;
    use Illuminate\Foundation\Testing\RefreshDatabase;
    use Illuminate\Foundation\Testing\WithoutMiddleware;

    class ExampleTest extends TestCase
    {
        public function testAvatarUpload()
        {
            Storage::fake('avatars');

            $response = $this->json('POST', '/avatar', [
                'avatar' => UploadedFile::fake()->image('avatar.jpg')
            ]);

            // Assert the file was stored...
            Storage::disk('avatars')->assertExists('avatar.jpg');

            // Assert a file does not exist...
            Storage::disk('avatars')->assertMissing('missing.jpg');
        }
    }

> {tip} 기본적으로, `fake` 메소드는 임시 디렉토리에 있는 모든 파일 삭제합니다. 이 파일들을 유지하고자 한다면, 대신 "persistentFake" 메소드를 사용하십시오.

<a name="mocking-facades"></a>
## 파사드

전통적인 스태틱 메소드의 호출과 다르게, [파사드](/docs/{{version}}/facades)는 mock이 가능합니다. 이는 이전의 전통적인 스태틱 메소드와 대비해 훨씬 큰 이점을 제공하며 의존성 주입을 사용하는 경우와 동일한 테스트 가능성을 가질 수 있습니다. 테스트를 수행할 때 곧잘 컨트롤러 안에서의 파사드를 호출을 mock하고 싶을 것입니다. 예를 들어 다음의 컨트롤러 액션을 살펴보겠습니다:

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

> {note} 여러분은 `Request` 파사드를 mock 해서는 안됩니다. 대신에, 테스트를 실행할 때 여러분이 원하는 입력을 `get` 과 `post` 와 같은 HTTP 헬퍼 메소드에 전달하십시오. 마찬가지로, `Config` 파사드를 mocking 하는 대신에, 테스트에서 `Config::set` 메소드를 호출하십시오.
