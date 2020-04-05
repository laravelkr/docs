# 라라벨 캐셔

- [시작하기](#introduction)
- [캐셔 업그레이드하기](#upgrading-cashier)
- [설치하기](#installation)
- [설정하기](#configuration)
    - [Billable 모델](#billable-model)
    - [API Keys](#api-keys)
    - [통화 설정하기](#currency-configuration)
    - [로깅](#logging)
- [고객](#customers)
    - [고객 조회](#retrieving-customers)
    - [고객 생성](#creating-customers)
    - [고객 업데이트](#updating-customers)
- [결제 수단](#payment-methods)
    - [결제 수단 저장](#storing-payment-methods)
    - [결제 수단 조회](#retrieving-payment-methods)
    - [사용자에게 결제 수단이 있는지 확인](#check-for-a-payment-method)
    - [기본 결제 수단 업데이트](#updating-the-default-payment-method)
    - [결제 수단 추가](#adding-payment-methods)
    - [결제 수단 삭제](#deleting-payment-methods)
- [정기 구독 모델](#subscriptions)
    - [새로운 정기 구독 생성하기](#creating-subscriptions)
    - [정기 구독 상태 확인하기](#checking-subscription-status)
    - [정기 구독 유형 변경하기](#changing-plans)
    - [정기 구독 수량 변경하기](#subscription-quantity)
    - [정기 구독의 세금계산](#subscription-taxes)
    - [정기 구독의 고정일](#subscription-anchor-date)
    - [정기 구독 취소하기](#cancelling-subscriptions)
    - [정기 구독 재개](#resuming-subscriptions)
- [구독 평가기간-trial](#subscription-trials)
    - [결제 수단 사전 등록](#with-payment-method-up-front)
    - [결제 수단없이 사전 등록](#without-payment-method-up-front)
    - [평가기간 연장](#extending-trials)
- [Stripe webook 처리하기](#handling-stripe-webhooks)
    - [webook 이벤트 핸들러 정의하기](#defining-webhook-event-handlers)
    - [실패한 정기구독](#handling-failed-subscriptions)
    - [Webhook의 서명 검증](#verifying-webhook-signatures)
- [한번만 결제하기](#single-charges)
    - [기본 결제](#simple-charge)
    - [청구서와 같이 결제](#charge-with-invoice)
    - [환불 수수료](#refunding-charges)
- [청구서](#invoices)
    - [Generating Invoice PDFs](#generating-invoice-pdfs)
- [결제 실패 처리](#handling-failed-payments)
- [강력한 고객 인증 (SCA)](#strong-customer-authentication)
    - [추가 확인이 필요한 결제](#payments-requiring-additional-confirmation)
    - [세션 외 결제 알림](#off-session-payment-notifications)
- [Stripe SDK](#stripe-sdk)
- [테스팅](#testing)

<a name="introduction"></a>
## 시작하기

라라벨 캐셔는 [Stripe](https://stripe.com)에 의해서 제공되는 손쉽고 편리한 구독(정기 과금) 서비스를 위한 인터페이스를 제공합니다. 라라벨 캐셔는 여러분이 작성하는데 어려움을 겪는 구독을 위한 청구서에서 필요한 거의 모든 관용구문들을 다룹니다.  기본적인 구독 관리 외에도, 캐셔를 통해서 쿠폰 관리, 구독 변경, 구매 수량 변경, 취소 유예 기간, 그리고 청구서를 PDF로 생성할 수도 있습니다.

<a name="upgrading-cashier"></a>
## 캐셔 업그레이드하기

Cashier의 새 버전으로 업그레이드 할 때는 [업그레이드 가이드](https://github.com/laravel/cashier/blob/master/UPGRADE.md)를 주의 깊게 검토해야합니다.

> {note} 변경을 방지하기 위해 Cashier는 Stripe API 버전을 고정으로 사용합니다. Cashier 10.1은 Stripe API 버전 `2019-08-14`를 사용합니다. Stripe API 버전은 새로운 Stripe 기능과 개선 사항을 사용하기 위해 마이너 릴리스에서 업데이트됩니다.

<a name="installation"></a>
## 설치하기

먼저 Stripe를 위한 캐셔 패키지를 의존성에 추가하십시오.

    composer require laravel/cashier

> {note} Cashier가 모든 스트라이프 이벤트를 올바르게 처리하도록하려면 [Cashier의 웹훅 처리](#handling-stripe-webhooks)를 설정해야합니다.

#### 데이터베이스 마이그레이션

Cashier 서비스 프로바이더는 자체 데이터베이스 마이그레이션 디렉토리를 등록하므로, 패키지를 설치 한 후 데이터베이스를 마이그레이션해야합니다. Cashier 마이그레이션은 `users`테이블에 여러 컬럼을 추가하고 모든 고객의 구독을 보유 할 `subscriptions`테이블을 생성합니다.

    php artisan migrate

Cashier 패키지와 함께 제공된 마이그레이션을 덮어 써야하는 경우 `vendor:publish` 아티잔 명령어을 사용하여 마이그레이션 파일을 내보낼 수 있습니다.

    php artisan vendor:publish --tag="cashier-migrations"

Cashier의 마이그레이션이 실행되지 않게하려면 Cashier가 제공하는 `ignoreMigrations`를 사용하면 됩니다. 일반적으로 이 메소드는 `AppServiceProvider`의 `register` 메소드에서 호출합니다.

    use Laravel\Cashier\Cashier;

    Cashier::ignoreMigrations();

> {note} Stripe은 Stripe 식별자를 저장하는 데 사용되는 모든 컬럼은 대소문자를 구분해야합니다. 따라서 예를 들자면 MySQL에서 `stripe_id` 컬럼에 대한 컬럼 데이터 컬렉션이 `utf8_bin`으로 설정되어 있는지 확인해야합니다. 자세한 내용은 [스트라이프 설명서](https://stripe.com/docs/upgrades#what-changes-does-stripe-consider-to-be-backwards-compatible)에서 확인할 수 있습니다.

<a name="configuration"></a>
## 설정하기

<a name="billable-model"></a>
### Billable 모델

Cashier를 사용하기전, `Billable` 트레이트-trait를 모델에 추가합니다. 이 트레이트-trait는 정기구독을 새롭게 생성하거나, 쿠폰을 적용하거나, 결제 수단 정보를 업데이트 하는 것과 같은 결제와 관련된 공통의 작업들을 제공합니다.

    use Laravel\Cashier\Billable;

    class User extends Authenticatable
    {
        use Billable;
    }

Cashier는 Billable 모델이 Laravel과 함께 제공되는 `App\User` 클래스라고 가정합니다. 이것을 바꾸고 싶다면 `.env` 파일에서 다른 모델을 지정할 수 있습니다.

    CASHIER_MODEL=App\User

> {note} Laravel에서 제공 한 `App\User` 모델 이외의 모델을 사용하는 경우, 대체 모델의 테이블 이름과 일치하도록 제공된 [migrations](#installation)을 퍼블리싱하고 변경해야합니다.

<a name="api-keys"></a>
### API Key

다음으로 `.env` 파일에서 Stripe 키를 설정해야합니다. Stripe 컨트롤 패널에서 Stripe API 키를 찾을 수 있습니다.

    STRIPE_KEY=your-stripe-key
    STRIPE_SECRET=your-stripe-secret

<a name="currency-configuration"></a>
### 통화 설정하기

Cashier의 기본 통화는 미국 달러(USD)입니다. `CASHIER_CURRENCY` 환경 변수를 설정하여 기본 통화를 변경할 수 있습니다.

    CASHIER_CURRENCY=eur

Cashier의 통화 구성 외에도 송장에 표시 할 돈의 값을 포매팅-formatting 할 때 사용할 로케일-locale을 지정할 수도 있습니다. 내부적으로 Cashier는 [PHP의 `NumberFormatter` 클래스](https://www.php.net/manual/en/class.numberformatter.php)를 사용하여 통화 로케일-locale을 설정합니다.

    CASHIER_CURRENCY_LOCALE=nl_BE

> {note} `en` 이외의 로케일을 사용하려면 서버에 `ext-intl` PHP 확장모듈이 설치 및 설정되어 있는지 확인하십시오.

<a name="logging"></a>
#### 로깅

Cashier를 사용하면 모든 스트라이프 관련 예외를 기록 할 때 사용할 로그 채널을 지정할 수 있습니다. `CASHIER_LOGGER` 환경 변수를 사용하여 로그 채널을 지정할 수 있습니다.

    CASHIER_LOGGER=stack

<a name="customers"></a>
## 고객

<a name="retrieving-customers"></a>
### 고객 조회

`Cashier::findBillable` 메서드를 사용하여 스트라이프 ID로 고객을 조회 할 수 있습니다. 그러면 Billable 모델의 인스턴스가 반환됩니다.

    use Laravel\Cashier\Cashier;

    $user = Cashier::findBillable($stripeId);

<a name="creating-customers"></a>
### 고객 생성하기

경우에 따라 구독을 시작하지 않고 Stripe 고객을 만들 수도 있습니다. 이 경우 `createAsStripeCustomer` 메소드를 사용하면 됩니다.

    $stripeCustomer = $user->createAsStripeCustomer();

Stripe에서 고객이 생성되면 나중에 구독을 시작할 수 있습니다. 선택적인 `$options` 배열을 사용하여 Stripe API에서 지원하는 추가 파라메터를 전달할 수도 있습니다.

    $stripeCustomer = $user->createAsStripeCustomer($options);

billable 엔티티가 이미 Stripe의 고객일 경우 고객 오브젝트를 반환하려고 한다면 `createOrGetStripeCustomer` 메소드를 사용할 수도 있습니다.

    $stripeCustomer = $user->createOrGetStripeCustomer();

<a name="updating-customers"></a>
### 고객 업데이트

때때로 추가 정보를 사용하여 Stripe 고객을 직접 업데이트 할 수 있습니다. `updateStripeCustomer` 메소드를 사용하여 이를 수행 할 수 있습니다.

    $stripeCustomer = $user->updateStripeCustomer($options);

<a name="payment-methods"></a>
## 결제 수단

<a name="storing-payment-methods"></a>
### 결제 수단 저장

Stripe에서 구독을 생성하거나 "일회성" 결제를 실행하려면 결제 수단을 저장하고 Stripe에서 해당 ID를 조회해야합니다. 이를 위해 사용되는 처리 방식은 지불 방법을 구독 또는 단일 청구 중 어디에 사용할지 따라 다르므로 아래에서 두 가지를 모두 확인해보겠습니다.

#### 구독을 위한 결제 수단

고객이 나중에 신용 카드를 사용하기 위해 저장하는 경우 Stripe Setup Intents API를 사용하여 고객의 결제 수단 세부 사항을 안전하게 가져와야합니다. "설정 의도-Setup Intent"는 고객의 지불 방법 청구 의도를 스트라이핑한다는 것을 나타냅니다. Cashier의  `Billable` trait에는 새 설정 의도를 쉽게 만들 수 있는 `createSetupIntent`가 포함되어 있습니다. 고객의 결제 수단 세부 정보를 수집하는 폼을 렌더링하는 라우트 또는 컨트롤러에서 이 메서드을 호출해야합니다.

    return view('update-payment-method', [
        'intent' => $user->createSetupIntent()
    ]);

설정 의도를 작성하여 뷰에 전달한 후 결제 수단을 수집 할 element에 해당 secret을 추가해야합니다. 예를 들어 다음 "결제 방법 업데이트" 폼을 참고하십시오.

    <input id="card-holder-name" type="text">

    <!-- Stripe Elements Placeholder -->
    <div id="card-element"></div>

    <button id="card-button" data-secret="{{ $intent->client_secret }}">
        Update Payment Method
    </button>

다음으로 Stripe.js 라이브러리를 사용하고 스트라이프 element를 폼에 추가하여 고객의 결제 세부 사항을 안전하게 가져 올 수 있습니다.

    <script src="https://js.stripe.com/v3/"></script>

    <script>
        const stripe = Stripe('stripe-public-key');

        const elements = stripe.elements();
        const cardElement = elements.create('card');

        cardElement.mount('#card-element');
    </script>

다음으로, [Stripe의 `confirmCardSetup` 메소드](https://stripe.com/docs/stripe-js/reference#stripe-handle-card)를 사용하여 카드를 확인하고 안전한 "결제 방법 식별자"를 Stripe에서 조회 할 수 있습니다.

    const cardHolderName = document.getElementById('card-holder-name');
    const cardButton = document.getElementById('card-button');
    const clientSecret = cardButton.dataset.secret;

    cardButton.addEventListener('click', async (e) => {
        const { setupIntent, error } = await stripe.confirmCardSetup(
            clientSecret, {
                payment_method: {
                    card: cardElement,
                    billing_details: { name: cardHolderName.value }
                }
            }
        );

        if (error) {
            // Display "error.message" to the user...
        } else {
            // The card has been verified successfully...
        }
    });

Stripe에서 카드를 확인한 후 결과 `setupIntent.payment_method` 식별자를 라라벨 애플리케이션에 전달하여 고객에게 추가 할 수 있습니다. 결제 수단은 [결제 수단 추가](#adding-payment-methods) 또는 [기본 결제 수단 업데이트](#updating-the-default-payment-method) 일 수 있습니다. 결제 수단 식별자를 즉시 ​​사용하여 [새로운 정기 구독 생성하기](#creating-subscriptions)를 할 수도 있습니다.

> {tip} 설정 의도 및 고객 지불 정보 수집에 대한 자세한 내용을 보려면 [Stripe에서 제공하는 개요](https://stripe.com/docs/payments/save-and-reuse#php)를 참고하십시오.

#### 단일 청구에 대한 결제 수단

물론 고객의 결제 수단으로 단일 청구를 할 때는 결제 수단 식별자를 한 번만 사용하면됩니다. 스트라이프 제한으로 인해 단일 청구에 대해 고객의 저장된 기본 결제 방법을 사용할 수 없습니다. 고객이 Stripe.js 라이브러리를 사용하여 결제 수단 세부 사항을 입력하도록 해야합니다. 예를 들어 다음 양식을 참고하십시오.

    <input id="card-holder-name" type="text">

    <!-- Stripe Elements Placeholder -->
    <div id="card-element"></div>

    <button id="card-button">
        Process Payment
    </button>

다음으로 Stripe.js 라이브러리를 사용하고 스트라이프 element를 폼에 추가하여 고객의 결제 세부 사항을 안전하게 가져 올 수 있습니다.

    <script src="https://js.stripe.com/v3/"></script>

    <script>
        const stripe = Stripe('stripe-public-key');

        const elements = stripe.elements();
        const cardElement = elements.create('card');

        cardElement.mount('#card-element');
    </script>

다음으로, [Stripe의 `createPaymentMethod` 메소드](https://stripe.com/docs/stripe-js/reference#stripe-create-payment)를 사용하여 카드를 확인하고 안전한 "지불 방법 식별자"를 Stripe에서 조회 할 수 있습니다.

    const cardHolderName = document.getElementById('card-holder-name');
    const cardButton = document.getElementById('card-button');

    cardButton.addEventListener('click', async (e) => {
        const { paymentMethod, error } = await stripe.createPaymentMethod(
            'card', cardElement, {
                billing_details: { name: cardHolderName.value }
            }
        );

        if (error) {
            // Display "error.message" to the user...
        } else {
            // The card has been verified successfully...
        }
    });

카드가 성공적으로 확인되면 `paymentMethod.id`를 라라벨 애플리케이션에 전달하고 [단일 청구](#simple-charge)를 처리 할 수 ​​있습니다.

<a name="retrieving-payment-methods"></a>
### 결제 수단 검색

Billable 모델 인스턴스의 `paymentMethods` 메소드는 `Laravel\Cashier\PaymentMethod` 인스턴스의 콜렉션을 리턴합니다.

    $paymentMethods = $user->paymentMethods();

기본 결제 수단을 조회하려면 `defaultPaymentMethod` 메소드를 사용할 수 있습니다.

    $paymentMethod = $user->defaultPaymentMethod();

`findPaymentMethod` 메소드를 사용하여 청구 가능 모델이 소유한 특정 지불 수단를 조회 할 수도 있습니다.

    $paymentMethod = $user->findPaymentMethod($paymentMethodId);

<a name="check-for-a-payment-method"></a>
### 사용자에게 결제 수단이 있는지 확인

Billable 모델의 계정에 지불 방법이 연결되어 있는지 확인하려면 `hasPaymentMethod` 메소드를 사용하십시오.

    if ($user->hasPaymentMethod()) {
        //
    }

<a name="updating-the-default-payment-method"></a>
### 기본 결제 수단 업데이트

`updateDefaultPaymentMethod` 메소드는 고객의 기본 결제 수단 정보를 업데이트하는 데 사용할 수 있습니다. 이 방법은 Stripe 지불 방법 식별자를 허용하며 새 지불 방법을 기본 청구 지불 방법으로 지정합니다.

    $user->updateDefaultPaymentMethod($paymentMethod);

기본 결제 수단 정보를 Stripe의 고객의 기본 결제 수단 정보와 동기화하려면 `updateDefaultPaymentMethodFromStripe` 메소드를 사용할 수 있습니다.

    $user->updateDefaultPaymentMethodFromStripe();

> {note} 고객의 기본 결제 수단은 인보이스 발행 및 새 구독 생성에만 사용할 수 있습니다. Stripe의 제한으로 인해 단일 청구에 사용할 수 없습니다.

<a name="adding-payment-methods"></a>
### 결제 수단 추가

새로운 결제 수단을 추가하려면 billable 사용자에게 `addPaymentMethod` 메소드를 호출하여 결제 수단 식별자를 전달하면됩니다.

    $user->addPaymentMethod($paymentMethod);

> {tip} 결제 수단 식별자를 조회하는 방법을 알아 보려면 [결제 수단 저장](#storing-payment-methods)을 확인하십시오.

<a name="deleting-payment-methods"></a>
### 결제 수단 삭제

결제 수단을 삭제하려면 삭제하려는 `Laravel\Cashier\PaymentMethod` 인스턴스에서 `delete` 메소드를 호출하면됩니다.

    $paymentMethod->delete();

`deletePaymentMethods` 메소드는 Billable 모델에 대한 모든 지불 수단 정보를 삭제합니다.

    $user->deletePaymentMethods();

> {note} 사용자가 현재 정기 구독중인 경우 기본 결제 수단을 삭제하지 못하게 해야합니다.

<a name="subscriptions"></a>
## 정기 구독 모델

<a name="creating-subscriptions"></a>
### 새로운 정기 구독 생성하기

새로운 정기 구독을 생성하려면, 먼저 청구가 가능한 (일반적으로 `App\User`가 되는 ) 모델 인스턴스를 조회해야 합니다. 모델 인스턴스를 조회하면, 모델에 대한 정기 구독을 생성하기 위해 `newSubscription` 메소드를 사용할 수 있습니다.

    $user = User::find(1);

    $user->newSubscription('default', 'premium')->create($paymentMethod);

`newSubscription` 메소드에 전달되는 첫번째 인자는 정기 구독의 제목이 되어야 합니다. 애플리케이션이 단 하나의 구독모델을 제공한다면, `default` 또는 `primary` 와 같이 사용할 수 있습니다. 두번째 인자는 사용자가 구독하고자 하는 지정된 plan입니다. 이 값은 Stripe의 plan 식별자와 일치해야 합니다.

[스트라이프 결제 방법 식별자](#storing-payment-methods) 또는 스트라이프 `PaymentMethod` 오브젝트를 허용하는 `create` 메소드는 구독을 시작하고 고객 ID 및 기타 관련 청구 정보로 데이터베이스를 업데이트합니다.

> {note} 결제 수단 식별자를 `create()` 정기 구독 메소드에 직접 전달하면 자동으로 사용자의 저장된 지불 수단에 추가됩니다.

#### 추가적인 사용자의 상세 정보

만약 여러분이 추가적인 사용자 정보를 지정하고 싶다면 이러한 정보를 `create` 메소드의 두번째 인자로 전달하면 됩니다.

    $user->newSubscription('default', 'monthly')->create($paymentMethod, [
        'email' => $email,
    ]);

추가적인 필드에 대한 Stripe의 지원정보를 확인하고자 한다면 Stripe의 [고객 생성에 관한 문서](https://stripe.com/docs/api#create_customer)를 확인하십시오.

#### 쿠폰

새로운 구독을 생성 할 때 쿠폰을 적용할 수 있게 하려면 `withCoupon` 메소드를 사용하면 됩니다.

    $user->newSubscription('default', 'monthly')
         ->withCoupon('code')
         ->create($paymentMethod);

<a name="checking-subscription-status"></a>
### 정기 구독 가입 상태 확인하기

사용자가 정기 구독을 시작하고 난 뒤에, 이에 대한 정보를 확인하는 것은 메소드 하나로 손쉽게 확인이 가능합니다. 먼저 `subscribed` 메소드가 `true` 를 반환한다면 사용자의 정기구독 가입 상태는 무료 평가기간을 포함하여, 활성화 되어 있다는 것을 의미합니다.

    if ($user->subscribed('default')) {
        //
    }

`subscribed` 메소드는 [라우트 미들웨어](/docs/{{version}}/middleware)에 사용될수 있는 좋은 방법중 하나입니다. 사용자의 구독 상태에 따라서, 라우트 및 컨트롤러에 대한 액세스를 제한할 할 수 있습니다.

    public function handle($request, Closure $next)
    {
        if ($request->user() && ! $request->user()->subscribed('default')) {
            // This user is not a paying customer...
            return redirect('billing');
        }

        return $next($request);
    }

사용자가 현재 무료 평가기간(trial) 을 통해서 이용중인지 아닌지 확인하고자 한다면, `onTrial` 메소드를 사용하면 됩니다. 이 메소드는 사용자들에게 그들이 현재 무료 평가기간을 이용중이라는 정보를 표시하는데 유용하게 사용될 수 있습니다.

    if ($user->subscription('default')->onTrial()) {
        //
    }

`subscribedToPlan` 메소드는 사용자가 주어진 Stripe plan ID에 대당하는 구독 플랜을 이용하는지 확인할 수 있습니다. 다음 예제는 사용자가 `default` 구독을 `monthly` plan 으로 구독하고 있는지 확인하게 됩니다.

    if ($user->subscribedToPlan('monthly', 'default')) {
        //
    }

`subscribedToPlan` 메소드에 배열을 전달하면 사용자의 `default`구독이 `monthly`또는 `yearly` plan이 활성화된 상태로 가입되어 있는지 확인할 수 있습니다.

    if ($user->subscribedToPlan(['monthly', 'yearly'], 'default')) {
        //
    }

`recurring` 메소드는 사용자가 현재 구독 중이며 더이상 평가기간에 속해 있지 않은지를 확인할 때 사용 할 수 있습니다. 

    if ($user->subscription('default')->recurring()) {
        //
    }

#### 정기 구독 취소하기

사용자가 이전에 한번 구독 후, 취소 했는지를 확인 하기 위해서 `cancelled` 메소드를 사용할 수 있습니다.

    if ($user->subscription('default')->cancelled()) {
        //
    }

또한 사용자가 구독을 취소했지만 아직 완전히 만료 되기 전의 "유예 기간" 중인지를 확인할 수 있습니다. 예를 들어, 사용자가 3 월 5 일에 구독을 취소하고 3 월 10 일에 만료 될 경우, 해당 사용자는 3 월 10 일까지가 "유예 기간”입니다. `subscribed` 메소드는 이 기간 동안 여전히 `true` 를 반환하는 것에 유의하십시오.

    if ($user->subscription('default')->onGracePeriod()) {
        //
    }

사용자가 구독을 취소했으며 더 이상 "유예 기간"내에 있지 않은지 확인하려면 `ended` 메소드를 사용할 수 있습니다.

    if ($user->subscription('default')->ended()) {
        //
    }

<a name="incomplete-and-past-due-status"></a>
#### 미완료 및 기한 만료 상태

구독 후 생성 된 보조 결제 수단이 필요한 경우 구독이 `incomplete`로 표시됩니다. 구독 상태는 Cashier의 `subscriptions` 데이터베이스 테이블의 `stripe_status` 컬럼에 저장됩니다.

마찬가지로, 플랜을 변경 할 때 보조 결제 수단이 필요한 경우 구독은 `past_due`로 표시됩니다. 구독이 이 상태 중 하나에 있으면 고객이 지불을 확인하기 전까지는 구독이 활성화되지 않습니다. 청구 가능 모델 또는 구독 인스턴스에서 `hasIncompletePayment` 메소드를 사용하여 구독에 완료되지 않은 결제가 있는지 확인하십시오.

    if ($user->hasIncompletePayment('default')) {
        //
    }

    if ($user->subscription('default')->hasIncompletePayment()) {
        //
    }

구독에 결제가 완료되지 않았다면 `latestPayment`식별자를 전달하여 사용자를 Cashier의 결제 확인 페이지로 안내해야합니다. 구독 인스턴스에서 사용 가능한 `latestPayment` 메소드를 사용하여 이 식별자를 조회 할 수 있습니다.

    <a href="{{ route('cashier.payment', $subscription->latestPayment()->id) }}">
        Please confirm your payment.
    </a>

`past_due`상태 일 때 구독을 계속 활성 상태로 유지하려면 Cashier에서 제공 한 `keepPastDueSubscriptionsActive` 메소드를 사용할 수 있습니다. 일반적으로이 메소드는 `AppServiceProvider`의 `register` 메소드에서 호출해야합니다.

    use Laravel\Cashier\Cashier;

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        Cashier::keepPastDueSubscriptionsActive();
    }

> {note} 구독이 `incomplete`상태 인 경우 결제가 확인 될 때까지 구독을 변경할 수 없습니다. 따라서 `swap`및 `updateQuantity`메소드는 구독이 `incomplete`상태 인 경우 예외를 발생시킵니다.

<a name="changing-plans"></a>
### 정기 구독 유형 변경하기

사용자가 애플리케이션을 구독한 뒤에, 구독 플랜을 변경하고자 하는 경우는 자주 있습니다. 사용자를 새로운 구독 플랜으로 변경하게 하려면 `swap` 메소드에 플랜의 id를 전달하면 됩니다.

    $user = App\User::find(1);

    $user->subscription('default')->swap('provider-plan-id');

사용자가 평가기간(trial)중이라면, 평가기간은 정상적으로 유지됩니다. 또한 구독의 "수량"이 존재하는 경우에도 이 수량은 유지됩니다.

구독 유형을 변경하고 사용자의 현재 구독 평가기간을 취소하려면 `skipTrial` 메소드를 하용하면 됩니다.

    $user->subscription('default')
            ->skipTrial()
            ->swap('provider-plan-id');

Plan을 바꾸고 다음 청구주기를 기다리는 대신 사용자에게 즉시 송장을 보내려면 `swapAndInvoice` 메소드를 사용할 수 있습니다.

    $user = App\User::find(1);

    $user->subscription('default')->swapAndInvoice('provider-plan-id');

#### 비례 배분

기본적으로, 스트라이프는 요금제를 교체 할 때 요금을 비례 배분하여 부과합니다. `noProrate`메소드는 요금을 비례 배분해서 생성하지 않고 구독을 업데이트하는 데 사용할 수 있습니다.

    $user->subscription('default')->noProrate()->swap('provider-plan-id');

구독 비례 배분에 대한 자세한 내용은 [스트라이프 문서](https://stripe.com/docs/billing/subscriptions/prorations)를 참조하십시오.

<a name="subscription-quantity"></a>
### 정기 구독 수량 변경하기

때로는 구독은 "수량"에 영향을 받을 수 있습니다. 예를 들어 여러분의 애플리케이션에서 하나의 계정의 **사용자마다** 한달에 10달러를 부과하고 있다고 한다면, `incrementQuantity` 와 `decrementQuantity` 메소드를 사용하여 구독 수를 늘리거나 줄일 수 있습니다.

    $user = User::find(1);

    $user->subscription('default')->incrementQuantity();

    // Add five to the subscription's current quantity...
    $user->subscription('default')->incrementQuantity(5);

    $user->subscription('default')->decrementQuantity();

    // Subtract five to the subscription's current quantity...
    $user->subscription('default')->decrementQuantity(5);

이 대신에, 특정 수량을 지정하고자 한다면, `updateQuantity` 메소드를 사용하면 됩니다.

    $user->subscription('default')->updateQuantity(10);

`noProrate` 메소드를 사용하면 요금에 영향을 주지 않으면서 구독의 수량을 수정 할 수 있습니다.

    $user->subscription('default')->noProrate()->updateQuantity(10);

구독 수량에 대한 보다 자세한 내용은 [Stripe 문서](https://stripe.com/docs/subscriptions/quantities)를 참고하십시오.

<a name="subscription-taxes"></a>
### 정기구독의 세금계산

고객이 구매를 진행할 때 세금에 대한 퍼센트를 지정하려면 청구가 가능한 모델에 `taxPercentage` 메소드를 사용하여 구현 소수점 자릿수가 2 자리 이내에서 0에서 100 사이의 숫자를 반환하면 됩니다.

    public function taxPercentage()
    {
        return 20;
    }

`taxPercentage` 메소드는 모델별로 세율을 적용하여 다양한 국가와 세율을 해당 국가의 사용자를 기반으로 적용할 수 이 있습니다.

> {note} `taxPercentage` 메소드는 정기구독의 결제 시에만 적용됩니다. "한번 결제"에서 캐셔를 사용하는 경우 세율을 직접 적용해야합니다.

### 세금 비율 동기화

`taxPercentage` 메소드에 의해 반환 된 하드 코딩 된 값을 변경할 때, 사용자를 위한 기존 구독에 대한 세금 설정은 동일하게 유지됩니다. 기존의 구독에 대한 세금 값을 반환 된 `taxPercentage` 값으로 업데이트하려면 사용자의 구독 인스턴스에서 `syncTaxPercentage` 메소드를 호출해야합니다.

    $user->subscription('default')->syncTaxPercentage();

<a name="subscription-anchor-date"></a>
### 정기 구독의 고정일

일반적으로 과금 주기의 고정일은 정기 구독이 시작 된 날짜 또는 평가기간이 있는 경우 평가기간이 종료되는 날짜입니다. 청구서의 고정일을 수정하려면 `anchorBillingCycleOn` 메소드를 사용할 수 있습니다.

    use App\User;
    use Carbon\Carbon;

    $user = User::find(1);

    $anchor = Carbon::parse('first day of next month');

    $user->newSubscription('default', 'premium')
                ->anchorBillingCycleOn($anchor->startOfDay())
                ->create($paymentMethod);

정기 구독의 결제주기 관리에 대한 자세한 내용은 [Stripe billing cycle documentation](https://stripe.com/docs/billing/subscriptions/billing-cycle) 에서 확인이 가능합니다

<a name="cancelling-subscriptions"></a>
### 정기구독 취소하기

정기구독을 취소하기 위해서는, 사용자의 정기구독에 대해서 `cancle` 메소드를 호출하면 됩니다.

    $user->subscription('default')->cancel();

구독이 취소되면 캐셔는 자동으로 데이터베이스의 `ends_at` 컬럼을 설정합니다. 이 컬럼은 언제 `subscribed` 메소드가 `false`를 반환해야 하는지 알기 위해서 사용되어 집니다. 예를 들어 사용자가 구독을 3월 1일에 취소했지만, 정기 구독이 3월 5일에 종료하도록 예정되어 있다면 `subscribed` 메소드는 3월 5일까지 `true`를 반환할 것입니다.

사용자가 구독을 취소했지만 아직 "유예 기간"이 남아 있는지 확인하고자 한다면, `onGracePeriod` 메소드를 사용하면 됩니다.

    if ($user->subscription('default')->onGracePeriod()) {
        //
    }

정기구독을 즉시 취소하고자 한다면, 사용자의 정기구독 에서 `cancelNow` 메소드를 호출하면 됩니다.

    $user->subscription('default')->cancelNow();

<a name="resuming-subscriptions"></a>
### 정기 구독 재개하기

만약 사용자가 취소한 구독을 재개하려면, `resume` 메소드를 사용하면 됩니다. 사용자는 구독을 재개하기 위해 **반드시** 자신의 유예 기간에 내에 있어야 합니다.

    $user->subscription('default')->resume();

만약 사용자가 구독을 취소하고 다음 정기 구독을 재개하는 경우 그 등록의 만료일이 되기 전까지는 비용이 바로 부과되지는 않습니다. 대신, 사용자의 정기 구독은 재활성화 되며, 원래의 주기에 따라 과금됩니다.

<a name="subscription-trials"></a>
## 구독 평가기간

> {note} Cashier는 구독의 평가판 날짜를 관리하며 스트라이프 plan에서 가져오지 않습니다. 따라서 Cashier가 평가판을 대신 관리 할 수 ​​있도록 평가판 기간이 0일이 되도록 Stripe에서 plan을 구성해야합니다.

<a name="with-payment-method-up-front"></a>
### 결제 수단 사전 등록

고객에게 평가기간을 제공하고, 결제 정보를 사전에 등록 해달라고 요청하고자 한다면, 구독을 생성할 때 `trialDays` 메소드를 해야합니다.

    $user = User::find(1);

    $user->newSubscription('default', 'monthly')
                ->trialDays(10)
                ->create($paymentMethod);

이 메소드는 데이터베이스 안의 구독 레코드에 평가기간의 종료일을 설정하고, Stripe 에게 이 기간이 지나기 전까지 고객에게 청구하지 않도록 지시합니다. `trialDays` 메소드를 사용할 때 캐셔는 Stripe의 정책에 의해 설정된 기본 평가기간을 덮어 씁니다.

> {note} 평가기간 종료 이전에 고객이 구독을 취소하지 않으면 평가기간이 만료되는 즉시 요금이 부과되므로, 평가기간의 종료일을 사용자에게 공지해야합니다.

`trialUntil` 메소드는 평가기간이 언제 종료되어야 하는지 지정하는 `DateTime` 인스턴스를 인자로 전달받습니다.

    use Carbon\Carbon;

    $user->newSubscription('default', 'monthly')
                ->trialUntil(Carbon::now()->addDays(10))
                ->create($paymentMethod);

사용자가 현재 평가기간 중인지 확인하려면, 사용자 인스턴스에서 `onTrial` 메소드를 사용하거나, 구독 인스턴스에서 `onTrial` 메소드를 사용하면 됩니다. 다음의 두 예제는 동일합니다.

    if ($user->onTrial('default')) {
        //
    }

    if ($user->subscription('default')->onTrial()) {
        //
    }

<a name="without-payment-method-up-front"></a>
### 결제 수단없이 사전 등록

고객에게 신용카드에 대한 결제 정보의 사전등록 없이 평가기간을 부여하고자 한다면, 사용자의 `trial_ends_at` 컬럼에 트리이얼 종료기간을 설정하면 됩니다. 이는 일반적으로 사용자를 등록할 때 설정하게 됩니다.

    $user = User::create([
        // Populate other user properties...
        'trial_ends_at' => now()->addDays(10),
    ]);

> {note} 모델 정의에 `trial_ends_at`이 [날짜 mutator](/docs/{{version}}/eloquent-mutators#date-mutators)로 추가되어 있어야 합니다.

기존 구독에 연결되는 것이 아니기 때문에, Cashier에서는 이 타입의 평가기간을 "포괄적 평가기간(generic trial)"이라고 지칭하고 있습니다. `User` 인스턴스의 `onTrial` 메소드는 만약 현재 시간이 아직 `trial_ends_at`을 넘기지 않았다면 `true`를 반환합니다.

    if ($user->onTrial()) {
        // User is within their trial period...
    }

또한 특별히 사용자가 현재 "포괄적" 평가기간 중이며 아직 실제 구독을 생성하지 않았는지 알고자 한다면 `onGenericTrial` 메소드를 사용할 수 있습니다.

    if ($user->onGenericTrial()) {
        // User is within their "generic" trial period...
    }

사용자에게 실제 구독을 생성할 준비가 되면, 평소대로 `newSubscription` 메소드를 사용하면됩니다.

    $user = User::find(1);

    $user->newSubscription('default', 'monthly')->create($paymentMethod);

<a name="extending-trials"></a>
### 평가기간 연장

`extendTrial` 메소드를 사용하면 구독 생성 후 평가기간을 연장 할 수 있습니다.

    // End the trial 7 days from now...
    $subscription->extendTrial(
        now()->addDays(7)
    );

    // Add an additional 5 days to the trial...
    $subscription->extendTrial(
        $subscription->trial_ends_at->addDays(5)
    );

평가판이 이미 만료되었고 고객이 이미 구독 요금을 청구하는 경우에도 평가판을 연장하여 제공 할 수 있습니다. 평가판 기간에 포함 된 시간은 고객의 다음 청구서에서 공제됩니다.

<a name="handling-stripe-webhooks"></a>
## Stripe webhook 처리하기

> {tip} [the Stripe CLI](https://stripe.com/docs/stripe-cli)을 사용하여 로컬 개발 도중에도 웹훅을 테스트 할 수 있습니다.

Stripe은 웹훅을 통해 다양한 이벤트를 애플리케이션에 알릴 수 있습니다. 기본적으로 Cashier의 웹훅 컨트롤러를 가리키는 경로는 Cashier 서비스 프로바이더를 통해 설정됩니다. 이 컨트롤러는 들어오는 모든 웹훅 요청을 처리합니다.

기본적으로 이 컨트롤러는 너무 많은 청구 실패 (스트라이프 설정에 의해 정의 됨), 고객 업데이트, 고객 삭제, 가입 업데이트 및 결제 수단 변경이 있는 가입 취소를 자동으로 처리합니다. 그러나 곧 알게 되겠지만 원하는 웹훅 이벤트를 처리하도록 이 컨트롤러를 확장 할 수 있습니다.

애플리케이션이 Stripe 웹훅을 처리 할 수 있도록하려면 Stripe 제어판에서 webhook URL을 구성하십시오. Stripe 제어판에서 구성해야하는 모든 웹훅의 전체 목록은 다음과 같습니다.

- `customer.subscription.updated`
- `customer.subscription.deleted`
- `customer.updated`
- `customer.deleted`
- `invoice.payment_action_required`

> {note} 캐셔에 포함된 [웹훅 서명 확인](/docs/{{version}}/billing#verifying-webhook-signatures) 미들웨어를 사용하는 것을 확인하십시오.

#### Webhook & CSRF 보호

Stripe webhook은 라라벨의 [CSRF 보호](/docs/{{version}}/csrf)를 우회해야하기 때문에, `VerifyCsrfToken` 미들웨어에서 예외 URI를 등록하거나, 라우트를 `web` 미들웨어 그룹 외부에 정의하십시오:

    protected $except = [
        'stripe/*',
    ];

<a name="defining-webhook-event-handlers"></a>
### Webhook 이벤트 핸들러 정의하기

캐셔는 결제가 실패하면 자동으로 구독을 취소처리하지만, 처리하고자 하는 추가적인 webhook을 가지고 있다면, Webhook 컨트롤러를 확장하면 됩니다. 메소드 이름은 캐셔의 컨벤션과 일치해야 하고, 특히 메소드는 `handle` 로 시작해야하며 처리하고자 하는 webhook을 "카멜 케이스"로 된 이름이어야 합니다. 예를 들어 `invoice.payment_succeeded` webhook을 처리하고자 한다면, 컨트롤러에 `handleInvoicePaymentSucceeded` 메소드를 추가해야 합니다.

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

다음으로 `routes/web.php` 파일에서 캐셔 컨트롤러에 대한 라우트를 정의하십시오. 이것은 기본으로 전달되는 라우트를 덮어 씁니다.

    Route::post(
        'stripe/webhook',
        '\App\Http\Controllers\WebhookController@handleWebhook'
    );

Cashier는 웹훅이 수신되면 `Laravel\Cashier\Events\WebhookReceived` 이벤트를, Cashier가 웹훅을 처리하면 `Laravel\Cashier\Events\WebhookHandled` 이벤트를 생성합니다. 두 이벤트 모두 Stripe 웹훅의 전체 페이로드를 포함합니다.

<a name="handling-failed-subscriptions"></a>
### 실패한 정기구독

고객의 신용 카드가 만료되면 어떻게됩니까? 걱정하지 마십시오. Cashier의 웹훅 컨트롤러는 고객의 구독을 취소합니다. 결제 실패는 컨트롤러에 의해 자동으로 캡처 및 처리됩니다. Stripe은 구독이 실패했다고 판단하면 (일반적으로 3 번의 지불 시도 실패 후) 고객의 구독을 취소합니다.

<a name="verifying-webhook-signatures"></a>
### Webhook의 서명 검증

안전한 webhook을 사용하기 위해 [Stripe's webhook signatures](https://stripe.com/docs/webhooks/signatures)를 사용할 수 있습니다. Cashier에는 유입되는 Stripe webhook 요청이 유효한지 자동으로 확인하는 미들웨어를 포함하고 있습니다.

웹훅의 검증을 활성화하려면 `.env` 파일에 `STRIPE_WEBHOOK_SECRET` 환경 변수가 설정되어 있는지 확인하십시오. webhook 의 `secret`은 Stripe 계정 대시보드에서 조회 할 수 있습니다.

<a name="single-charges"></a>
## 한번만 결제하기

<a name="simple-charge"></a>
### 간단한 결제

>> {note} `charge` 메소드는 **애플리케이션에서 사용하는 통화의 최저 denominator** 로 청구하려는 금액을 수락합니다.

가입한 고객의 지불 수단에 대해 "일회성"청구를하려면 청구 가능 모델 인스턴스에서 `charge` 메소드를 사용할 수 있습니다. 두 번째 인수로 [결제 수단 식별자](#storing-payment-methods)를 제공해야합니다.

    // Stripe Accepts Charges In Cents...
    $stripeCharge = $user->charge(100, $paymentMethod);

`charge` 메소드는 배열을 세 번째 인수로 받아들이므로 원하는 모든 옵션을 기본 Strip charge 생성에 전달할 수 있습니다. 청구 작성시 사용 가능한 옵션에 대해서는 Stripe 문서를 참조하십시오.

    $user->charge(100, $paymentMethod, [
        'custom_option' => $value,
    ]);

기본 고객이나 사용자없이 `charge` 메서드를 사용할 수도 있습니다.

    use App\User;

    $stripeCharge = (new User)->charge(100, $paymentMethod);

충전이 실패하면 `charge` 메소드에서 예외가 발생합니다. 청구가 성공하면 `Laravel\Cashier\Payment` 인스턴스가 메소드에서 리턴됩니다.

    try {
        $payment = $user->charge(100, $paymentMethod);
    } catch (Exception $e) {
        //
    }

<a name="charge-with-invoice"></a>
### 결제와 청구서

때로는 한 번만 결제를 진행하면서 고객에게 PDF 영수증을 발행하고자 할 수도 있습니다. `invoiceFor` 메소드는 바로 이경우 사용합니다. 예를 들어, 고객에게 "한번의 요금"을 위해서 5달러를 청구해 보겠습니다.

    // Stripe Accepts Charges In Cents...
    $user->invoiceFor('One Time Fee', 500);

청구서는 사용자의 청구수단에 즉시 발행됩니다. `invoiceFor` 메소드는 세번째 인자로 배열을 받습니다. 이 배열에는 송장 항목에 대한 청구 옵션이 있습니다. 메소드의 네 번째 인자는 배열이며 이 마지막 인자는 인보이스 자체에 대한 청구 옵션을 받습니다.

    $user->invoiceFor('Stickers', 500, [
        'quantity' => 50,
    ], [
        'tax_percent' => 21,
    ]);

> {note} `invoiceFor` 메소드는 결제 시도가 실패한 경우 이를 재시도하는 Stripe 청구서를 생성할 것입니다. 실패한 결제의 재시도를 위한 청구를 수행하지 않으려면, 결제 시도가 실패한 뒤에 Stripe API를 사용하여 청구서를 종료(close) 해야 합니다.

<a name="refunding-charges"></a>
### 환불 수수료

Stripe의 결제를 환불해야하는 경우 `refund` 메소드를 사용할 수 있습니다. 이 메소드는 첫 번째 인수로 Stripe Payment Intent ID를 입력 받습니다.

    $payment = $user->charge(100, $paymentMethod);

    $user->refund($payment->id);

<a name="invoices"></a>
## 청구서

`invoices` 메소드를 사용하여 청구가 가능한 모델의 청구서를 손쉽게 배열 형태로 조회 할 수 있습니다.

    $invoices = $user->invoices();

    // Include pending invoices in the results...
    $invoices = $user->invoicesIncludingPending();

사용자의 청구서를 나열하는 경우, 청구서 관련 정보를 표시하기 위해서 청구서의 헬퍼 함수를 사용할 수 있습니다. 예를 들어 사용자가 쉽게 다운로드 할 수 있도록 테이블 내의 모든 청구서를 목록으로 보여주고자 할 수도 있습니다.

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
### 청구서 PDF 로 생성하기

라우트 또는 컨트롤러 안에서 `downloadInvoice` 메소드를 사용하여 청구서를 PDF 로 생성하고 다운로드 할 수 있습니다. 이 메소드는 자동으로 브라우저서 다운로드 할 수 있는 HTTP 응답을 생성합니다.

    use Illuminate\Http\Request;

    Route::get('user/invoice/{invoice}', function (Request $request, $invoiceId) {
        return $request->user()->downloadInvoice($invoiceId, [
            'vendor' => 'Your Company',
            'product' => 'Your Product',
        ]);
    });

<a name="handling-failed-payments"></a>
## 결제 실패 처리

때로는 구독 결제 또는 단일 청구에 실패 할 수 있습니다. 이런 일이 발생하면 캐셔은 이 문제가 발생했음을 알리는 `IncompletePayment`예외를 발생시킵니다. 이 예외를 발생시킨 후 처리 방법에 대한 두 가지 옵션이 있습니다.

첫번째로는, 고객을 Cashier에 포함 된 결제 확인 전용 페이지로 리디렉션 할 수 있습니다. 이 페이지는 Cashier의 서비스 프로바이더를 통해 이미 관련된 경로가 등록되어 있습니다. 따라서 `IncompletePayment`예외가 발생하여 결제 확인 페이지로 리디렉션 될 수 있습니다.

    use Laravel\Cashier\Exceptions\IncompletePayment;

    try {
        $subscription = $user->newSubscription('default', $planId)
                                ->create($paymentMethod);
    } catch (IncompletePayment $exception) {
        return redirect()->route(
            'cashier.payment',
            [$exception->payment->id, 'redirect' => route('home')]
        );
    }

결제 확인 페이지에서 고객에게 신용 카드 정보를 다시 입력하라는 메시지가 표시되고 "3D 보안" 확인과 같이 Stripe에 필요한 추가 작업을 수행하라는 메시지가 표시됩니다. 결제를 확인한 후 사용자는 위에 지정된 `redirect` 파라메터가 제공한 URL로 리디렉션됩니다. 리디렉션시 `message` (문자열) 및 `success` (정수) 쿼리스트링 변수가 URL에 추가됩니다.

또는 Stripe이 지불 확인을 처리하도록 허용 할 수 있습니다. 이 경우 결제 확인 페이지로 리디렉션하는 대신 스트라이프 대시 보드에서 [스트라이프 자동 결제 이메일 설정](https://dashboard.stripe.com/account/billing/automatic) 을 할 수 있습니다. 그러나 `IncompletePayment`예외가 발생하더라도 사용자에게 추가 지불 확인 지시 사항이 포함 된 이메일을 수신하도록 알려야합니다.

`Billable` 사용자의 `charge`, `invoiceFor` 및  `invoice`메서드에 대해서는 지불 예외가 발생할 수 있습니다. 구독을 처리 할 때 `SubscriptionBuilder`의`create` 메소드와 `Subscription` 모델의 `incrementAndInvoice` 및 `swapAndInvoice` 메소드에서 예외가 발생할 수 있습니다.

`IncompletePayment`를 확장하는 두 가지 유형의 지불 예외가 있습니다. 사용자 경험을 커스터마이징 할 수 있도록 필요한 경우 이를 별도로 처리할 수 있습니다.


- `PaymentActionRequired`: 결제를 확인하고 처리하기 위해 Stripe에서 추가 확인이 필요함을 나타냅니다.
- `PaymentFailure`: 사용 가능한 자금이 부족한 등 여러 가지 이유로 결제가 실패했음을 나타냅니다.

<a name="strong-customer-authentication"></a>
## 강력한 고객 인증

If your business is based in Europe you will need to abide by the Strong Customer Authentication (SCA) regulations. These regulations were imposed in September 2019 by the European Union to prevent payment fraud. Luckily, Stripe and Cashier are prepared for building SCA compliant applications.

비즈니스가 유럽에 기반을 둔 경우 강력한 고객 인증 (SCA) 규정을 준수해야합니다. 이 규정은 2019년 9월 유럽 연합에 의해 지불 사기를 방지하기 위해 부과되었습니다. 운 좋게도 Stripe과 Cashier는 SCA 호환 애플리케이션을 구축 할 준비가되어 있습니다.

> {note} 시작하기 전에 [PS2 및 SCA에 대한 Stripe 안내서](https://stripe.com/en-be/guides/strong-customer-authentication)와 [새로운 SCA API에 대한 문서](https://stripe.com/docs/strong-customer-authentication) 를 검토하십시오.

<a name="payments-requiring-additional-confirmation"></a>
### 추가 확인이 필요한 결제

SCA 규정은 종종 지불을 확인하고 처리하기 위해 추가 검증이 필요합니다. 이런 일이 발생하면 캐셔는 이 추가 확인이 필요하다는 것을 알려주는 `PaymentActionRequired` 예외를 처리합니다. 이러한 예외를 처리하는 방법에 대한 자세한 내용은 [여기](#handling-failed-payments)를 참조하십시오.

#### 미완료 및 기한 만료 상태

결제에 추가 확인이 필요한 경우, 구독은 `stripe_status` 데이터베이스 컬럼에 표시된 것처럼 `incomplete` 또는 `past_due`상태로 유지됩니다. Cashier는 결제 확인이 완료되는 즉시 웹훅을 통해 고객의 구독을 자동으로 활성화합니다.

`incomplete` 와 `past_due` 상태에 대한 자세한 내용은 [추가 문서](#incomplete-and-past-due-status)를 참조하십시오.

<a name="off-session-payment-notifications"></a>
### 세션 외 결제 알림

SCA 규정에 따라 고객은 구독이 활성화되어있는 동안에도 결제 세부 정보를 확인해야하므로 세션 외 지불 확인이 필요한 경우 Cashier가 고객에게 결제 알림을 보낼 수 있습니다. 예를 들어 구독이 갱신 될 때 발생할 수 있습니다. `CASHIER_PAYMENT_NOTIFICATION` 환경 변수에 알림 클래스로 설정하여 Cashier의 지불 알림으로 사용할 수 있습니다. 기본적으로 이 알림은 비활성화되어 있습니다. 물론 Cashier에는 이 목적으로 사용할 수있는 알림 클래스가 포함되어 있지만 원하는 경우 자신의 알림 클래스를 자유롭게 설정 할 수 있습니다.

    CASHIER_PAYMENT_NOTIFICATION=Laravel\Cashier\Notifications\ConfirmPayment

세션 외 결제 확인 알림이 전달되도록하려면 애플리케이션에 대한 [Stripe webhooks 구성](#handling-stripe-webhooks) 및 Stripe 대시 보드에서 `invoice.payment_action_required` webhook이 활성화되어 있는지 확인하십시오. 또한 `Billable` 모델은 Laravel의 `Illuminate\Notifications\Notifiable` trait도 사용해야합니다.

> {note} 고객이 추가 확인이 필요한 수동 결제를 하는 경우에도 알림이 전송됩니다. 불행히도 Stripe이 지불이 수동 또는 "세션 외"로 완료되었음을 알 수있는 방법은 없습니다. 그러나 고객은 이미 결제를 확인한 후 결제 페이지를 방문하면 "결제 성공"메시지를 보게됩니다. 고객은 실수로 동일한 결제를 두 번 확인하고 실수로 두 번째 청구를 할 수 없습니다.

<a name="stripe-sdk"></a>
## Stripe SDK

Cashier의 많은 객체는 Stripe SDK 객체를 감싸는 래퍼입니다. Stripe 객체와 직접 상호 작용하려면 `asStripe` 메소드를 사용하여 편리하게 조회 할 수 있습니다.

    $stripeSubscription = $subscription->asStripeSubscription();

    $stripeSubscription->update(['application_fee_percent' => 5]);

<a name="testing"></a>
## 테스팅

캐셔를 사용하는 애플리케이션을 테스트 할 때 실제 HTTP 요청을 Stripe API로 모킹 할 수 있습니다. 그러나 이를 위해서는 캐셔 자신의 행동을 부분적으로 다시 구현해야합니다. 따라서 테스트가 실제 Stripe API에 도달하도록 허용하는 것이 좋습니다. 속도는 느리지만 애플리케이션이 예상대로 작동하고 있으며 느린 테스트는 자체 PHPUnit 테스트 그룹 내에 배치 될 수 있습니다.

테스트시 캐셔 자체에는 이미 훌륭한 테스트 suite가 있으므로 모든 기본 캐셔 동작이 아니라 자체 애플리케이션의 서브스크립션 및 지불 플로우 테스트에만 중점을 두어야합니다.

시작하려면, **testing** 버전의 Stripe secret을 `phpunit.xml` 파일에 추가하십시오.

    <env name="STRIPE_SECRET" value="sk_test_<your-key>"/>

이제 테스트하는 동안 캐셔와 상호 작용할 때마다 실제 API 요청이 Stripe 테스트 환경으로 전송됩니다. 편의상 스트라이프 테스트 계정에 구독/플랜을 미리 채워서 테스트 중에 사용할 수 있도록해야합니다.
