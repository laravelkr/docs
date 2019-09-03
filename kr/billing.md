# Laravel Cashier
# 라라벨 캐셔

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Upgrading Cashier](#upgrading-cashier)
- [캐셔 업그레이드하기](#upgrading-cashier)
- [Installation](#installation)
- [설치하기](#installation)
- [Configuration](#configuration)
- [설정하기](#configuration)
    - [Database Migrations](#database-migrations)
    - [데이터베이스 이관](#database-migrations)
    - [Billable Model](#billable-model)
    - [Billable 모델](#billable-model)
    - [API Keys](#api-keys)
    - [API Keys](#api-keys)
    - [Currency Configuration](#currency-configuration)
    - [화폐 설정하기](#currency-configuration)
    - [Webhooks](#webhooks)
    - [Webhooks](#webhooks)
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
    - [Subscription Anchor Date](#subscription-anchor-date)
    - [정기 구독의 고정일](#subscription-anchor-date)
    - [Cancelling Subscriptions](#cancelling-subscriptions)
    - [정기 구독 취소하기](#cancelling-subscriptions)
    - [Resuming Subscriptions](#resuming-subscriptions)
    - [정기 구독 재개](#resuming-subscriptions)
- [Subscription Trials](#subscription-trials)
- [구독 트라이얼-trial](#subscription-trials)
    - [With Credit Card Up Front](#with-credit-card-up-front)
    - [신용카드 사전 등록](#with-credit-card-up-front)
    - [Without Credit Card Up Front](#without-credit-card-up-front)
    - [신용카드 없이 사전 등록](#without-credit-card-up-front)
- [Customers](#customers)
- [고객](#customers)
    - [Creating Customers](#creating-customers)
    - [고객 생성하기](#creating-customers)
- [Cards](#cards)
- [신용카드](#cards)
    - [Retrieving Credit Cards](#retrieving-credit-cards)
    - [신용카드 조회하기](#retrieving-credit-cards)
    - [Determining If A Card Is On File](#determining-if-a-card-is-on-file)
    - [카드를 소유하고 있는 것인지 확인](#determining-if-a-card-is-on-file)
    - [Updating Credit Cards](#updating-credit-cards)
    - [신용카드 정보 업데이트 하기](#updating-credit-cards)
    - [Deleting Credit Cards](#deleting-credit-cards)
    - [신용카드 정보 삭제하기](#deleting-credit-cards)
- [Handling Stripe Webhooks](#handling-stripe-webhooks)
- [Stripe webook 처리하기](#handling-stripe-webhooks)
    - [Defining Webhook Event Handlers](#defining-webhook-event-handlers)
    - [webook 이벤트 핸들러 정의하기](#defining-webhook-event-handlers)
    - [Failed Subscriptions](#handling-failed-subscriptions)
    - [실패한 정기구독](#handling-failed-subscriptions)
    - [Verifying Webhook Signatures](#verifying-webhook-signatures)
    - [Webhook의 인증 확인](#verifying-webhook-signatures)
- [Single Charges](#single-charges)
- [한번만 결제하기](#single-charges)
    - [Simple Charge](#simple-charge)
    - [기본 결제](#simple-charge)
    - [Charge With Invoice](#charge-with-invoice)
    - [청구서와 같이 결제](#charge-with-invoice)
    - [Refunding Charges](#refunding-charges)
    - [환불 수수료](#refunding-charges)
- [Invoices](#invoices)
- [청구서](#invoices)
    - [Generating Invoice PDFs](#generating-invoice-pdfs)
    - [청구서 PDF로 생성하기](#generating-invoice-pdfs)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel Cashier provides an expressive, fluent interface to [Stripe's](https://stripe.com) subscription billing services. It handles almost all of the boilerplate subscription billing code you are dreading writing. In addition to basic subscription management, Cashier can handle coupons, swapping subscription, subscription "quantities", cancellation grace periods, and even generate invoice PDFs.

라라벨 캐셔는 [Stripe](https://stripe.com)에 의해서 제공되는 손쉽고 편리한 구독(정기 과금) 서비스를 위한 인터페이스를 제공합니다. 라라벨 캐셔는 여러분이 작성하는데 어려움을 겪는 구독을 위한 청구서에서 필요한 거의 모든 관용구문들을 다룹니다.  기본적인 구독 관리 외에도, 캐셔를 통해서 쿠폰 관리, 구독 변경, 구매 수량 변경, 취소 유예 기간, 그리고 청구서를 PDF로 생성할 수도 있습니다.

> {note} If you're only performing "one-off" charges and do not offer subscriptions, you should not use Cashier. Instead, use the Stripe SDK directly.

> {note} 만약 여러분이 "한번의 결제"만을 사용하고 정기 과금형태의 구독모델을 사용하지 않는다면, 캐셔를 사용할 필요가 없습니다. 대신에, Stripe SDK를 직접 사용하면 됩니다.

<a name="upgrading-cashier"></a>
## Upgrading Cashier
## 캐셔 업그레이드하기

When upgrading to a new major version of the Cashier, it's important that you carefully review [the upgrade guide](https://github.com/laravel/cashier/blob/master/UPGRADE.md).

캐셔의 새로운 메이저(major) 버전으로 업그레이드 할 때는 [업그레이드 가이드](https://github.com/laravel/cashier/blob/master/UPGRADE.md)를 자세히 확인 해야합니다.

<a name="installation"></a>
## Installation
## 설치하기

First, require the Cashier package for Stripe with Composer:
    
먼저 Stripe를 위한 캐셔 패키지를 의존성에 추가하십시오:

    composer require laravel/cashier
    
<a name="configuration"></a>
## Configuration
## 설정하기

<a name="database-migrations"></a>
### Database Migrations
### 데이터베이스 마이그레이션

Before using Cashier, we'll also need to [prepare the database](/docs/{{version}}/migrations). We need to add several columns to your `users` table and create a new `subscriptions` table to hold all of our customer's subscriptions:

캐셔를 바로 사용하기 전에, [데이터베이스를 준비](/docs/{{version}}/migrations)해야 합니다. 몇개의 컬럼을 `users` 테이블에 추가하고, 사용자의 구독 정보를 저장할 새로운 `subscriptions` 테이블을 생성해야 합니다:

    Schema::table('users', function (Blueprint $table) {
        $table->string('stripe_id')->nullable()->collation('utf8mb4_bin');
        $table->string('card_brand')->nullable();
        $table->string('card_last_four', 4)->nullable();
        $table->timestamp('trial_ends_at')->nullable();
    });

    Schema::create('subscriptions', function (Blueprint $table) {
        $table->bigIncrements('id');
        $table->unsignedBigInteger('user_id');
        $table->string('name');
        $table->string('stripe_id')->collation('utf8mb4_bin');
        $table->string('stripe_plan');
        $table->integer('quantity');
        $table->timestamp('trial_ends_at')->nullable();
        $table->timestamp('ends_at')->nullable();
        $table->timestamps();
    });

Once the migrations have been created, run the `migrate` Artisan command.

마이그레이션들이 생성되면 `migrate` 아티즌 명령어를 실행하십시오.

<a name="billable-model"></a>
### Billable Model
### Billable 모델

Next, add the `Billable` trait to your model definition. This trait provides various methods to allow you to perform common billing tasks, such as creating subscriptions, applying coupons, and updating credit card information:

다음으로 `Billable` 트레이트-trait을 모델에 추가합니다. 이 트레이트-trait는 정기구독을 새롭게 생성하거나, 쿠폰을 적용하거나, 신용카드 정보를 업데이트 하는 것과 같은 결제와 관련된 공통의 작업들을 제공합니다:

    use Laravel\Cashier\Billable;

    class User extends Authenticatable
    {
        use Billable;
    }

<a name="api-keys"></a>
### API Keys
### API Key

Finally, you should configure your Stripe key in your `services.php` configuration file. You can retrieve your Stripe API keys from the Stripe control panel:

마지막으로 `services.php` 설정 파일에 Stripe 키를 지정해야 합니다: Stripe API key는 Stripe 설정 패널에서 확인할 수 있습니다:

    'stripe' => [
        'model' => App\User::class,
        'key' => env('STRIPE_KEY'),
        'secret' => env('STRIPE_SECRET'),
        'webhook' => [
            'secret' => env('STRIPE_WEBHOOK_SECRET'),
            'tolerance' => env('STRIPE_WEBHOOK_TOLERANCE', 300),
        ],
    ],

<a name="currency-configuration"></a>
### Currency Configuration
### 화폐 설정하기

The default Cashier currency is United States Dollars (USD). You can change the default currency by calling the `Cashier::useCurrency` method from within the `boot` method of one of your service providers. The `useCurrency` method accepts two string parameters: the currency and the currency's symbol:

캐셔의 기본 화폐는 미국달러(USD)입니다. 여러분의 서비스 프로바이더 중 한곳의 `boot` 메소드 안에서 `Cashier::useCurrency` 메소드를 호출하면, 기본화폐를 변경할 수 있습니다. `useCurrency` 메소드는 두개의 문자형 인자를 받습니다: 화폐의 이름과 화폐의 기호입니다:

    use Laravel\Cashier\Cashier;

    Cashier::useCurrency('eur', '€');

<a name="webhooks"></a>
### Webhooks
### Webhooks

To make sure Cashier properly handles all Stripe events, we strongly recommend [setting up Cashier's webhook handling](#handling-stripe-webhooks).

캐셔가 모든 Stripe 이벤트를 제대로 처리하게 하려면 [캐셔 Webhook의 동작을 설정](#handling-stripe-webhooks) 하시길 강력히 권장합니다.

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

The first argument passed to the `newSubscription` method should be the name of the subscription. If your application only offers a single subscription, you might call this `main` or `primary`. The second argument is the specific plan the user is subscribing to. This value should correspond to the plan's identifier in Stripe.

`newSubscription` 메소드에 전달되는 첫번째 인자는 구독의 제목이 되어야 합니다. 애플리케이션이 단 하나의 구독모델을 제공한다면, `main` 또는 `primary` 와 같이 사용할 수 있습니다. 두번째 인자는 사용자가 구독하고자 하는 지정된 plan입니다. 이 값은 Stripe의 plan 식별자와 일치해야 합니다.

The `create` method, which accepts a Stripe credit card / source token, will begin the subscription as well as update your database with the customer ID and other relevant billing information.

Stripe 신용카드 / 소스 토큰을 전달받아 `create` 메소드는 정기 구독을 시작하며, 데이터베이스에 고객의 ID와 관련된 결제정보를 업데이트 합니다.

#### Additional User Details
#### 추가적인 사용자의 상세 정보

If you would like to specify additional customer details, you may do so by passing them as the second argument to the `create` method:

만약 여러분이 추가적인 사용자 정보를 지정하고 싶다면 이러한 정보를 `create` 메소드의 두번째 인자로 전달하면 됩니다:

    $user->newSubscription('main', 'monthly')->create($token, [
        'email' => $email,
    ]);

To learn more about the additional fields supported by Stripe, check out Stripe's [documentation on customer creation](https://stripe.com/docs/api#create_customer).

추가적인 필드에 대한 Stripe의 지원정보를 확인하고자 한다면 Stripe의 [고객 생성에 관한 문서](https://stripe.com/docs/api#create_customer).

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

The `subscribedToPlan` method may be used to determine if the user is subscribed to a given plan based on a given Stripe plan ID. In this example, we will determine if the user's `main` subscription is actively subscribed to the `monthly` plan:

`subscribedToPlan` 메소드는 사용자가 주어진 Stripe plan ID에 대당하는 구독 플랜을 이용하는지 확인할 수 있습니다. 다음 예제는 사용자가 `main` 구독을 `monthly` plan 으로 구독하고 있는지 확인하게 됩니다:

    if ($user->subscribedToPlan('monthly', 'main')) {
        //
    }

The `recurring` method may be used to determine if the user is currently subscribed and is no longer within their trial period:

`recurring` 메소드는 사용자가 현재 구독 중인지 아니면 더이상 평가 기간에 속해 있지 않은지를 확인할 때 사용 할 수 있습니다. 

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

To determine if the user has cancelled their subscription and is no longer within their "grace period", you may use the `ended` method:

사용자가 구독을 취소했으며 더 이상 "유예 기간"내에 있지 않은지 확인하려면 `ended` 메소드를 사용할 수 있습니다 :

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

<a name="subscription-quantity"></a>
### Subscription Quantity
### 정기 구독 수량 변경하기

Sometimes subscriptions are affected by "quantity". For example, your application might charge $10 per month **per user** on an account. To easily increment or decrement your subscription quantity, use the `incrementQuantity` and `decrementQuantity` methods:

때로는 구독은 "수량"에 영향을 받을 수 있습니다. 예를 들어 여러분의 애플리케이션에서 하나의 계정의 **사용자마다** 한달에 10달러를 부과하고 있다고 한다면, `incrementQuantity` 와 `decrementQuantity` 메소드를 사용하여 구독 수를 늘리거나 줄일 수 있습니다:

    $user = User::find(1);

    $user->subscription('main')->incrementQuantity();

    // Add five to the subscription's current quantity...
    $user->subscription('main')->incrementQuantity(5);

    $user->subscription('main')->decrementQuantity();

    // Subtract five to the subscription's current quantity...
    $user->subscription('main')->decrementQuantity(5);

Alternatively, you may set a specific quantity using the `updateQuantity` method:

이 대신에, 특정 수량을 지정하고자 한다면, `updateQuantity` 메소드를 사용하면 됩니다:

    $user->subscription('main')->updateQuantity(10);

The `noProrate` method may be used to update the subscription's quantity without pro-rating the charges:

`noProrate` 메소드를 사용하면 요금에 영향을 주지 않으면서 구독의 수량을 수정 할 수 있습니다:

    $user->subscription('main')->noProrate()->updateQuantity(10);

For more information on subscription quantities, consult the [Stripe documentation](https://stripe.com/docs/subscriptions/quantities).

구독 수량에 대한 보다 자세한 내용은 [Stripe 문서](https://stripe.com/docs/subscriptions/quantities)를 참고하십시오.

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

#### Syncing Tax Percentages
### 세금 비율 동기화

When changing the hard-coded value returned by the `taxPercentage` method, the tax settings on any existing subscriptions for the user will remain the same. If you wish to update the tax value for existing subscriptions with the returned `taxPercentage` value, you should call the `syncTaxPercentage` method on the user's subscription instance:

`taxPercentage` 메소드에 의해 반환 된 하드 코딩 된 값을 변경할 때, 사용자를 위한 기존 구독에 대한 세금 설정은 동일하게 유지됩니다. 기존의 구독에 대한 세금 값을 반환 된 `taxPercentage` 값으로 업데이트하려면 사용자의 구독 인스턴스에서 `syncTaxPercentage` 메소드를 호출해야합니다.

    $user->subscription('main')->syncTaxPercentage();

<a name="subscription-anchor-date"></a>
### Subscription Anchor Date
### 정기 구독의 고정일

> {note} Modifying the subscription anchor date is only supported by the Stripe edition of Cashier.

> {note} 정기 구독의 고정일을 변경 하는 것은 Cashier의 Stripe 버전에서만 지원합니다.

By default, the billing cycle anchor is the date the subscription was created, or if a trial period is used, the date that the trial ends. If you would like to modify the billing anchor date, you may use the `anchorBillingCycleOn` method:

일반적으로 과금 주기의 고정일은 정기 구독이 시작 된 날짜 또는 평가 기간이 있는 경우 평가 기간이 종료되는 날짜입니다. 청구서의 고정일을 수정하려면 `anchorBillingCycleOn` 메소드를 사용할 수 있습니다 :

    use App\User;
    use Carbon\Carbon;

    $user = User::find(1);

    $anchor = Carbon::parse('first day of next month');

    $user->newSubscription('main', 'premium')
                ->anchorBillingCycleOn($anchor->startOfDay())
                ->create($token);

For more information on managing subscription billing cycles, consult the [Stripe billing cycle documentation](https://stripe.com/docs/billing/subscriptions/billing-cycle)

정기 구독의 결제주기 관리에 대한 자세한 내용은 [Stripe billing cycle documentation](https://stripe.com/docs/billing/subscriptions/billing-cycle) 에서 확인이 가능합니다

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

This method will set the trial period ending date on the subscription record within the database, as well as instruct Stripe to not begin billing the customer until after this date. When using the `trialDays` method, Cashier will overwrite any default trial period configured for the plan in Stripe.

이 메소드는 데이터베이스 안의 구독 레코드에 트라이얼 기간의 종료일을 설정하고, Stripe 에게 이 기간이 지나기 전까지 고객에게 청구하지 않도록 지시합니다. `trialDays` 메소드를 사용할 때 캐셔는 Stripe의 정책에 의해 설정된 기본 평가 기간을 덮어 씁니다.

> {note} If the customer's subscription is not cancelled before the trial ending date they will be charged as soon as the trial expires, so you should be sure to notify your users of their trial ending date.

> {note} 트라이얼 기간 종료 이전에 고객이 구독을 취소하지 않으면 트라이얼 기간이 만료되는 즉시 요금이 부과되므로, 트라이얼의 종료일을 사용자에게 공지해야합니다.

The `trialUntil` method allows you to provide a `DateTime` instance to specify when the trial period should end:

`trialUntil` 메소드는 트라이얼 기간이 언제 종료되어야 하는지 지정하는 `DateTime` 인스턴스를 인자로 전달받습니다:

    use Carbon\Carbon;

    $user->newSubscription('main', 'monthly')
                ->trialUntil(Carbon::now()->addDays(10))
                ->create($token);

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

Occasionally, you may wish to create a Stripe customer without beginning a subscription. You may accomplish this using the `createAsStripeCustomer` method:

경우에 따라 구독을 시작하지 않고 Stripe 고객을 만들 수도 있습니다. 이 경우 `createAsStripeCustomer` 메소드를 사용하면 됩니다 :

    $user->createAsStripeCustomer();

Once the customer has been created in Stripe, you may begin a subscription at a later date.

Stripe에서 고객을 생성 한 후 나중에 구독을 시작 할 수도 있습니다.

<a name="cards"></a>
## Cards
## 신용카드

<a name="retrieving-credit-cards"></a>
### Retrieving Credit Cards
### 신용 카드 조회하기

The `cards` method on the billable model instance returns a collection of `Laravel\Cashier\Card` instances:

청구가 가능한 모델 인스턴스에 `cards` 메소드를 호출하면 `Laravel\Cashier\Card` 의 컬렉션 인스턴스가 반환됩니다:

    $cards = $user->cards();

To retrieve the default card, the `defaultCard` method may be used;

기존으로 설정된 카드 정보를 조회하려면 `defaultCard` 메소드를 사용하면 됩니다:

    $card = $user->defaultCard();

<a name="determining-if-a-card-is-on-file"></a>
### Determining If A Card Is On File
### 카드를 소유하고 있는 것인지 확인

You may check if a customer has a credit card attached to their account using the `hasCardOnFile` method:

`hasCardOnFile` 메소드를 사용하여 신용카드가 해당 고객이 계정에 연결되어 있는 것인지 확인할 수 있습니다:

    if ($user->hasCardOnFile()) {
        //
    }

<a name="updating-credit-cards"></a>
### Updating Credit Cards
### 신용카드 정보 업데이트 하기

The `updateCard` method may be used to update a customer's credit card information. This method accepts a Stripe token and will assign the new credit card as the default billing source:

`updateCard` 메소드는 고객의 신용카드 정보를 수정하는데 사용합니다. 이 메소드는 Stripe 토큰을 받아서 새로운 신용카드를 기본적으로 청구하는데 사용하도록 지정합니다:

    $user->updateCard($token);

To sync your card information with the customer's default card information in Stripe, you may use the `updateCardFromStripe` method:

Stripe 에서 카드 정보를 고객이 기본적으로 사용할 카드로 지정(sync)하려면 `updateCardFromStripe` 메소드를 사용하면 됩니다:

    $user->updateCardFromStripe();

<a name="deleting-credit-cards"></a>
### Deleting Credit Cards
### 신용카드 정보 삭제하기

To delete a card, you should first retrieve the customer's cards with the `cards` method. Then, you may call the `delete` method on the card instance you wish to delete:

등록된 카드 정보를 삭제하려면 먼저 `card` 메소드를 사용하여 고객의 카드를 조회해야합니다. 그다음에, 삭제하고자 하는 카드 인스턴스에 `delete` 메소드를 호출하면 됩니다:

    foreach ($user->cards() as $card) {
        $card->delete();
    }

> {note} If you delete the default card, please make sure that you sync the new default card with your database using the `updateCardFromStripe` method.

> {note} 기본카드로 지정된 카드 정보를 삭제한다면, `updateCardFromStripe` 메소드를 사용하여 새로운 카드를 기본적으로 사용할 카드로 지정하도록 하십시오.

The `deleteCards` method will delete all of the card information stored by your application:

`deleteCards` 메소드는 애플리케이션에 저장된 모든 카드 정보를 삭제합니다:

    $user->deleteCards();

> {note} If the user has an active subscription, you should consider preventing them from deleting the last remaining payment source.

> {note} 고객이 활성화된 구독을 보유하고 있다면, 마지막으로 남아 있는 지급수단이 삭제되지 않도록 방지하는 것을 고려하십시오.

<a name="handling-stripe-webhooks"></a>
## Handling Stripe Webhooks
## Stripe webhook 처리하기

Stripe can notify your application of a variety of events via webhooks. To handle webhooks, define a route that points to Cashier's webhook controller. This controller will handle all incoming webhook requests and dispatch them to the proper controller method:

Stripe는 Webhook을 통해서 애플리케이션에 다양한 이벤트를 알릴 수 있습니다. webhook을 처리하려면 캐셔의 webhook 컨트롤러의 라우트를 정의해야합니다. 이 컨트롤러는 유입되는 모든 webhook request-요청을 처리하고 알맞은 컨트롤러 메소드에 전달합니다:

    Route::post(
        'stripe/webhook',
        '\Laravel\Cashier\Http\Controllers\WebhookController@handleWebhook'
    );

> {note} Once you have registered your route, be sure to configure the webhook URL in your Stripe control panel settings.

> {note} 라우트를 등록하고나서, Stripe 설정 패널에서 webhook URL을 설정하십시오.

By default, this controller will automatically handle cancelling subscriptions that have too many failed charges (as defined by your Stripe settings), customer updates, customer deletions, subscription updates, and credit card changes; however, as we'll soon discover, you can extend this controller to handle any webhook event you like.

기본적으로, 이 컨트롤러는 결제 실패가 너무 많이 발행 할 경우 (Stripe 설정에서 정의한대로) 자동으로 구독을 취소, 고객 업데이트, 고객 삭제, 정기 구독 취소 및 신용카드를 변경 할 것입니다. 잠시후 이 컨트롤러를 확장하여 원하는 webhook 이벤트를 처리하는 것을 살펴보겠습니다.

> {note} Make sure you protect incoming requests with Cashier's included [webhook signature verification](/docs/{{version}}/billing#verifying-webhook-signatures) middleware.

> {note} 캐셔에 포함된 [웹훅 서명 확인](/docs/{{version}}/billing#verifying-webhook-signatures) 미들웨어를 사용하는 것을 확인하십시오.

#### Webhooks & CSRF Protection
#### Webhook & CSRF 보호

Since Stripe webhooks need to bypass Laravel's [CSRF protection](/docs/{{version}}/csrf), be sure to list the URI as an exception in your `VerifyCsrfToken` middleware or list the route outside of the `web` middleware group:

Stripe webhook은 라라벨의 [CSRF 보호](/docs/{{version}}/csrf)를 우회해야하기 때문에, `VerifyCsrfToken` 미들웨어에서 예외 URI를 등록하거나, 라우트를 `web` 미들웨어 그룹 외부에 정의하십시오:

    protected $except = [
        'stripe/*',
    ];

<a name="defining-webhook-event-handlers"></a>
### Defining Webhook Event Handlers
### Webhook 이벤트 핸들러 정의하기

Cashier automatically handles subscription cancellation on failed charges, but if you have additional webhook events you would like to handle, extend the Webhook controller. Your method names should correspond to Cashier's expected convention, specifically, methods should be prefixed with `handle` and the "camel case" name of the webhook you wish to handle. For example, if you wish to handle the `invoice.payment_succeeded` webhook, you should add a `handleInvoicePaymentSucceeded` method to the controller:

캐셔는 결제가 실패하면 자동으로 구독을 취소처리하지만, 처리하고자 하는 추가적인 webhook을 가지고 있다면, Webhook 컨트롤러를 확장하면 됩니다. 메소드 이름은 캐셔의 컨벤션과 일치해야 하고, 특히 메소드는 `handle` 로 시작해야하며 처리하고자 하는 webhook을 "카멜 케이스"로 된 이름이어야 합니다. 예를 들어 `invoice.payment_succeeded` webhook을 처리하고자 한다면, 컨트롤러에 `handleInvoicePaymentSucceeded` 메소드를 추가해야 합니다:

    <?php

    namespace App\Http\Controllers;

    use Laravel\Cashier\Http\Controllers\WebhookController as CashierController;

    class WebhookController extends CashierController
    {
        /**
         * Handle invoice payment succeeded.
         *
         * @param  array  $payload
         * @return \Symfony\Component\HttpFoundation\Response
         */
        public function handleInvoicePaymentSucceeded($payload)
        {
            // Handle The Event
        }
    }

Next, define a route to your Cashier controller within your `routes/web.php` file:

다음으로 `routes/web.php` 파일에서 캐셔 컨트롤러에 대한 라우트를 정의하십시오:

    Route::post(
        'stripe/webhook',
        '\App\Http\Controllers\WebhookController@handleWebhook'
    );

<a name="handling-failed-subscriptions"></a>
### Failed Subscriptions
### 실패한 정기구독

What if a customer's credit card expires? No worries - Cashier includes a Webhook controller that can easily cancel the customer's subscription for you. As noted above, all you need to do is point a route to the controller:

만약 사용자의 신용카드가 만료 되었다면? 걱정하지 마십시오 - 캐셔는 사용자의 구독을 쉽게 취소할 수 있는 Webhook 컨트롤러를 포함하고 있습니다. 앞서 이야기 한것 처럼, 컨트롤러에 라우트를 지정하면 됩니다:

    Route::post(
        'stripe/webhook',
        '\Laravel\Cashier\Http\Controllers\WebhookController@handleWebhook'
    );

That's it! Failed payments will be captured and handled by the controller. The controller will cancel the customer's subscription when Stripe determines the subscription has failed (normally after three failed payment attempts).

이게 전부입니다! 실패한 결제는 컨트롤러에 의해 확인되어 처리될 것입니다. 컨트롤러는 Stripe 가 결제에 실패하였다고 결정되면(보통 3번의 결제 시도가 실패하면) 사용자의 구독을 취소시킬 것입니다.

<a name="verifying-webhook-signatures"></a>
### Verifying Webhook Signatures
### Webhook의 인증 확인

To secure your webhooks, you may use [Stripe's webhook signatures](https://stripe.com/docs/webhooks/signatures). For convenience, Cashier automatically includes a middleware which validates that the incoming Stripe webhook request is valid.

안전한 webhook을 사용하기 위해 [Stripe's webhook signatures](https://stripe.com/docs/webhooks/signatures)를 사용할 수 있습니다. Cashier에는 유입되는 Stripe webhook 요청이 유효한지 자동으로 확인하는 미들웨어를 포함하고 있습니다.

To enable webhook verification, ensure that the `stripe.webhook.secret` configuration value is set in your `services` configuration file. The webhook `secret` may be retrieved from your Stripe account dashboard.

webhook 확인을 활성화 하려면, `stripe.webhook.secret` 설정 값이 `services` 설정 파일에 설정되어 있는지 확인하십시오. webhook 의 `secret` 은 Stripe 대쉬보드에서 조회할 수 있습니다.

<a name="single-charges"></a>
## Single Charges
## 한번만 결제하기

<a name="simple-charge"></a>
### Simple Charge
### 간단한 결제

> {note} When `charge` method accepts the amount you would like to charge in the **lowest denominator of the currency used by your application**.

> {note} `charge` 메소드는 청구하려는 **애플리케이션에서 사용하는 통화의 가장 낮은 기준 금액**을 인자로 받습니다.

If you would like to make a "one off" charge against a subscribed customer's credit card, you may use the `charge` method on a billable model instance.

정기 구독하고 있는 고객의 신용 카드에 대해 "일회성 결제"를 하고 싶을 때에는 결제가 가능한 모델 인스턴스에 대해서 `charge` 메소드를 사용합니다.

    // Stripe Accepts Charges In Cents...
    $stripeCharge = $user->charge(100);

The `charge` method accepts an array as its second argument, allowing you to pass any options you wish to the underlying Stripe charge creation. Consult the Stripe documentation regarding the options available to you when creating charges:

`charge` 메소드는 두번째 인자로 Stripe 청구에 사용할 수 있는 옵션에 대한 배열을 전달 받습니다. 결제 할 때 사용할 수 있는 옵션에 대해서는 Stripe 문서를 확인하십시오:

    $user->charge(100, [
        'custom_option' => $value,
    ]);

The `charge` method will throw an exception if the charge fails. If the charge is successful, the full Stripe response will be returned from the method:

`charge` 메소드는 결제가 실패했을 경우에 예외-exception을 발생시킵니다. 결제가 성공적으로 완료되었다면 메소드에서는 Stripe 응답 객체가 반환됩니다:

    try {
        $response = $user->charge(100);
    } catch (Exception $e) {
        //
    }

<a name="charge-with-invoice"></a>
### Charge With Invoice
### 결제와 청구서

Sometimes you may need to make a one-time charge but also generate an invoice for the charge so that you may offer a PDF receipt to your customer. The `invoiceFor` method lets you do just that. For example, let's invoice the customer $5.00 for a "One Time Fee":

때로는 한 번만 결제를 진행하면서 고객에게 PDF 영수증을 발행하고자 할 수도 있습니다. `invoiceFor` 메소드는 바로 이경우 사용합니다. 예를 들어, 고객에게 "한번의 요금"을 위해서 5달러를 청구해 보겠습니다:

    // Stripe Accepts Charges In Cents...
    $user->invoiceFor('One Time Fee', 500);

The invoice will be charged immediately against the user's credit card. The `invoiceFor` method also accepts an array as its third argument. This array contains the billing options for the invoice item. The fourth argument accepted by the method is also an array. This final argument accepts the billing options for the invoice itself:

청구서는 사용자의 신용 카드에 즉시 청구됩니다. `invoiceFor` 메소드는 세번째 인자로 배열을 받습니다. 이 배열에는 송장 항목에 대한 청구 옵션이 있습니다. 메소드의 네 번째 인자는 배열이며 이 마지막 인자는 인보이스 자체에 대한 청구 옵션을 받습니다.

    $user->invoiceFor('Stickers', 500, [
        'quantity' => 50,
    ], [
        'tax_percent' => 21,
    ]);

> {note} The `invoiceFor` method will create a Stripe invoice which will retry failed billing attempts. If you do not want invoices to retry failed charges, you will need to close them using the Stripe API after the first failed charge.

> {note} `invoiceFor` 메소드는 결제 시도가 실패한 경우 이를 재시도하는 Stripe 청구서를 생성할 것입니다. 실패한 결제의 재시도를 위한 청구를 수행하지 않으려면, 결제 시도가 실패한 뒤에 Stripe API를 사용하여 청구서를 종료(close) 해야 합니다.

<a name="refunding-charges"></a>
### Refunding Charges
### 환불 수수료

If you need to refund a Stripe charge, you may use the `refund` method. This method accepts the Stripe charge ID as its only argument:

Stripe의 결제를 환불해야하는 경우 `refund` 메소드을 사용할 수 있습니다. 이 메소드는 Stripe 결제 ID만을 인수로 허용합니다.


    $stripeCharge = $user->charge(100);

    $user->refund($stripeCharge->id);

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
