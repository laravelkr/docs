# Mocking-모의 객체

- [소개하기](#introduction)
- [이벤트](#mocking-events)
    - [Mock 사용하기](#using-event-mocks)
    - [Fakes 사용하기](#using-event-fakes)
- [Jobs](#mocking-jobs)
    - [Mocks 사용하기](#using-job-mocks)
    - [Fakes 사용하기](#using-job-fakes)
- [Mail Fakes](#mail-fakes)
- [Notification Fakes](#notification-fakes)
- [파사드](#mocking-facades)

<a name="introduction"></a>
## 소개하기

라라벨 애플리케이션을 테스팅할 때, 애플리케이션의 특정 부분을 "mock"하여 주어진 테스트가 진행될 때 실제로 실행되지 않도록 할 수 있습니다. 예를 들어 이벤트를 발생시키는 컨트롤러를 테스트 할 때, 이벤트 리스너가 테스트 중에 실제로 실행되지 않도록 mock할 수 있습니다. 이렇게하면 여러분은 이벤트 리스너는 자체 테스트 케이스에서 테스트 할 수 있고, 따라서 이벤트 리스너가 실행될것에 대한 걱정을 하지 않고, 컨트롤러의 HTTP response-응답만 테스트할 수 있습니다.

라라벨은 기본적으로 이벤트, job 그리고 파사드에 대한 mock 헬퍼를 제공합니다. 이 헬퍼들은 주로 Mockery에서 작동하는 편리한 레이어를 제공하고 있기 때문에, 수동으로 복잡한 Mockery 메소드를 호출할 필요가 없습니다. 물론, 여러분의 고유한 mock 이나 spy 객체를 만드는데 자유롭게 [Mockery](http://docs.mockery.io/en/latest/)나 PHPUnit을 사용할 수 있습니다.

<a name="mocking-events"></a>
## 이벤트

<a name="using-event-mocks"></a>
### Mock 사용하기

라라벨의 이벤트 시스템을 많이 사용하고 있는 경우, 테스팅하는 동안 특정 이벤트를 조용하게 하거나(꺼두거나) mock 하고자 할 수 있습니다. 예를 들어 사용자 가입을 테스팅하는 경우, `UserRegistered` 이벤트가 발생되어 리스너가 "환영" 이메일을 보내는 것을 원하지는 않을 것입니다. 

라라벨은 실행중 예상되는 이벤트가 리스너를 통하지 않고도 발생하였는지 확인하는 편리한 `expectsEvents` 메소드를 제공합니다.:

    <?php

    use App\Events\UserRegistered;

    class ExampleTest extends TestCase
    {
        /**
         * Test new user registration.
         */
        public function testUserRegistration()
        {
            $this->expectsEvents(UserRegistered::class);

            // Test user registration...
        }
    }

주어진 이벤트가 발생하지 않았는지 확인하는 `doesntExpectEvents` 메소드를 사용할 수도 있습니다:

    <?php

    use App\Events\OrderShipped;
    use App\Events\OrderFailedToShip;

    class ExampleTest extends TestCase
    {
        /**
         * Test order shipping.
         */
        public function testOrderShipping()
        {
            $this->expectsEvents(OrderShipped::class);
            $this->doesntExpectEvents(OrderFailedToShip::class);

            // Test order shipping...
        }
    }

실행중인 모든 이벤트 리스너를 방지하려면, `withoutEvents` 메소드를 사용할 수 있습니다. 이 메소드가 호출 되면, 모든 이벤트 리스너가 mocking 됩니다:

    <?php

    class ExampleTest extends TestCase
    {
        public function testUserRegistration()
        {
            $this->withoutEvents();

            // Test user registration code...
        }
    }

<a name="using-event-fakes"></a>
### Fake 사용하기

모든 이벤트 리스너가 실행되는걸 방지하기 위해서 mocking 을 사용하는 대신, `Event` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 이벤트가 발생했는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다:

    <?php

    use App\Events\OrderShipped;
    use App\Events\OrderFailedToShip;
    use Illuminate\Support\Facades\Event;

    class ExampleTest extends TestCase
    {
        /**
         * Test order shipping.
         */
        public function testOrderShipping()
        {
            Event::fake();

            // Perform order shipping...

            Event::assertFired(OrderShipped::class, function ($e) use ($order) {
                return $e->order->id === $order->id;
            });

            Event::assertNotFired(OrderFailedToShip::class);
        }
    }

<a name="mocking-jobs"></a>
## Jobs

<a name="using-job-mocks"></a>
### Mock 사용하기

때로는 어플리케이에 request을 수행할 때 주어진 작업이 진행되는지 테스트 하고자 할 수 있습니다. 이렇게 하면 job의 로직에 관계없이 여러분의 라우트와 컨트롤러를 독립적으로 테스트 할 수 있습니다. 물론, 별도의 테스트 케이스에서 job을 테스트 해야 합니다. 

라라벨은 처리된 job이 실행되었는지 확인할 수 있는 편리한 `expectsJobs` 메소드를 제공합니다. 하지만 이때 job 자체는 실행되지 않습니다:

    <?php

    use App\Jobs\ShipOrder;

    class ExampleTest extends TestCase
    {
        public function testOrderShipping()
        {
            $this->expectsJobs(ShipOrder::class);

            // Test order shipping...
        }
    }

> {note} 이 메소드는 단지 `DispatchesJobs` 트레이트의 dispatch 메소드 혹은 `dispatch` 헬퍼 함수를 통해서 처리되는 job 만을 감지합니다. 이는 `Queue::push`를 통해서 바로 전달되어 queue를 통해서 처리되는 job을 감지하지는 않습니다.
 
이벤트 mocking 헬퍼와 같이, 또한 `doesntExpectJobs` 메소드를 사용ㅎ여 job이 처리되지 않았다는 것을 테스트 할 수 있습니다:

    <?php

    use App\Jobs\ShipOrder;

    class ExampleTest extends TestCase
    {
        /**
         * Test order cancellation.
         */
        public function testOrderCancellation()
        {
            $this->doesntExpectJobs(ShipOrder::class);

            // Test order cancellation...
        }
    }

대신에, `withoutJobs` 메소드를 사용하여 모든 job 처리를 무시할 수 있습니다. 테스트 메소드 안에서 이 메소드가 호출되면, 해당 테스트 중에 전달되는 모든 job 이 취소됩니다:

    <?php

    use App\Jobs\ShipOrder;

    class ExampleTest extends TestCase
    {
        /**
         * Test order cancellation.
         */
        public function testOrderCancellation()
        {
            $this->withoutJobs();

            // Test order cancellation...
        }
    }

<a name="using-job-fakes"></a>
### Fake 사용하기

큐를 통해서 job이 실행되는 것을 방지하기 위해서 mocking 을 사용하는 대신, `Queue ` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 job이 큐에 추가되었는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다:

    <?php

    use App\Jobs\ShipOrder;
    use Illuminate\Support\Facades\Queue;

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

            // Assert a job was not pushed...
            Queue::assertNotPushed(AnotherJob::class);
        }
    }

<a name="mail-fakes"></a>
## Mail Fakes

메일이 발송되는 것을 방지하기 위해서 `Mail` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 [mailable](/docs/{{version}}/mail)객체가 사용자에게 보내졌는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다:

    <?php

    use App\Mail\OrderShipped;
    use Illuminate\Support\Facades\Mail;

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
            Mail::assertSentTo([$user], OrderShipped::class);

            // Assert a mailable was not sent...
            Mail::assertNotSent(AnotherMailable::class);
        }
    }

<a name="notification-fakes"></a>
## Notification Fakes

알림이 발송되는 것을 방지하기 위해서 `Notification ` 파사드의 `fake` 메소드를 사용할 수 있습니다. 여러분은 [알림-notification](/docs/{{version}}/notifications)이 사용자에게 보내졌는지 확인하고 데이터가 수신되었는지에 대해서도 검사할 수 있습니다. fake를 사용하면, 테스트 대상 코드가 실행된 뒤에 검증이(assertions) 수행됩니다:

    <?php

    use App\Notifications\OrderShipped;
    use Illuminate\Support\Facades\Notification;

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

    class FooTest extends TestCase
    {
        public function testGetIndex()
        {
            Cache::shouldReceive('get')
                        ->once()
                        ->with('key')
                        ->andReturn('value');

            $this->visit('/users')->see('value');
        }
    }

> {note} 여러분은 `Request` 파사드를 mock 해서는 안됩니다. 대신에, 테스트를 실행할 때 여러분이 원하는 입력을 `call` 과 `post` 와 같은 HTTP 헬퍼 메소드에 전달하십시오. 마찬가지로, `Config` 파사드를 mocking 하는 대신에, 테스트에서 `Config::set` 메소드를 호출하십시오.
