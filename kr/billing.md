# Laravel Cashier
# 라라벨 캐셔

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Subscriptions](#subscriptions)
- [정기 구독 모델](#subscriptions)
    - [Creating Subscriptions](#creating-subscriptions)
    - [새로운 정기 구독 생성하기](#creating-subscriptions)
    - [Checking Subscription Status](#checking-subscription-status)
    - [정기 구독 상태 확인하기](#checking-subscription-status)
    - [Changing Plans](#changing-plans)
    - [정기 구독 유형 변경하기](#changing-plans)
    - [Subscription Quantity](#subscription-quantity)
    - [정기 구독 수량 변경하기](#subscription-quantity)
    - [Subscription Taxes](#subscription-taxes)
    - [정기 구독의 세금계산](#subscription-taxes)
    - [Cancelling Subscriptions](#cancelling-subscriptions)
    - [정기 구독 취소하기](#cancelling-subscriptions)
    - [Resuming Subscriptions](#resuming-subscriptions)
    - [정기 구독 재개](#resuming-subscriptions)
- [Handling Stripe Webhooks](#handling-stripe-webhooks)
- [Stripe 후킹 처리하기](#handling-stripe-webhooks)
    - [Failed Subscriptions](#handling-failed-subscriptions)
    - [실패한 정기 구독](#handling-failed-subscriptions)
    - [Other Webhooks](#handling-other-webhooks)
    - [기타 웹 훅](#handling-other-webhooks)
- [Single Charges](#single-charges)
- [한번만 결제하기](#single-charges)
- [Invoices](#invoices)
- [청구서](#invoices)
    - [Generating Invoice PDFs](#generating-invoice-pdfs)
    - [청구서 PDF 로 생성하기](#generating-invoice-pdfs)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel Cashier provides an expressive, fluent interface to [Stripe's](https://stripe.com) subscription billing services. It handles almost all of the boilerplate subscription billing code you are dreading writing. In addition to basic subscription management, Cashier can handle coupons, swapping subscription, subscription "quantities", cancellation grace periods, and even generate invoice PDFs.

라라벨 캐셔는 [Stripe's](https://stripe.com)에 의해서 제공되는 손쉽고 편리한 구독(정기 과금) 서비스를 위한 인터페이스를 제공합니다. 라라벨 캐셔는 여러분이 작성하는데 어려움을 겪는 구독을 위한 청구서에서 필요한 거의 모든 관용구문들을 다룹니다.  기본적인 구독 관리 외에도, 캐셔를 통해서 쿠폰 관리, 구독 변경, 구매 수량 변경, 취소 유예 기간, 그리고 청구서를 PDF로 생성할 수도 있습니다. 

<a name="configuration"></a>
### Configuration
### 설정하기

#### Composer
#### 컴포저

First, add the Cashier package to your `composer.json` file and run the `composer update` command:
먼저 여러분의 `composer.json` 파일에 캐셔 패키지를 추가하고 `composer update` 명령어를 실행 시켜야 합니다.

    "laravel/cashier": "~5.0" (For Stripe SDK ~2.0, and Stripe APIs on 2015-02-18 version and later)
    "laravel/cashier": "~4.0" (For Stripe APIs on 2015-02-18 version and later)
    "laravel/cashier": "~3.0" (For Stripe APIs up to and including 2015-02-16 version)

#### Service Provider
#### 서비스 프로바이더

Next, register the `Laravel\Cashier\CashierServiceProvider` [service provider](/docs/{{version}}/providers) in your `app` configuration file.

다음으로 `app` 설정 파일에 `Laravel\Cashier\CashierServiceProvider` [서비스 프로바이더](/docs/{{version}}/providers)를 등록해야 합니다. 

#### Migration
#### 마이그레이션

Before using Cashier, we'll need to add several columns to your database. Don't worry, you can use the `cashier:table` Artisan command to create a migration to add the necessary column. For example, to add the column to the users table run the command: `php artisan cashier:table users`.

캐셔를 바로 사용하기 전에 몇가지 컬럼을 데이터베이스에 추가해 주어야 합니다. 걱정하지 마십시오. `cachier:table` 아티즌 명령어를 통해서 필요한 컬럼을 추가하기 위한 마이그레이션을 손쉽게 생성할 수 있습니다. 예를 들어 user테이블에 컬럼을 추가하기 위해서는 `php artisan cashier:table users` 명령어를 실행 하면 됩니다. 

Once the migration has been created, simply run the `migrate` command.

마이그레이션이 생성되면 `migrate` 명령을 실행하십시오. 

#### Model Setup
#### 모델 설정

Next, add the `Billable` trait and appropriate date mutators to your model definition:

다음으로 `Billable` trait와 적당한  날짜 뮤테이터(mutators)을 모델에 추가합니다. 

    use Laravel\Cashier\Billable;
    use Laravel\Cashier\Contracts\Billable as BillableContract;

    class User extends Model implements BillableContract
    {
        use Billable;

        protected $dates = ['trial_ends_at', 'subscription_ends_at'];
    }

Adding the columns to your model's `$dates` property will instruct Eloquent to return the columns as Carbon / DateTime instances instead of raw strings.

모델의 `$dates` 속성에 컬럼을 추가하는 것은 Eloquent가 해당 컬럼을 문자열이 아닌 Carbon / DateTime 인스턴스 형태로 반환하도록 할 것입니다. 

#### Stripe Key
#### Stripe Key

Finally, set your Stripe key in your `services.php` configuration file:

마지막으로 `services.php` 설정 파일에 Stripe 키를 지정하십시오. 

    'stripe' => [
        'model'  => 'User',
        'secret' => env('STRIPE_API_SECRET'),
    ],

<a name="subscriptions"></a>
## Subscriptions
## 정기 구독 모델

<a name="creating-subscriptions"></a>
### Creating Subscriptions
### 새로운 정기 구독 생성하기

To create a subscription, first retrieve an instance of your billable model, which typically will be an instance of `App\User`. Once you have retrieved the model instance, you may use the `subscription` method to manage the model's subscription:

새로운 정기 구독을 생성하려면, 먼저 청구가 가능한 (일반적으로 `App\User`가 되는 ) 모델 인스턴스를 찾아야 합니다. 모델 인스턴스를 찾으면, 모델에 대한 정기 구독을 구성하기 위핸 `subscription` 메소드를 사용할 수 있습니다. 

    $user = User::find(1);

    $user->subscription('monthly')->create($creditCardToken);

The `create` method will automatically create the Stripe subscription, as well as update your database with Stripe customer ID and other relevant billing information. If your plan has a trial configured in Stripe, the trial end date will also automatically be set on the user record.

`create` 메소드는 자동으로 Stripe 정기 구독을 생성할 것이며, 여러분의 database 에 Stripe 고객 ID 와 관련된 결제정보를 업데이트 합니다. 만약 여러분의 구독 모델에 무료 평가 기간(trial)을 설정하는 경우 종료 날짜가 자동으로 사용자 레코드에 설정됩니다. 

If you want to implement trial periods, but are managing the trials entirely within your application instead of defining them within Stripe, you must manually set the trial end date:

만약 여러분이 무료 평가 기간(trial)을 Stripe 에서 정의하고 있는 것이 아니라, 애플리케이션에서 자체적으로 기간을 구현하려면 종료날짜를 직접 설정해야 합니다. 

    $user->trial_ends_at = Carbon::now()->addDays(14);

    $user->save();

#### Additional User Details
#### 사용자의 상세 정보 추가하기

If you would like to specify additional customer details, you may do so by passing them as the second argument to the `create` method:

만약 여러분이 추가적인 사용자 정보를 지정하고 싶다면 이러한 정보를 `create` 메소드의 두번째 인자로 전달하면 됩니다. 

    $user->subscription('monthly')->create($creditCardToken, [
        'email' => $email, 'description' => 'Our First Customer'
    ]);

To learn more about the additional fields supported by Stripe, check out Stripe's [documentation on customer creation](https://stripe.com/docs/api#create_customer).

추가적인 필드에 대한 Stripe 의 지원정보를 확인하고자 한다면 Stripe의 [고객 생성에 관한 문서](https://stripe.com/docs/api#create_customer)를 참고하십시오. 

#### Coupons
#### 쿠폰

If you would like to apply a coupon when creating the subscription, you may use the `withCoupon` method:

새로운 구독을 생성 할 때 쿠폰을 적용할 수 있게 하려면 `withCoupon` 메소드를 사용하면 됩니다. 

    $user->subscription('monthly')
         ->withCoupon('code')
         ->create($creditCardToken);

<a name="checking-subscription-status"></a>
### Checking Subscription Status
### 정기 구독 가입 상태 확인하기

Once a user is subscribed to your application, you may easily check their subscription status using a variety of convenient methods. First, the `subscribed` method returns `true` if the user has an active subscription, even if the subscription is currently within its trial period:

사용자가 정기 구독을 시작하고 난 뒤에, 이에 대한 정보를 확인하는 것은 메소드 하나로 손쉽게 확인이 가능합니다. 먼저 `subscribed` 메소드가 `true` 를 반환한다면 사용자의 정기구독 가입 상태는 무료 평가 기간을 포함하여, 활성화 되어 있다는 것을 의미합니다. 

    if ($user->subscribed()) {
        //
    }

The `subscribed` method also makes a great candidate for a [route middleware](/docs/{{version}}/middleware), allowing you to filter access to routes and controllers based on the user's subscription status:

`subscribed` 메소드는 라우트 미들웨어에 사용될수 있는 좋은 방법중 하나입니다: 사용자의 구독 상태에 따라서, 라우트 및 컨트롤러에 대한 액세스를 제한할 할 수 있습니다.

    public function handle($request, Closure $next)
    {
        if ($request->user() && ! $request->user()->subscribed()) {
            // This user is not a paying customer...
            return redirect('billing');
        }

        return $next($request);
    }

If you would like to determine if a user is still within their trial period, you may use the `onTrial` method. This method can be useful for displaying a warning to the user that they are still on their trial period:

사용자가 현재 무료 평가 기간(trial) 을 통해서 이용중인지 아닌지 확인하고자 한다면, `onTrial` 메소드를 사용하면 됩니다. 이 메소드는 사용자들에게 그들이 현재 무료 평가 기간을 이용중이라는 정보를 표시하는데 유용하게 사용될 수 있습니다. 

    if ($user->onTrial()) {
        //
    }

The `onPlan` method may be used to determine if the user is subscribed to a given plan based on its Stripe ID:

`onPlan` 메소드는 사용자가 지정된 Stripe ID에 대당하는 구독 플랜을 이용하는지 확인할 수 있습니다. 

    if ($user->onPlan('monthly')) {
        //
    }

#### Cancelled Subscription Status
#### 정기 구독 취소하기

To determine if the user was once an active subscriber, but has cancelled their subscription, you may use the `cancelled` method:

사용자가 이전에 한번 구독 후, 취소 했는지를 확인 하기 위해서 `cancelled` 메서드를 사용할 수 있습니다.

    if ($user->cancelled()) {
        //
    }

You may also determine if a user has cancelled their subscription, but are still on their "grace period" until the subscription fully expires. For example, if a user cancels a subscription on March 5th that was originally scheduled to expire on March 10th, the user is on their "grace period" until March 10th. Note that the `subscribed` method still returns `true` during this time.

또한 사용자가 구독을 취소하고 있지만 아직 완전히 만료 되기 전의 "유예 기간" 중인지를 확인할 수 있습니다. 예를 들어, 사용자가 3 월 5 일에 구독을 취소하고 3 월 10 일에 만료 될 경우, 해당 사용자는 3 월 10 일까지가 "유예 기간”입니다. `subscribed` 메소드는 이 기간 동안 여전히 `ture` 를 반환하는 것에 유의하십시오. 

    if ($user->onGracePeriod()) {
        //
    }

The `everSubscribed` method may be used to determine if the user has ever subscribed to a plan in your application:

`everSubscribed` 메소드는 사용자가 애플리케이션을 한번도 구독한적이 없는지 확인하는데 사용됩니다. 

    if ($user->everSubscribed()) {
        //
    }

<a name="changing-plans"></a>
### Changing Plans
### 정기 구독 유형 변경하기

After a user is subscribed to your application, they may occasionally want to change to a new subscription plan. To swap a user to a new subscription, use the `swap` method. For example, we may easily switch a user to the `premium` subscription:

    $user = App\User::find(1);

    $user->subscription('premium')->swap();

If the user is on trial, the trial period will be maintained. Also, if a "quantity" exists for the subscription, that quantity will also be maintained. When swapping plans, you may also use the `prorate` method to indicate that the charges should be pro-rated. In addition, you may use the `swapAndInvoice` method to immediately invoice the user for the plan change:

사용자가 평가기간(trial)중이라면, 평가 기간은 정상적으로 유지됩니다. 또한 구독의 "수량"이 존재하는 경우에도 이 수량은 유지됩니다. 구독 유형을 변경할 때에는 `prorate` 메소드를 사용하여, 요금에 비례 하여 변경된 유형 기간이 재분배 되도록 할 수 있습니다. 이에 더해 `swapAndInvoice` 메소드를 사용하여 사용자에게 즉시 변경된 구독 유형에 대한 청구서를 발행해 줄수도 있습니다. 

    $user->subscription('premium')
                ->prorate()
                ->swapAndInvoice();

<a name="subscription-quantity"></a>
### Subscription Quantity
### 정기 구독 수량 변경하기

Sometimes subscriptions are affected by "quantity". For example, your application might charge $10 per month **per user** on an account. To easily increment or decrement your subscription quantity, use the `increment` and `decrement` methods:

때로는 구독은 "수량"에 영향을 받을 수 있습니다. 예를 들어 여러분의 애플리케이션에서 하나의 계정의 **사용자마다** 한달에 10달러를 부과하고 있다고 한다면, `increment` 와 `decrement` 메소드를 사용하여 구독 수를 늘리거나 줄일 수 있습니다. 

    $user = User::find(1);

    $user->subscription()->increment();

    // Add five to the subscription's current quantity...
    $user->subscription()->increment(5);

    $user->subscription()->decrement();

    // Subtract five to the subscription's current quantity...
    $user->subscription()->decrement(5);

Alternatively, you may set a specific quantity using the `updateQuantity` method:

이 대신에, 특정 수량을 지정하고자 한다면, `updateQuantity` 메소드를 사용하면 됩니다.

    $user->subscription()->updateQuantity(10);

For more information on subscription quantities, consult the [Stripe documentation](https://stripe.com/docs/guides/subscriptions#setting-quantities).

구독 수량에 대한 보다 자세한 내용은 [Stripe 문서](https://stripe.com/docs/guides/subscriptions#setting-quantities)를 참고하십시오.

<a name="subscription-taxes"></a>
### Subscription Taxes
### 정기구독의 세금계산

With Cashier, it's easy to provide the `tax_percent` value sent to Stripe. To specify the tax percentage a user pays on a subscription, implement the `getTaxPercent` method on your billable model, and return a numeric value between 0 and 100, with no more than 2 decimal places.

캐셔에서는, Stripe에 손쉽게 "세율(tax_percent)'을 전달할 수 있습니다. 고객이 구매를 진행할 때 세금에 대한 퍼센트를 지정하려면 청구가 가능한 모델에 `getTaxPercent` 메소드를 사용하여 구현 소수점 자릿수가 2 자리 이내에서 0에서 100 사이의 숫자를 반환하면 됩니다.

    public function getTaxPercent() {
        return 20;
    }

This enables you to apply a tax rate on a model-by-model basis, which may be helpful for a user base that spans multiple countries.

이렇게 하면 모델별로 세율을 적용하여 다양한 국가와 해당 국가의 사용자를 기반으로 세율을 적용할 수 이 있습니다.

<a name="cancelling-subscriptions"></a>
### Cancelling Subscriptions
### 정기구독 취소하기

To cancel a subscription, simply call the `cancel` method on the user's subscription:

정기구독을 취소하기 위해서는, 간단하게 사용자의 정기구독에 대해서 `cancle` 메소드를 호출하면 됩니다:

    $user->subscription()->cancel();

When a subscription is cancelled, Cashier will automatically set the `subscription_ends_at` column on your database. This column is used to know when the `subscribed` method should begin returning `false`. For example, if a customer cancels a subscription on March 1st, but the subscription was not scheduled to end until March 5th, the `subscribed` method will continue to return `true` until March 5th.

구독이 취소되면 캐셔는 자동으로 데이터베이스의 `subscription_ends_at` 컬럼을 설정합니다. 이 컬럼은 언제 `subscribed` 메소드가 `false`를 반환해야 하는지 알기 위해서 사용되어 집니다. 예를 들어 사용자가 구독을 3월 1일에 취소했지만, 정기 구독이 3월 5일에 종료하도록 예정되어 있다면 `subscribed` 메소드는 3월 5일까지 `true`를 반환할 것입니다. 

You may determine if a user has cancelled their subscription but are still on their "grace period" using the `onGracePeriod` method:

사용자가 구독을 취소했지만 아직 "유예 기간"이 남아 있는지 확인하고자 한다면, `onGracePeriod` 메소드를 사용하면 됩니다.

    if ($user->onGracePeriod()) {
        //
    }

<a name="resuming-subscriptions"></a>
### Resuming Subscriptions
### 정기 구독 재개하기

If a user has cancelled their subscription and you wish to resume it, use the `resume` method:

만약 사용자가 취소한 구독을 재개하려면, `resume` 메서드를 사용하면 됩니다:

    $user->subscription('monthly')->resume($creditCardToken);

If the user cancels a subscription and then resumes that subscription before the subscription has fully expired, they will not be billed immediately. Instead, their subscription will simply be re-activated, and they will be billed on the original billing cycle.

만약 사용자가 구독을 취소하고 다음 정기 구독을 재개하는 경우 그 등록의 만료일이 되기 전까지는 비용이 바로 부과되지는 않습니다. 대신, 사용자의 정기 구독은 손쉽게 재활성화 되며, 원래의 주기에 따라 과금됩니다.

<a name="handling-stripe-webhooks"></a>
## Handling Stripe Webhooks
## Stripe 후킹 처리하기

<a name="handling-failed-subscriptions"></a>
### Failed Subscriptions
### 실패한 정기 구독

What if a customer's credit card expires? No worries - Cashier includes a Webhook controller that can easily cancel the customer's subscription for you. Just point a route to the controller:

만약 사용자의 신용카드가 만료 되었다면? 걱정하지 마십시오 - 캐셔는 사용자의 구독을 쉽게 취소할 수 있는 Webhook 컨트롤러를 포함하고 있습니다. 컨트롤러에 라우트를 지정하면 됩니다.

    Route::post('stripe/webhook', '\Laravel\Cashier\WebhookController@handleWebhook');

That's it! Failed payments will be captured and handled by the controller. The controller will cancel the customer's subscription when Stripe determines the subscription has failed (normally after three failed payment attempts). Don't forget: you will need to configure the webhook URI in your Stripe control panel settings.

끝입니다! 실패한 결제는 컨트롤러에 의해 확인되어 처리됩니다. 컨트롤러는 Stripe 가 결제에 실패하였다고 결정되면(보통 3번의 결제 시도가 실패하면) 사용자의 구독을 취소시킬 것입니다. 잊지 마십시오: 여러분은 Stripe 설정 패널에서 웹 후킹 URI를 설정해야합니다.

Since Stripe webhooks need to bypass Laravel's [CSRF verification](/docs/{{version}}/routing#csrf-protection), be sure to list the URI as an exception in your `VerifyCsrfToken` middleware:

Stripe의 웹 후킹은 라라벨의 [CSRF 확인](/docs/{{version}}/routing#csrf-protection)을 회피 할 필요가 있기 때문에 `VerifyCsrfToken` 미들웨어에서 제외될  URI 목록에 추가해 주어야 합니다.

    protected $except = [
        'stripe/*',
    ];

<a name="handling-other-webhooks"></a>
### Other Webhooks
### 기타 후킹

If you have additional Stripe webhook events you would like to handle, simply extend the Webhook controller. Your method names should correspond to Cashier's expected convention, specifically, methods should be prefixed with `handle` and the "camel case" name of the Stripe webhook you wish to handle. For example, if you wish to handle the `invoice.payment_succeeded` webhook, you should add a `handleInvoicePaymentSucceeded` method to the controller.

만약 추가적인 Stripe 웹 후크 이벤트가 있다면, 간단하게 Webhook 컨트롤러를 확장하면 됩니다. 확장하는 메소드의 이름은 캐셔가 요구하는 규칙에 따라야 하고, `handle` 로 시작하며 "카멜 케이스"의 Stripe 의 webhook 이름에 부합해야 합니다. 예를 들어, `invoice.payment_succeeded` webhook을 사용하고자 한다면 컨트롤러에는 `handleInvoicePaymentSucceeded` 메소드를 추가해야 합니다.

    <?php

    namespace App\Http\Controllers;

    use Laravel\Cashier\WebhookController as BaseController;

    class WebhookController extends BaseController
    {
        /**
         * Handle a stripe webhook.
         *
         * @param  array  $payload
         * @return Response
         */
        public function handleInvoicePaymentSucceeded($payload)
        {
            // Handle The Event
        }
    }

<a name="single-charges"></a>
## Single Charges
## 한번만 결제하기

If you would like to make a "one off" charge against a subscribed customer's credit card, you may use the `charge` method on a billable model instance. The `charge` method accepts the amount you would like to charge in the **lowest denominator of the currency used by your application**. So, for example, the example above will charge 100 cents, or $1.00, against the user's credit card.

정기 구독하고 있는 고객의 신용 카드에 대해 "일회성 청구"를 하고 싶을 때에는 청구가 가능한 모델 인스턴스에 대해서 `charge` 메서드를 사용합니다. `charge` 메소드는 청구하려는 **애플리케이션에서 사용하는 통화의 가장 낮은 기준 금액**을 인수로 받습니다. 따라서 위의 예제는 100 센트 또는 1 달러를 신용 카드로 결제합니다.

    $user->charge(100);

The `charge` method accepts an array as its second argument, allowing you to pass any options you wish to the underlying Stripe charge creation:

`charge` 메소드는 두번째 인자로 Stripe 청구에 사용할 수 있는 옵션에 대한 배열을 전달 받습니다. 

    $user->charge(100, [
        'source' => $token,
        'receipt_email' => $user->email,
    ]);

The `charge` method will return `false` if the charge fails. This typically indicates the charge was denied:

`charge` 메소드는 청구가 실패했을 경우에 `false` 를 반환합니다. 일반적으로 이경우는 결제가 거부 되었다는 것을 의미합니다. 

    if ( ! $user->charge(100)) {
        // The charge was denied...
    }

If the charge is successful, the full Stripe response will be returned from the method.

청구가 성공적으로 완료되었다면 메소드에서는 Stripe 응답 객체가 반환됩니다. 

<a name="invoices"></a>
## Invoices
## 청구서

You may easily retrieve an array of a billable model's invoices using the `invoices` method:

`invoices` 메소드를 사용하여 청구가 가능한 모델의 청구서를 손쉽게 배열 형태로 조회 할 수 있습니다:

    $invoices = $user->invoices();

When listing the invoices for the customer, you may use the invoice's helper methods to display the relevant invoice information. 
For example, you may wish to list every invoice in a table, allowing the user to easily download any of them:

사용자의 청구서를 나열하는 경우, 청구서 관련 정보를 표시하기 위해서 청구서의 헬퍼 함수를 사용할 수 있습니다. 예를 들어 사용자가 쉽게 다운로드 할 수 있도록 테이블 내의 모든 청구서를 목록으로 보여주고자 할 수도 있습니다.

    <table>
        @foreach ($invoices as $invoice)
            <tr>
                <td>{{ $invoice->dateString() }}</td>
                <td>{{ $invoice->dollars() }}</td>
                <td><a href="/user/invoice/{{ $invoice->id }}">Download</a></td>
            </tr>
        @endforeach
    </table>

<a name="generating-invoice-pdfs"></a>
#### Generating Invoice PDFs
#### 청구서 PDF 로 생성하기

From within a route or controller, use the `downloadInvoice` method to generate a PDF download of the invoice. This method will automatically generate the proper HTTP response to send the download to the browser:

라우트 또는 컨트롤러 안에서 `downloadInvoice` 메소드를 사용하여 청구서를 PDF 로 생성하고 다운로드 할 수 있습니다. 이 메소드는 자동으로 브라우저서 다운로드 할 수 있는 HTTP 응답을 생성할 것입니다. 

    Route::get('user/invoice/{invoice}', function ($invoiceId) {
        return Auth::user()->downloadInvoice($invoiceId, [
            'vendor'  => 'Your Company',
            'product' => 'Your Product',
        ]);
    });
