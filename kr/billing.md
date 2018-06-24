# Laravel Cashier
# 라라벨 캐셔

- [Introduction 소개](#introduction)
- [Configuration 설정](#configuration)
- [Subscribing To A Plan 플랜 가입](#subscribing-to-a-plan)
- [Single Charges 한번만 결제하기](#single-charges)
- [No Card Up Front 신용카드 없이 가입](#no-card-up-front)
- [Swapping Subscriptions 구독 변경하기](#swapping-subscriptions)
- [Subscription Quantity 구독 수량](#subscription-quantity)
- [Cancelling A Subscription 구독 취소](#cancelling-a-subscription)
- [Resuming A Subscription 구독 재개](#resuming-a-subscription)
- [Checking Subscription Status 가입 상태 확인](#checking-subscription-status)
- [Handling Failed Subscriptions 구독 실패 처리](#handling-failed-subscriptions)
- [Handling Other Stripe Webhooks 기타 Stripe 후킹 처리](#handling-other-stripe-webhooks)
- [Invoices 청구서](#invoices)

<a name="introduction"></a>
## Introduction
## 소개

Laravel Cashier provides an expressive, fluent interface to [Stripe's](https://stripe.com) subscription billing services. It handles almost all of the boilerplate subscription billing code you are dreading writing. In addition to basic subscription management, Cashier can handle coupons, swapping subscription, subscription "quantities", cancellation grace periods, and even generate invoice PDFs.

라라벨 캐셔는 [Stripe's](https://stripe.com)에 의해서 제공되는 손쉽고 편리한 구독(정기 과금) 서비스를 위한 인터페이스를 제공합니다. 라라벨 캐셔는 여러분이 작성하는데 어려움을 겪는 구독을 위한 청구서에서 필요한 거의 모든 관용구문들을 다룹니다.  기본적인 구독 관리 외에도, 캐셔를 통해서 쿠폰 관리, 구독 변경, 구매 수량 변경, 취소 유예 기간, 그리고 청구서를 PDF로 생성할 수도 있습니다. 

<a name="configuration"></a>
## Configuration
## 설정

#### Composer
#### 컴포저

First, add the Cashier package to your `composer.json` file:
먼저 여러분의 `composer.json` 파일에 캐셔 패키지를 추가해야 합니다. 

	"laravel/cashier": "~5.0" (For Stripe SDK ~2.0, and Stripe APIs on 2015-02-18 version and later)
	"laravel/cashier": "~4.0" (For Stripe APIs on 2015-02-18 version and later)
	"laravel/cashier": "~3.0" (For Stripe APIs up to and including 2015-02-16 version)

#### Service Provider
#### 서비스 프로바이더

Next, register the `Laravel\Cashier\CashierServiceProvider` in your `app` configuration file.

다음으로 `app` 설정 파일에 `Laravel\Cashier\CashierServiceProvider`를 등록해야 합니다. 

#### Migration
#### 마이그레이션

Before using Cashier, we'll need to add several columns to your database. Don't worry, you can use the `cashier:table` Artisan command to create a migration to add the necessary column. For example, to add the column to the users table use `php artisan cashier:table users`. Once the migration has been created, simply run the `migrate` command.

캐셔를 바로 사용하기 전에 몇가지 컬럼을 데이터베이스에 추가해야 합니다. 걱정하지 마십시오. `cachier:table` 아티즌 명령어를 통해서 필요한 컬럼을 추가하기 위한 마이그레이션을 손쉽게 생성할 수 있습니다. 예를 들어 user테이블에 컬럼을 추가하기 위해서 `php artisan cashier:table users`를 사용할 수 있습니다. 마이그레이션이 생성되면 `migrate` 명령으를 실행하십시오. 

#### Model Setup
#### 모델 설정

Next, add the `Billable` trait and appropriate date mutators to your model definition:
다음으로 `Billable` trait와 적당한  날짜 뮤테이터(mutators)을 모델에 추가합니다. 

(역자주 : 뮤테이터는 보통 세터라고 지칭되는, 인스턴스 변수의 값을 설정하는 역활을 합니다. 라라벨의 모델의 경우 마이그레이션을 통해서 추가되는 컬럼에 대한 변경을 수행하는 역활을 한다고 할 수 있습니다. )

	use Laravel\Cashier\Billable;
	use Laravel\Cashier\Contracts\Billable as BillableContract;

	class User extends Model implements BillableContract {

		use Billable;

		protected $dates = ['trial_ends_at', 'subscription_ends_at'];

	}

#### Stripe Key
#### Stripe 키

Finally, set your Stripe key in your `services.php` config file:
마지막으로 `services.php` 설정 파일에 Stripe 키를 지정하십시오. 

	'stripe' => [
		'model'  => 'User',
		'secret' => env('STRIPE_API_SECRET'),
	],

Alternatively you can store it in one of your bootstrap files or service providers, such as the `AppServiceProvider`:
다른 방법으로 Stripe 키를 부트스트랩 파일 또는 `AppServiceProvider` 같은 서비스 프로바이더에서 지정할 수도 있습니다. 

	User::setStripeKey('stripe-key');

<a name="subscribing-to-a-plan"></a>
## Subscribing To A Plan
## 플랜에 가입

Once you have a model instance, you can easily subscribe that user to a given Stripe plan:
모델 인스턴스를 통해서 사용자를 주어진 Stripe 플랜에 쉽게 가입 시킬 수 있습니다:

	$user = User::find(1);

	$user->subscription('monthly')->create($creditCardToken);

If you would like to apply a coupon when creating the subscription, you may use the `withCoupon` method:

구독을 생성할때 쿠폰을 적용하는 경우 `withCoupon` 메소드를 사용하면 됩니다. 

	$user->subscription('monthly')
	     ->withCoupon('code')
	     ->create($creditCardToken);

The `subscription` method will automatically create the Stripe subscription, as well as update your database with Stripe customer ID and other relevant billing information. If your plan has a trial configured in Stripe, the trial end date will also automatically be set on the user record.

`subscription` 메소드는 자동으로 Stripe 구독에 가입하고 여러분의 데이터베이스에 Stripe 고객 ID 와 관련된 결제정보를 업데이트 합니다. Stripe에서 평가 기간(trial)을 설정하는 경우 종료 날짜가 자동으로 사용자 레코드에 설정됩니다. 

If your plan has a trial period that is **not** configured in Stripe, you must set the trial end date manually after subscribing:

만약 Stripe에 평가 기간(trial)을 지정하지 **않았다면**,  구독 후 수동으로 평가기간(trial) 종료일을 설정 해야합니다:

	$user->trial_ends_at = Carbon::now()->addDays(14);

	$user->save();

### Specifying Additional User Details
### 추가적인 사용자 정보 지정하기

If you would like to specify additional customer details, you may do so by passing them as second argument to the `create` method:

만약 여러분이 추가적인 사용자 정보를 지정하고 싶다면 이러한 정보를 `create` 메소드의 두번째 인자로 전달하면 됩니다. 

	$user->subscription('monthly')->create($creditCardToken, [
		'email' => $email, 'description' => 'Our First Customer'
	]);

To learn more about the additional fields supported by Stripe, check out Stripe's [documentation on customer creation](https://stripe.com/docs/api#create_customer).

추가적인 필드에 대한 Stripe 의 지원정보를 확인하고자 한다면 Stripe의 [고객 생성에 관한 문서](https://stripe.com/docs/api#create_customer)를 참고하십시오. 

<a name="single-charges"></a>
## Single Charges
## 한번만 결제하기

If you would like to make a "one off" charge against a subscribed customer's credit card, you may use the `charge` method:

정기 구독하고 있는 고객의 신용 카드에 대해 “일회성 청구”를 하고 싶을 때에는 `charge` 메서드를 사용합니다. 

	$user->charge(100);

The `charge` method accepts the amount you would like to charge in the **lowest denominator of the currency**. So, for example, the example above will charge 100 cents, or $1.00, against the user's credit card.

`charge` 메서드는 청구하려는 **통화의 가장 낮은 기준 금액**을 인수로 받습니다.  따라서 위의 예제는 100 센트 또는 1 달러를 신용 카드로 결제합니다.

The `charge` method accepts an array as its second argument, allowing you to pass any options you wish to the underlying Stripe charge creation:

`charge` 메소드는 두번째 인자로 Stripe 청구에 사용할 수 있는 옵션에 대한 배열을 전달 받습니다. 

	$user->charge(100, [
		'source' => $token,
		'receipt_email' => $user->email,
	]);

The `charge` method will return `false` if the charge fails. This typically indicates the charge was denied:

`charge` 메소드는 청구가 실패했을 경우에 `false` 를 반환합니다. 일반적으로 이경우는 결제가 거부 되었다는 것을 의미합니다. 

	if ( ! $user->charge(100))
	{
		// The charge was denied...
	}

If the charge is successful, the full Stripe response will be returned from the method.
청구가 성공적으로 완료되었다면 메소드에서는 Stripe 응답 객체가 반환됩니다. 

<a name="no-card-up-front"></a>
## No Card Up Front
## 신용카드 정보 없이 가입

If your application offers a free-trial with no credit-card up front, set the `cardUpFront` property on your model to `false`:

만약 여러분의 애플리케이션이 무료 평가기간(trial) 을 신용카드 정보 없이 제공한다면 모델클래스에 `cardUpFront` 속성을 `false`로 지정하십시오. 

	protected $cardUpFront = false;

On account creation, be sure to set the trial end date on the model:
계정을 생성할때, 모델에 평가기간 종료일을 설정하는 것을 잊지마십시오:

	$user->trial_ends_at = Carbon::now()->addDays(14);

	$user->save();

<a name="swapping-subscriptions"></a>
## Swapping Subscriptions
## 구독 변경

To swap a user to a new subscription, use the `swap` method:
사용자의 구독을 새롭게 변경하고자 한다면 `swap` 메소드를 사용하면 됩니다. 

	$user->subscription('premium')->swap();

If the user is on trial, the trial will be maintained as normal. Also, if a "quantity" exists for the subscription, that quantity will also be maintained.

사용자가 평가기간(trial)중이라면, 평가 기간은 정상적으로 유지됩니다. 또한 구독의 “수량”이 존재하는 경우에도 이 수량은 유지됩니다. 

<a name="subscription-quantity"></a>
## Subscription Quantity
## 구독 수량

Sometimes subscriptions are affected by "quantity". For example, your application might charge $10 per month per user on an account. To easily increment or decrement your subscription quantity, use the `increment` and `decrement` methods:

때로는 구독은 “수량”에 영향을 받을 수 있습니다.  예를 들어 여러분의 애플리케이션에서 하나의 계정의 사용자마다 한달에 10달러를 부과하고 있다고 한다면, `increment` 와 `decrement` 메소드를 사용하여 구독 수를 늘리거나 줄일 수 있습니다. 

	$user = User::find(1);

	$user->subscription()->increment();

	// Add five to the subscription's current quantity...
	$user->subscription()->increment(5);

	$user->subscription()->decrement();

	// Subtract five to the subscription's current quantity...
	$user->subscription()->decrement(5);

<a name="subscription-tax"></a>
## Subscription Tax

With Cashier, it's easy to override the `tax_percent` value sent to Stripe. To specify the tax percentage a user pays on a subscription, implement the `getTaxPercent` method on your model, and return a numeric value between 0 and 100, with no more than 2 decimal places.

	public function getTaxPercent()
	{
		return 20;
	}

This enables you to apply a tax rate on a model-by-model basis, which may be helpful for a user base that spans multiple countries.

<a name="cancelling-a-subscription"></a>
## Cancelling A Subscription
## 구독 취소하기

Cancelling a subscription is a walk in the park:
구독을 취소하는 것은 매우 쉽습니다. 

	$user->subscription()->cancel();

When a subscription is cancelled, Cashier will automatically set the `subscription_ends_at` column on your database. This column is used to know when the `subscribed` method should begin returning `false`. For example, if a customer cancels a subscription on March 1st, but the subscription was not scheduled to end until March 5th, the `subscribed` method will continue to return `true` until March 5th.

구독이 취소되면 캐셔는 자동으로 데이터베이스의 `subscription_ends_at` 컬럼을 설정합니다. 이 컬럼은 언제 `subscribed` 메소드가 `false`를 반환해야 하는지 알기 위해서 사용되어 집니다. 예를 들어 사용자가 구독을 3월 1일에 취소했지만, 정기 구독이 3월 5일에 종료하도록 예정되어 있다면 `subscribed` 메소드는 3월 5일까지 `true`를 반환할 것입니다. 

<a name="resuming-a-subscription"></a>
## Resuming A Subscription
## 구독 다시 시작하기

If a user has cancelled their subscription and you wish to resume it, use the `resume` method:
만약 사용자가 취소한 구독을 재개하려면, `resume` 메서드를 사용하면 됩니다:

	$user->subscription('monthly')->resume($creditCardToken);

If the user cancels a subscription and then resumes that subscription before the subscription has fully expired, they will not be billed immediately. Their subscription will simply be re-activated, and they will be billed on the original billing cycle.

만약 사용자가 구독을 취소하고 다음 정기 구독을 재개하는 경우 그 등록의 만료일이 되기 전까지는 비용이 바로 부과되지는 않습니다. 사용자의 정기 구독은 간단하게 다시 활성화 되며, 원래의 주기에 따라 과금됩니다.

<a name="checking-subscription-status"></a>
## Checking Subscription Status
## 가입 상태 확인

To verify that a user is subscribed to your application, use the `subscribed` method:
사용자가 애플리케이션에 대해 구독중인지 아닌지 확인하려면 `subscribed` 메소드를 사용하면 됩니다. 

	if ($user->subscribed())
	{
		//
	}

The `subscribed` method makes a great candidate for a [route middleware](/docs/{{version}}/middleware):

`subscribed` 메소드는 라우트 미들웨어에 사용될수 있는 좋은 방법중 하나입니다:

	public function handle($request, Closure $next)
	{
		if ($request->user() && ! $request->user()->subscribed())
		{
			return redirect('billing');
		}

		return $next($request);
	}

You may also determine if the user is still within their trial period (if applicable) using the `onTrial` method:

여러분은 또한 사용자가 현재 무료 평가 기간(trial) 을 통해서 이용중인지 확인하기 위해서 `onTrial` 메소드를 사용할 수 있습니다. 

	if ($user->onTrial())
	{
		//
	}

To determine if the user was once an active subscriber, but has cancelled their subscription, you may use the `cancelled` method:

사용자가 이전에 한번 구독 후, 취소 했는지를 확인 하기 위해서 `cancelled` 메서드를 사용할 수 있습니다.

	if ($user->cancelled())
	{
		//
	}

You may also determine if a user has cancelled their subscription, but are still on their "grace period" until the subscription fully expires. For example, if a user cancels a subscription on March 5th that was scheduled to end on March 10th, the user is on their "grace period" until March 10th. Note that the `subscribed` method still returns `true` during this time.

또한 사용자가 구독을 취소하고 있지만 아직 완전히 만료 되기 전의 "유예 기간" 중인지를 확인할 수 있습니다. 예를 들어, 사용자가 3 월 5 일에 구독을 취소하고 3 월 10 일에 만료 될 경우, 해당 사용자는 3 월 10 일까지가 "유예 기간”입니다. `subscribed` 메소드는 이 기간 동안 여전히 `ture` 를 반환하는 것에 유의하십시오. 

	if ($user->onGracePeriod())
	{
		//
	}

The `everSubscribed` method may be used to determine if the user has ever subscribed to a plan in your application:

`everSubscribed` 메소드는 사용자가 애플리케이션을 한번도 구독한적이 없는지 확인하는데 사용됩니다. 

	if ($user->everSubscribed())
	{
		//
	}

The `onPlan` method may be used to determine if the user is subscribed to a given plan based on its ID:

`onPlan` 메소드는 사용자가 지정된 ID에 대당하는 구독 플랜을 이용하는지 확인할 수 있습니다. 

	if ($user->onPlan('monthly'))
	{
		//
	}

<a name="handling-failed-subscriptions"></a>
## Handling Failed Subscriptions
## 구독 실패 처리

What if a customer's credit card expires? No worries - Cashier includes a Webhook controller that can easily cancel the customer's subscription for you. Just point a route to the controller:

만약 사용자의 신용카드가 만료 되었다면? 걱정하지 마십시오 - 캐셔는 사용자의 구독을 쉽게 취소할 수 있는 Webhook 컨트롤러를 포함하고 있습니다. 컨트롤러에 라우트를 지정하면 됩니다.

	Route::post('stripe/webhook', 'Laravel\Cashier\WebhookController@handleWebhook');

That's it! Failed payments will be captured and handled by the controller. The controller will cancel the customer's subscription when Stripe determines the subscription has failed (normally after three failed payment attempts). The `stripe/webhook` URI in this example is just for example. You will need to configure the URI in your Stripe settings.

끝입니다! 실패한 결제는 컨트롤러에 의해 확인되어 처리됩니다. 컨트롤러는 Stripe 가 결제에 실패하였다고 결정되면(보통 3번의 결제 시도가 실패하면) 사용자의 구독을 취소시킬 것입니다. 이 예제에서 `stripe/webhook` URI  는 단순한 예제일 뿐입니다. 여러분은 Stripe 설정에서 URI를 설정해야합니다.

<a name="handling-other-stripe-webhooks"></a>
## Handling Other Stripe Webhooks
## 기타 Stripe Webhook(웹 후킹) 처리하기

If you have additional Stripe webhook events you would like to handle, simply extend the Webhook controller. Your method names should correspond to Cashier's expected convention, specifically, methods should be prefixed with `handle` and the name of the Stripe webhook you wish to handle. For example, if you wish to handle the `invoice.payment_succeeded` webhook, you should add a `handleInvoicePaymentSucceeded` method to the controller.

만약 추가적인 Stripe 웹 후크 이벤트가 있다면, 간단하게 Webhook 컨트롤러를 확장하면 됩니다. 확장하는 메소드의 이름은 캐셔가 요구하는 규칙에 따라야 하고, `handle` 로 시작하며 Stripe 의 webhook 이름에 부합해야 합니다. 예를 들어, `invoice.payment_succeeded` webhook을 사용하고자 한다면 컨트롤러에는 `handleInvoicePaymentSucceeded` 메소드를 추가해야 합니다.

	class WebhookController extends Laravel\Cashier\WebhookController {

		public function handleInvoicePaymentSucceeded($payload)
		{
			// Handle The Event
		}

	}

> **Note:** In addition to updating the subscription information in your database, the Webhook controller will also cancel the subscription via the Stripe API.

> **주의** 추가적으로 데이터베이스에서 구독정보를 업데이트하면, Webhook 컨트롤러는 또한 Stripe API를 통해서 구독을 취소할 것입니다. 

<a name="invoices"></a>
## Invoices
## 청구서

You can easily retrieve an array of a user's invoices using the `invoices` method:
`invoices` 메소드를 사용하여 사용자의 청구서를 손쉽게 배열 형태로 조회 할 수 있습니다:

	$invoices = $user->invoices();

When listing the invoices for the customer, you may use these helper methods to display the relevant invoice information:

사용자의 청구서를 나열하는 경우, 청구서 관련 정보를 표시하기 위해서 헬퍼 함수를 사용할 수 있습니다. 

	{{ $invoice->id }}

	{{ $invoice->dateString() }}

	{{ $invoice->dollars() }}

Use the `downloadInvoice` method to generate a PDF download of the invoice. Yes, it's really this easy:

`downloadInvoice` 메소드를 사용하면 청구서를 PDF 로 생성하고 다운로드 할 수 있습니다. 참 쉽죠.

	return $user->downloadInvoice($invoice->id, [
		'vendor'  => 'Your Company',
		'product' => 'Your Product',
	]);
