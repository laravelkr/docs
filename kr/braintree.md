# Laravel Cashier (Braintree)

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Caveats](#caveats)
    - [주의사항](#caveats)
- [Installation](#installation)
- [설치하기](#installation)
- [Configuration](#configuration)
- [설정하기](#configuration)
    - [Plan Credit Coupon](#plan-credit-coupon)
    - [Plan Credit 쿠폰](#plan-credit-coupon)
    - [Database Migrations](#database-migrations)
    - [데이터베이스 마이그레이션](#database-migrations)
    - [Billable Model](#billable-model)
    - [Billable 모델](#billable-model)
    - [API Keys](#api-keys)
    - [API Keys](#api-keys)
    - [Currency Configuration](#currency-configuration)
    - [화폐 설정하기](#currency-configuration)
- [Subscriptions](#subscriptions)
- [정기 구독 모델](#subscriptions)
    - [Creating Subscriptions](#creating-subscriptions)
    - [새로운 정기 구독 생성하기](#creating-subscriptions)
    - [Checking Subscription Status](#checking-subscription-status)
    - [정기 구독 가입 상태 확인하기](#checking-subscription-status)
    - [Changing Plans](#changing-plans)
    - [정기 구독 유형 변경하기](#changing-plans)
    - [Subscription Taxes](#subscription-taxes)
    - [정기구독의 세금계산](#subscription-taxes)
    - [Cancelling Subscriptions](#cancelling-subscriptions)
    - [정기구독 취소하기](#cancelling-subscriptions)
    - [Resuming Subscriptions](#resuming-subscriptions)
    - [정기 구독 재개하기](#resuming-subscriptions)
- [Subscription Trials](#subscription-trials)
- [구독 트라이얼](#subscription-trials)
    - [With Credit Card Up Front](#with-credit-card-up-front)
    - [신용카드 사전 등록](#with-credit-card-up-front)
    - [Without Credit Card Up Front](#without-credit-card-up-front)
    - [신용카드없이 사전 등록](#without-credit-card-up-front)
- [Customers](#customers)
- [고객](#customers)
    - [Creating Customers](#creating-customers)
    - [고객 생성하기](#creating-customers)
- [Cards](#cards)
- [신용카드](#cards)
    - [Updating Credit Cards](#updating-credit-cards)
    - [신용카드 정보 업데이트 하기](#updating-credit-cards)
- [Handling Webhooks](#handling-webhooks)
- [Webhooks 처리하기](#handling-webhooks)
    - [Defining Webhook Event Handlers](#defining-webhook-event-handlers)
    - [Webhook 이벤트 처리기 정의하기](#defining-webhook-event-handlers)
    - [Failed Subscriptions](#handling-failed-subscriptions)
    - [실패한 구독처리](#handling-failed-subscriptions)
- [Single Charges](#single-charges)
- [한번만 결제하기](#single-charges)
    - [Simple Charge](#simple-charge)
    - [간단한 결제](#simple-charge)
    - [Charge With Invoice](#charge-with-invoice)
    - [결제와 청구서](#charge-with-invoice)
- [Invoices](#invoices)
- [청구서](#invoices)
    - [Generating Invoice PDFs](#generating-invoice-pdfs)
    - [청구서 PDF 생성하기](#generating-invoice-pdfs)

<a name="introduction"></a>
## Introduction
## 소개하기

Laravel Cashier Braintree provides an expressive, fluent interface to [Braintree's](https://www.braintreepayments.com) subscription billing services. It handles almost all of the boilerplate subscription billing code you are dreading writing. In addition to basic subscription management, Cashier can handle coupons, swapping subscription, subscription "quantities", cancellation grace periods, and even generate invoice PDFs.

라라벨 캐셔 Braintree는 [Braintree](https://www.braintreepayments.com)에서 제공하는 손쉽고 편리한 구독(정기 과금) 서비스를 위한 인터페이스를 제공합니다. 라라벨 캐셔는 여러분이 작성하는데 어려움을 겪는 구독을 위한 청구서에서 필요한 거의 모든 관용구문들을 다룹니다. 기본적인 구독 관리 외에도, 캐셔를 통해서 쿠폰 관리, 구독 변경, 구매 수량 변경, 취소 유예 기간, 그리고 청구서를 PDF로 생성할 수도 있습니다.


> {note} These are the docs for the Cashier integration with Braintree. You may find the Stripe integration docs [here](/docs/{{version}}/billing).

> {note} 이것은 Braintree와 캐셔의 통합을 위한 문서입니다. Stripe와 통합 문서는 [여기](/docs/{{version}}/billing)에서 찾을 수 있습니다.

> {note} If you're only performing "one-off" charges and do not offer subscriptions, you should not use Cashier. Instead, use the Braintree SDK directly.

> {note} 만약 여러분이 "한번의 결제"만을 사용하고 정기 과금형태의 구독모델을 사용하지 않는다면, 캐셔를 사용할 필요가 없습니다. 대신에 Braintree SDK를 직접 사용하면 됩니다.


<a name="caveats"></a>
### Caveats
### 주의사항

For many operations, the Stripe and Braintree implementations of Cashier function the same. Both services provide subscription billing with credit cards but Braintree also supports payments via PayPal. However, Braintree also lacks some features that are supported by Stripe. You should keep the following in mind when deciding to use Stripe or Braintree:

대부분의 동작들에서, Stripe 와 Braintree 의 캐셔 함수의 구현부분은 동일합니다. 두개의 서비스 모두 신용카드에 대한 구독 청구를 지원합니다. 다른 점은 Braintree 는 페이팔과 같은 결제도 지원한다는 것입니다. 하지만 Braintree 는 Stripe 에서 지원하는 몇가지 기능들은 누락되어 있습니다. 여러분은 Stripe 과 Braintree 둘중에 어느 서비스를 사용할지 결정해야 합니다:

- Braintree supports PayPal while Stripe does not.
- Braintree does not support the `increment` and `decrement` methods on subscriptions. This is a Braintree limitation, not a Cashier limitation.
- Braintree does not support percentage based discounts. This is a Braintree limitation, not a Cashier limitation.

- Braintree는 PayPal을 지원하지만 Stripe는 미지원.
- Braintree는 구독에 대해 `increment` 와`decrement` 메소드 지원하지 않습니다. 이것은 Braintree 의 제한 사항으로, Cashier 에서 제한하는 것은 아닙니다.
- Braintree는 퍼센트 기반 할인을 지원하지 않습니다. 이것은 Braintree 의 제한 사항으로, Cashier 에서 제한하는 것은 아닙니다.

<a name="installation"></a>
## Installation
## 설치하기

First, require the Cashier package for Braintree with Composer:

먼저 컴포저와 함께 Braintree를 위한 캐셔 패키지를 포함하십시오:

    composer require laravel/cashier-braintree

<a name="configuration"></a>
## Configuration
## 설정하기

<a name="plan-credit-coupon"></a>
### Plan Credit Coupon
### Plan Credit 쿠폰

Before using Cashier with Braintree, you will need to define a `plan-credit` discount in your Braintree control panel. This discount will be used to properly prorate subscriptions that change from yearly to monthly billing, or from monthly to yearly billing.

Braintree를 통해서 캐셔를 사용하기 전에 Braintree 설정 패널에서 `plan-credit` 할인을 정의할 필요가 있습니다. 이 할인은 월별 결제에서 년단위 결제로 변경하거나, 반대로 년단위 결제에서 월별 결제로 변경할 때 정확하게 비율을 배분하는데 사용될 것입니다.

The discount amount configured in the Braintree control panel can be any value you wish, as Cashier will override the defined amount with our own custom amount each time we apply the coupon. This coupon is needed since Braintree does not natively support prorating subscriptions across subscription frequencies.

이 할인율은 Braintree 설정 패널에서 여러분이 원하는 값으로 설정할 수 있으며 고객이 쿠폰을 적용한 경우 캐셔는 정의된 값을 대체합니다. Braintree는 가입 기간에 비례한 비율을 지원하지 않기 때문에 이 쿠폰이 필요합니다.

<a name="database-migrations"></a>
### Database Migrations
#### 데이터베이스 마이그레이션

Before using Cashier, we'll need to [prepare the database](/docs/{{version}}/migrations). We need to add several columns to your `users` table and create a new `subscriptions` table to hold all of our customer's subscriptions:

캐셔를 바로 사용하기 전에, [데이터베이스를 준비](/docs/{{version}}/migrations)해야 합니다. 몇개의 컬럼을 `users` 테이블에 추가하고, 사용자의 구독 정보를 저장할 새로운 `subscriptions` 테이블을 생성해야 합니다:

    Schema::table('users', function (Blueprint $table) {
        $table->string('braintree_id')->nullable();
        $table->string('paypal_email')->nullable();
        $table->string('card_brand')->nullable();
        $table->string('card_last_four')->nullable();
        $table->timestamp('trial_ends_at')->nullable();
    });

    Schema::create('subscriptions', function (Blueprint $table) {
        $table->increments('id');
        $table->unsignedInteger('user_id');
        $table->string('name');
        $table->string('braintree_id');
        $table->string('braintree_plan');
        $table->integer('quantity');
        $table->timestamp('trial_ends_at')->nullable();
        $table->timestamp('ends_at')->nullable();
        $table->timestamps();
    });

Once the migrations have been created, run the `migrate` Artisan command.

마이그레이션들이 생성되면 `migrate` 아티즌 명령어를 실행하십시오.

<a name="billable-model"></a>
### Billable Model
#### Billable 모델

Next, add the `Billable` trait to your model definition:

다음으로 `Billable` 트레이트-trait을 모델에 추가합니다:

    use Laravel\Cashier\Billable;

    class User extends Authenticatable
    {
        use Billable;
    }

<a name="api-keys"></a>
### API Keys
#### API Key

Next, you should configure the following options in your `services.php` file:

다음으로 `services.php`에 다음의 옵션값들을 설정해야 합니다:

    'braintree' => [
        'model'  => App\User::class,
        'environment' => env('BRAINTREE_ENV'),
        'merchant_id' => env('BRAINTREE_MERCHANT_ID'),
        'public_key' => env('BRAINTREE_PUBLIC_KEY'),
        'private_key' => env('BRAINTREE_PRIVATE_KEY'),
    ],

Then you should add the following Braintree SDK calls to your `AppServiceProvider` service provider's `boot` method:

그리고 다음의 Braintree SDK 호출을 `AppServiceProvider` 서비스 프로바이더의 `boot` 메소드에 추가해야 합니다:

    \Braintree_Configuration::environment(config('services.braintree.environment'));
    \Braintree_Configuration::merchantId(config('services.braintree.merchant_id'));
    \Braintree_Configuration::publicKey(config('services.braintree.public_key'));
    \Braintree_Configuration::privateKey(config('services.braintree.private_key'));

<a name="currency-configuration"></a>
### Currency Configuration
### 화폐 설정하기

The default Cashier currency is United States Dollars (USD). You can change the default currency by calling the `Cashier::useCurrency` method from within the `boot` method of one of your service providers. The `useCurrency` method accepts two string parameters: the currency and the currency's symbol:

캐셔의 기본 화폐는 미국달러(USD)입니다. 여러분의 서비스 프로바이더 중 한곳의 `boot` 메소드 안에서 `Cashier::useCurrency` 메소드를 호출하면, 기본화폐를 변경할 수 있습니다. `useCurrency` 메소드는 두개의 문자형 인자를 받습니다: 화폐의 이름과 화폐의 기호입니다:

    use Laravel\Cashier\Cashier;

    Cashier::useCurrency('eur', '€');

<a name="subscriptions"></a>
## Subscriptions
## 정기 구독 모델

<a name="creating-subscriptions"></a>
### Creating Subscriptions
### 새로운 정기 구독 생성하기

To create a subscription, first retrieve an instance of your billable model, which typically will be an instance of `App\User`. Once you have retrieved the model instance, you may use the `newSubscription` method to create the model's subscription:

새로운 정기 구독을 생성하려면, 먼저 청구가 가능한 (일반적으로 `App\User`가 되는 ) 모델 인스턴스를 찾아야 합니다. 모델 인스턴스를 찾으면, 모델에 대한 정기 구독을 생성하기 위해 `newSubscription` 메소드를 사용할 수 있습니다:

    $user = User::find(1);

    $user->newSubscription('main', 'premium')->create($token);

The first argument passed to the `newSubscription` method should be the name of the subscription. If your application only offers a single subscription, you might call this `main` or `primary`. The second argument is the specific plan the user is subscribing to. This value should correspond to the plan's identifier in Braintree.

`newSubscription` 메소드에 전달되는 첫번째 인자는 구독의 제목이 되어야 합니다. 애플리케이션이 단 하나의 구독모델을 제공한다면, `main` 또는 `primary` 와 같이 사용할 수 있습니다. 두번째 인자는 사용자가 구독하고자 하는 지정된 plan입니다. 이 값은 Braintree의 plan 식별자와 일치해야 합니다.

The `create` method, which accepts a credit card / source token, will begin the subscription as well as update your database with the customer ID and other relevant billing information.

신용카드 / 소스 토큰을 전달받아 `create` 메소드는 정기 구독을 시작하며, 데이터베이스에 고객의 ID와 관련된 결제정보를 업데이트 합니다.

#### Additional User Details
#### 추가적인 사용자의 상세 정보

If you would like to specify additional customer details, you may do so by passing them as the second argument to the `create` method:

만약 여러분이 추가적인 사용자 정보를 지정하고 싶다면 이러한 정보를 `create` 메소드의 두번째 인자로 전달하면 됩니다:

    $user->newSubscription('main', 'monthly')->create($token, [
        'email' => $email,
    ]);

To learn more about the additional fields supported by Braintree, check out the corresponding [Braintree documentation](https://developers.braintreepayments.com/reference/request/customer/create/php).

추가적인 필드에 대한 Braintree의 지원정보를 확인하고자 한다면 [Braintree 매뉴얼](https://developers.braintreepayments.com/reference/request/customer/create/php)을 참고하십시오.

#### Coupons
#### 쿠폰

If you would like to apply a coupon when creating the subscription, you may use the `withCoupon` method:

새로운 구독을 생성 할 때 쿠폰을 적용할 수 있게 하려면 `withCoupon` 메소드를 사용하면 됩니다:

    $user->newSubscription('main', 'monthly')
         ->withCoupon('code')
         ->create($token);

<a name="checking-subscription-status"></a>
### Checking Subscription Status
### 정기 구독 가입 상태 확인하기

Once a user is subscribed to your application, you may easily check their subscription status using a variety of convenient methods. First, the `subscribed` method returns `true` if the user has an active subscription, even if the subscription is currently within its trial period:

사용자가 정기 구독을 시작하고 난 뒤에, 이에 대한 정보를 확인하는 것은 메소드 하나로 손쉽게 확인이 가능합니다. 먼저 `subscribed` 메소드가 `true` 를 반환한다면 사용자의 정기구독 가입 상태는 무료 평가 기간을 포함하여, 활성화 되어 있다는 것을 의미합니다:

    if ($user->subscribed('main')) {
        //
    }

The `subscribed` method also makes a great candidate for a [route middleware](/docs/{{version}}/middleware), allowing you to filter access to routes and controllers based on the user's subscription status:

`subscribed` 메소드는 [라우트 미들웨어](/docs/{{version}}/middleware)에 사용될수 있는 좋은 방법중 하나입니다: 사용자의 구독 상태에 따라서, 라우트 및 컨트롤러에 대한 액세스를 제한할 할 수 있습니다:

    public function handle($request, Closure $next)
    {
        if ($request->user() && ! $request->user()->subscribed('main')) {
            // This user is not a paying customer...
            return redirect('billing');
        }

        return $next($request);
    }

If you would like to determine if a user is still within their trial period, you may use the `onTrial` method. This method can be useful for displaying a warning to the user that they are still on their trial period:

사용자가 현재 무료 평가 기간(trial) 을 통해서 이용중인지 아닌지 확인하고자 한다면, `onTrial` 메소드를 사용하면 됩니다. 이 메소드는 사용자들에게 그들이 현재 무료 평가 기간을 이용중이라는 정보를 표시하는데 유용하게 사용될 수 있습니다:

    if ($user->subscription('main')->onTrial()) {
        //
    }

The `subscribedToPlan` method may be used to determine if the user is subscribed to a given plan based on a given plan ID. In this example, we will determine if the user's `main` subscription is actively subscribed to the `monthly` plan:

`subscribedToPlan` 메소드는 사용자가 주어진 plan ID에 대당하는 구독 플랜을 이용하는지 확인할 수 있습니다. 다음 예제는 사용자가 `main` 구독을 `monthly` plan 으로 구독하고 있는지 확인하게 됩니다:

    if ($user->subscribedToPlan('monthly', 'main')) {
        //
    }

The `recurring` method may be used to determine if the user is currently subscribed and is no longer within their trail period:

`recurring` 메소드는 사용자가 현재 구독 중인지 아니면 더이상 trail 기간에 속해 있지 않은지를 확인할 때 사용될 수 있습니다. 

    if ($user->subscription('main')->recurring()) {
        //
    }

#### Cancelled Subscription Status
#### 정기 구독 취소하기

To determine if the user was once an active subscriber, but has cancelled their subscription, you may use the `cancelled` method:

사용자가 이전에 한번 구독 후, 취소 했는지를 확인 하기 위해서 `cancelled` 메소드를 사용할 수 있습니다:

    if ($user->subscription('main')->cancelled()) {
        //
    }

You may also determine if a user has cancelled their subscription, but are still on their "grace period" until the subscription fully expires. For example, if a user cancels a subscription on March 5th that was originally scheduled to expire on March 10th, the user is on their "grace period" until March 10th. Note that the `subscribed` method still returns `true` during this time:

또한 사용자가 구독을 취소하고 있지만 아직 완전히 만료 되기 전의 "유예 기간" 중인지를 확인할 수 있습니다. 예를 들어, 사용자가 3 월 5 일에 구독을 취소하고 3 월 10 일에 만료 될 경우, 해당 사용자는 3 월 10 일까지가 "유예 기간”입니다. `subscribed` 메소드는 이 기간 동안 여전히 `true` 를 반환하는 것에 유의하십시오:

    if ($user->subscription('main')->onGracePeriod()) {
        //
    }

To determine if the user has cancelled their subscription is no longer within their "grace period", you may use the `ended` method:

사용자가 더 이상 "유예 기간"내에 있지 않고 자신의 가입을 취소되었는지 판단하기 위해 `ended` 메소드을 사용할 수 있습니다.

    if ($user->subscription('main')->ended()) {
        //
    }

<a name="changing-plans"></a>
### Changing Plans
### 정기 구독 유형 변경하기

After a user is subscribed to your application, they may occasionally want to change to a new subscription plan. To swap a user to a new subscription, pass the plan's identifier to the `swap` method:

사용자가 애플리케이션을 구독한 뒤에, 구독 플랜을 변경하고자 하는 경우는 자주 있습니다. 사용자를 새로운 구독 플랜으로 변경하게 하려면 `swap` 메소드에 플랜의 id를 전달하면 됩니다:


    $user = App\User::find(1);

    $user->subscription('main')->swap('provider-plan-id');

If the user is on trial, the trial period will be maintained. Also, if a "quantity" exists for the subscription, that quantity will also be maintained.

사용자가 평가기간(trial)중이라면, 평가 기간은 정상적으로 유지됩니다. 또한 구독의 "수량"이 존재하는 경우에도 이 수량은 유지됩니다.

If you would like to swap plans and cancel any trial period the user is currently on, you may use the `skipTrial` method:

구독 유형을 변경하고 사영자의 현재 구독 트라이얼 기간을 취소하려면 `skipTrial` 메소드를 하용하면 됩니다:

    $user->subscription('main')
            ->skipTrial()
            ->swap('provider-plan-id');

<a name="subscription-taxes"></a>
### Subscription Taxes
### 정기구독의 세금계산

To specify the tax percentage a user pays on a subscription, implement the `taxPercentage` method on your billable model, and return a numeric value between 0 and 100, with no more than 2 decimal places.

고객이 구매를 진행할 때 세금에 대한 퍼센트를 지정하려면 청구가 가능한 모델에 `taxPercentage` 메소드를 사용하여 구현 소수점 자릿수가 2 자리 이내에서 0에서 100 사이의 숫자를 반환하면 됩니다.

    public function taxPercentage()
    {
        return 20;
    }

The `taxPercentage` method enables you to apply a tax rate on a model-by-model basis, which may be helpful for a user base that spans multiple countries and tax rates.

`taxPercentage` 메소드는 모델별로 세율을 적용하여 다양한 국가와 세율을 해당 국가의 사용자를 기반으로 적용할 수 이 있습니다.

> {note} The `taxPercentage` method only applies to subscription charges. If you use Cashier to make "one off" charges, you will need to manually specify the tax rate at that time.

> {note} `taxPercentage` 메소드는 정기구독의 결제 시에만 적용됩니다. "한번 결제"에서 캐셔를 사용하는 경우 세율을 직접 적용해야합니다.

<a name="cancelling-subscriptions"></a>
### Cancelling Subscriptions
### 정기구독 취소하기

To cancel a subscription, call the `cancel` method on the user's subscription:

정기구독을 취소하기 위해서는, 사용자의 정기구독에 대해서 `cancle` 메소드를 호출하면 됩니다:

    $user->subscription('main')->cancel();

When a subscription is cancelled, Cashier will automatically set the `ends_at` column in your database. This column is used to know when the `subscribed` method should begin returning `false`. For example, if a customer cancels a subscription on March 1st, but the subscription was not scheduled to end until March 5th, the `subscribed` method will continue to return `true` until March 5th.

구독이 취소되면 캐셔는 자동으로 데이터베이스의 `ends_at` 컬럼을 설정합니다. 이 컬럼은 언제 `subscribed` 메소드가 `false`를 반환해야 하는지 알기 위해서 사용되어 집니다. 예를 들어 사용자가 구독을 3월 1일에 취소했지만, 정기 구독이 3월 5일에 종료하도록 예정되어 있다면 `subscribed` 메소드는 3월 5일까지 `true`를 반환할 것입니다.

You may determine if a user has cancelled their subscription but are still on their "grace period" using the `onGracePeriod` method:

사용자가 구독을 취소했지만 아직 "유예 기간"이 남아 있는지 확인하고자 한다면, `onGracePeriod` 메소드를 사용하면 됩니다:

    if ($user->subscription('main')->onGracePeriod()) {
        //
    }

If you wish to cancel a subscription immediately, call the `cancelNow` method on the user's subscription:

정기구독을 즉시 취소하고자 한다면, 사용자의 정기구독 에서 `cancelNow` 메소드를 호출하면 됩니다:

    $user->subscription('main')->cancelNow();

<a name="resuming-subscriptions"></a>
### Resuming Subscriptions
### 정기 구독 재개하기

If a user has cancelled their subscription and you wish to resume it, use the `resume` method. The user **must** still be on their grace period in order to resume a subscription:

만약 사용자가 취소한 구독을 재개하려면, `resume` 메소드를 사용하면 됩니다. 사용자는 구독을 재개하기 위해 **반드시** 자신의 유예 기간에 내에 있어야 합니다:

    $user->subscription('main')->resume();

If the user cancels a subscription and then resumes that subscription before the subscription has fully expired, they will not be billed immediately. Instead, their subscription will be re-activated, and they will be billed on the original billing cycle.

만약 사용자가 구독을 취소하고 다음 정기 구독을 재개하는 경우 그 등록의 만료일이 되기 전까지는 비용이 바로 부과되지는 않습니다. 대신, 사용자의 정기 구독은 재활성화 되며, 원래의 주기에 따라 과금됩니다.

<a name="subscription-trials"></a>
## Subscription Trials
## 구독 트라이얼

<a name="with-credit-card-up-front"></a>
### With Credit Card Up Front
### 신용카드 사전 등록

If you would like to offer trial periods to your customers while still collecting payment method information up front, you should use the `trialDays` method when creating your subscriptions:

고객에게 트라이얼 기간을 제공하고, 결제 정보를 사전에 등록 해달라고 요청하고자 한다면, 구독을 생성할 때 `trialDays` 메소드를 해야합니다:

    $user = User::find(1);

    $user->newSubscription('main', 'monthly')
                ->trialDays(10)
                ->create($token);

This method will set the trial period ending date on the subscription record within the database, as well as instruct Braintree to not begin billing the customer until after this date.

이 메소드는 데이터베이스 안의 구독 레코드에 트라이얼 기간의 종료일을 설정하고, Braintree에게 이 기간이 지나기 전까지 고객에게 청구하지 않도록 지시합니다.

> {note} If the customer's subscription is not cancelled before the trial ending date they will be charged as soon as the trial expires, so you should be sure to notify your users of their trial ending date.

> {note} 트라이얼 기간 종료 이전에 고객이 구독을 취소하지 않으면 트라이얼 기간이 만료되는 즉시 요금이 부과되므로, 트라이얼의 종료일을 사용자에게 공지해야합니다.

You may determine if the user is within their trial period using either the `onTrial` method of the user instance, or the `onTrial` method of the subscription instance. The two examples below are identical:

사용자가 현재 트라이얼 기간 안에 있는지 확인하려면, 사용자 인스턴스에서 `onTrial` 메소드를 사용하거나, 구독 인스턴스에서 `onTrial` 메소드를 사용하면 됩니다. 다음의 두 예제는 동일합니다:

    if ($user->onTrial('main')) {
        //
    }

    if ($user->subscription('main')->onTrial()) {
        //
    }

<a name="without-credit-card-up-front"></a>
### Without Credit Card Up Front
### 신용카드없이 사전 등록

If you would like to offer trial periods without collecting the user's payment method information up front, you may set the `trial_ends_at` column on the user record to your desired trial ending date. This is typically done during user registration:

고객에게 신용카드에 대한 결제 정보의 사전등록 없이 트라이얼 기간을 부여하고자 한다면, 사용자의 `trial_ends_at` 컬럼에 트리이얼 종료기간을 설정하면 됩니다. 이는 일반적으로 사용자를 등록할 때 설정하게 됩니다:

    $user = User::create([
        // Populate other user properties...
        'trial_ends_at' => now()->addDays(10),
    ]);

> {note}  Be sure to add a [date mutator](/docs/{{version}}/eloquent-mutators#date-mutators) for `trial_ends_at` to your model definition.

> {note} 모델 정의에 `trial_ends_at`을 [날짜 mutator](/docs/{{version}}/eloquent-mutators#date-mutators)로 추가되어 있어야 합니다.

Cashier refers to this type of trial as a "generic trial", since it is not attached to any existing subscription. The `onTrial` method on the `User` instance will return `true` if the current date is not past the value of `trial_ends_at`:

기존 구독에 연결되는 것이 아니기 때문에, Cashier에서는 이 타입의 트라이얼을 "포괄적 시험기간(generic trial)"이라고 지칭하고 있습니다. `User` 인스턴스의 `onTrial` 메소드는 만약 현재 시간이 아직 `trial_ends_at`을 넘기지 않은 경우에 `true`를 반환합니다:

    if ($user->onTrial()) {
        // User is within their trial period...
    }

You may also use the `onGenericTrial` method if you wish to know specifically that the user is within their "generic" trial period and has not created an actual subscription yet:

또한 특별히 사용자가 현재 "포괄적" 시험기간 중이며 아직 실제 구독을 생성하지 않았는지 알고자 한다면 `onGenericTrial` 메소드를 사용할 수 있습니다:

    if ($user->onGenericTrial()) {
        // User is within their "generic" trial period...
    }

Once you are ready to create an actual subscription for the user, you may use the `newSubscription` method as usual:

사용자에게 실제 구독을 생성할 준비가 되면, 일반적으로 `newSubscription` 메소드를 사용할 수 있습니다:

    $user = User::find(1);

    $user->newSubscription('main', 'monthly')->create($token);

<a name="customers"></a>
## Customers
## 고객

<a name="creating-customers"></a>
### Creating Customers
### 고객 생성하기

Occasionally, you may wish to create a Braintree customer without beginning a subscription. You may accomplish this using the `createAsBraintreeCustomer` method:

경우에 따라 구독을 시작하지 않고 Stripe 고객을 만들 수도 있습니다. 이 경우 `createAsBraintreeCustomer` 메소드를 사용하면 됩니다 :

    $user->createAsBraintreeCustomer();

Once the customer has been created in Braintree, you may begin a subscription at a later date.

Braintree에서 고객을 생성 한 후 나중에 구독을 시작 할 수도 있습니다.

<a name="cards"></a>
## Cards
## 신용카드

<a name="updating-credit-cards"></a>
### Updating Credit Cards
### 신용카드 정보 업데이트 하기

The `updateCard` method may be used to update a customer's credit card information. This method accepts a Braintree token and will assign the new credit card as the default billing source:

`updateCard` 메소드는 고객의 신용카드 정보를 수정하는데 사용합니다. 이 메소드는 Braintree 토큰을 받아서 새로운 신용카드를 기본적으로 청구하는데 사용하도록 지정합니다:

    $user->updateCard($token);

<a name="handling-webhooks"></a>
## Handling Webhooks
## Webhooks 처리하기

Braintree can notify your application of a variety of events via webhooks. To handle webhooks, define a route that points to Cashier's webhook controller. This controller will handle all incoming webhook requests and dispatch them to the proper controller method:

Braintree는 webhooks를 통해 다양한 이벤트를 애플리케이션에 알릴 수 있습니다. Webhook을 처리하려면 캐셔의 Webhook 컨트롤러를 가리키는 라우트를 정의하십시오. 이 컨트롤러는 들어오는 모든 webhook 요청을 처리하고 이를 적절한 컨트롤러 메소드에 전달합니다.

    Route::post(
        'braintree/webhook',
        '\Laravel\Cashier\Http\Controllers\WebhookController@handleWebhook'
    );

> {note} Once you have registered your route, be sure to configure the webhook URL in your Braintree control panel settings.

> {note} 라우트를 등록한 후에는 Braintree 제어판에서 webhook URL을 설정하십시오.

By default, this controller will automatically handle cancelling subscriptions that have too many failed charges (as defined by your Braintree settings); however, as we'll soon discover, you can extend this controller to handle any webhook event you like.

기본적으로, 이 컨트롤러는 결제 실패가 너무 많이 발행 할 경우 (Braintree 설정에서 정의한대로) 자동으로 구독을 취소 할 것입니다. 잠시후 이 컨트롤러를 확장하여 원하는 webhook 이벤트를 처리하는 것을 살펴보겠습니다.


#### Webhooks & CSRF Protection
#### Webhooks 과 CSRF 보호

Since webhooks need to bypass Laravel's [CSRF protection](/docs/{{version}}/csrf), be sure to list the URI as an exception in your `VerifyCsrfToken` middleware or list the route outside of the `web` middleware group:

webhook은 라라벨의 [CSRF 보호](/docs/{{version}}/csrf)를 우회해야하기 때문에, `VerifyCsrfToken` 미들웨어에서 예외 URI를 등록하거나, 라우트를 `web` 미들웨어 그룹 외부에 정의하십시오:

    protected $except = [
        'braintree/*',
    ];

<a name="defining-webhook-event-handlers"></a>
### Defining Webhook Event Handlers
### Webhook 이벤트 핸들러 정의하기

Cashier automatically handles subscription cancellation on failed charges, but if you have additional webhook events you would like to handle, extend the Webhook controller. Your method names should correspond to Cashier's expected convention, specifically, methods should be prefixed with `handle` and the "camel case" name of the webhook you wish to handle. For example, if you wish to handle the `dispute_opened` webhook, you should add a `handleDisputeOpened` method to the controller:

캐셔는 결제가 실패하면 자동으로 구독을 취소처리하지만, 처리하고자 하는 추가적인 webhook을 가지고 있다면, Webhook 컨트롤러를 확장하면 됩니다. 메소드 이름은 캐셔의 컨벤션과 일치해야 하고, 특히 메소드는 `handle` 로 시작해야하며 처리하고자 하는 webhook을 "카멜 케이스"로 된 이름이어야 합니다. 예를 들어 `dispute_opened` webhook을 처리하고자 한다면, 컨트롤러에 `handleDisputeOpened` 메소드를 추가해야 합니다:

    <?php

    namespace App\Http\Controllers;

    use Braintree\WebhookNotification;
    use Laravel\Cashier\Http\Controllers\WebhookController as CashierController;

    class WebhookController extends CashierController
    {
        /**
         * Handle a new dispute.
         *
         * @param  \Braintree\WebhookNotification  $webhook
         * @return \Symfony\Component\HttpFoundation\Responses
         */
        public function handleDisputeOpened(WebhookNotification $webhook)
        {
            // Handle The Webhook...
        }
    }

<a name="handling-failed-subscriptions"></a>
### Failed Subscriptions
### 실패한 정기구독

What if a customer's credit card expires? No worries - Cashier includes a Webhook controller that can easily cancel the customer's subscription for you. Just point a route to the controller:

What if a customer's credit card expires? No worries - Cashier includes a Webhook controller that can easily cancel the customer's subscription for you. As noted above, all you need to do is point a route to the controller:

만약 사용자의 신용카드가 만료 되었다면? 걱정하지 마십시오 - 캐셔는 사용자의 구독을 쉽게 취소할 수 있는 Webhook 컨트롤러를 포함하고 있습니다. 앞서 이야기 한것 처럼, 컨트롤러에 대한 경로를 지정하기만 하면됩니다.

    Route::post(
        'braintree/webhook',
        '\Laravel\Cashier\Http\Controllers\WebhookController@handleWebhook'
    );

That's it! Failed payments will be captured and handled by the controller. The controller will cancel the customer's subscription when Braintree determines the subscription has failed (normally after three failed payment attempts). Don't forget: you will need to configure the webhook URI in your Braintree control panel settings.

이게 전부입니다! 실패한 결제는 컨트롤러에 의해 확인되어 처리될 것입니다. 컨트롤러는 Stripe 가 결제에 실패하였다고 결정되면(보통 3번의 결제 시도가 실패하면) 사용자의 구독을 취소시킬 것입니다. 잊지 마세요. Braintree 제어판 설정에서 Webhook URI를 구성해야합니다.

<a name="single-charges"></a>
## Single Charges
## 한번만 결제하기

<a name="simple-charge"></a>
### Simple Charge
### 간단한 결제

> {note} You should pass the full dollar amount to the `charge` method:

> {note} 전체 금액을 `charge` 메소드에 전달해야합니다.

If you would like to make a "one off" charge against a subscribed customer's credit card, you may use the `charge` method on a billable model instance.

정기 구독하고 있는 고객의 신용 카드에 대해 "일회성 결제"를 하고 싶을 때에는 결제가 가능한 모델 인스턴스에 대해서 `charge` 메소드를 사용합니다.

    $user->charge(1);

The `charge` method accepts an array as its second argument, allowing you to pass any options you wish to the underlying charge creation. Consult the Braintree documentation regarding the options available to you when creating charges:

`charge` 메소드는 두번째 인자로 청구에 사용할 수 있는 옵션에 대한 배열을 전달 받습니다. 결제 할 때 사용할 수 있는 옵션에 대해서는 Braintree 문서를 확인하십시오:

    $user->charge(1, [
        'custom_option' => $value,
    ]);

The `charge` method will throw an exception if the charge fails. If the charge is successful, the full Braintree response will be returned from the method:

`charge` 메소드는 결제가 실패했을 경우에 예외-exception을 발생시킵니다. 결제가 성공적으로 완료되었다면 메소드에서는 Braintree 응답 객체가 반환됩니다:

    try {
        $response = $user->charge(1);
    } catch (Exception $e) {
        //
    }

<a name="charge-with-invoice"></a>
### Charge With Invoice
### 결제와 청구서

Sometimes you may need to make a one-time charge but also generate an invoice for the charge so that you may offer a PDF receipt to your customer. The `invoiceFor` method lets you do just that. For example, let's invoice the customer $5.00 for a "One Time Fee":

때로는 한 번만 결제를 진행하면서 고객에게 PDF 영수증을 발행하고자 할 수도 있습니다. `invoiceFor` 메소드는 바로 이경우 사용합니다. 예를 들어, 고객에게 "한번의 요금"을 위해서 5달러를 청구해 보겠습니다:

    $user->invoiceFor('One Time Fee', 5);

The invoice will be charged immediately against the user's credit card. The `invoiceFor` method also accepts an array as its third argument. This array contains the billing options for the invoice item. You must include a `description` option when calling the `invoiceFor` method:

청구서는 사용자의 신용 카드에 즉시 청구됩니다. `invoiceFor` 메소드는 세번째 인자로 배열을 받습니다. 이 배열에는 송장 항목에 대한 청구 옵션이 있습니다. `invoiceFor` 메소드를 호출 할 때 `description` 옵션을 포함시켜야합니다 :

    $user->invoiceFor('One Time Fee', 5, [
        'description' => 'your invoice description here',
    ]);

<a name="invoices"></a>
## Invoices
## 청구서

You may easily retrieve an array of a billable model's invoices using the `invoices` method:

`invoices` 메소드를 사용하여 청구가 가능한 모델의 청구서를 손쉽게 배열 형태로 조회 할 수 있습니다:

    $invoices = $user->invoices();

    // Include pending invoices in the results...
    $invoices = $user->invoicesIncludingPending();

When listing the invoices for the customer, you may use the invoice's helper methods to display the relevant invoice information. For example, you may wish to list every invoice in a table, allowing the user to easily download any of them:

사용자의 청구서를 나열하는 경우, 청구서 관련 정보를 표시하기 위해서 청구서의 헬퍼 함수를 사용할 수 있습니다. 예를 들어 사용자가 쉽게 다운로드 할 수 있도록 테이블 내의 모든 청구서를 목록으로 보여주고자 할 수도 있습니다:

    <table>
        @foreach ($invoices as $invoice)
            <tr>
                <td>{{ $invoice->date()->toFormattedDateString() }}</td>
                <td>{{ $invoice->total() }}</td>
                <td><a href="/user/invoice/{{ $invoice->id }}">Download</a></td>
            </tr>
        @endforeach
    </table>

<a name="generating-invoice-pdfs"></a>
### Generating Invoice PDFs
### 청구서 PDF 로 생성하기

From within a route or controller, use the `downloadInvoice` method to generate a PDF download of the invoice. This method will automatically generate the proper HTTP response to send the download to the browser:

라우트 또는 컨트롤러 안에서 `downloadInvoice` 메소드를 사용하여 청구서를 PDF 로 생성하고 다운로드 할 수 있습니다. 이 메소드는 자동으로 브라우저서 다운로드 할 수 있는 HTTP 응답을 생성합니다:

    use Illuminate\Http\Request;

    Route::get('user/invoice/{invoice}', function (Request $request, $invoiceId) {
        return $request->user()->downloadInvoice($invoiceId, [
            'vendor'  => 'Your Company',
            'product' => 'Your Product',
        ]);
    });
