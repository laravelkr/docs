# 라라벨 캐셔 패들

- [시작하기](#introduction)
- [캐셔 업그레이드](#upgrading-cashier)
- [설치하기](#installation)
- [환경설정](#configuration)
    - [청구 가능 모델](#billable-model)
    - [API Keys](#api-keys)
    - [Paddle JS](#paddle-js)
    - [통화 설정하기](#currency-configuration)
- [핵심 개념](#core-concepts)
    - [결제 링크](#pay-links)
    - [인라인 체크아웃](#inline-checkout)
    - [사용자 식별](#user-identification)
- [가격](#prices)
- [고객](#customers)
    - [고객 기본값](#customer-defaults)
- [구독](#subscriptions)
    - [구독 생성](#creating-subscriptions)
    - [구독 상태 확인](#checking-subscription-status)
    - [일회성 구독 청구](#subscription-single-charges)
    - [결제 정보 업데이트](#updating-payment-information)
    - [플랜 변경](#changing-plans)
    - [구독 수량](#subscription-quantity)
    - [구독 일시 중지](#pausing-subscriptions)
    - [구독 취소](#cancelling-subscriptions)
- [구독 평가판](#subscription-trials)
    - [결제수단 등록 후 시작하기](#with-payment-method-up-front)
    - [결제수단 등록없이 시작하기](#without-payment-method-up-front)
- [패들 웹훅 처리](#handling-paddle-webhooks)
    - [Webhook 이벤트 핸들러 정의](#defining-webhook-event-handlers)
    - [실패한 구독](#handling-failed-subscriptions)
    - [Webhook 서명 확인](#verifying-webhook-signatures)
- [Single Charges](#single-charges)
    - [Simple Charge](#simple-charge)
    - [Charging Products](#charging-products)
    - [Refunding Orders](#refunding-orders)
- [Receipts](#receipts)
    - [Past & Upcoming Payments](#past-and-upcoming-payments)
- [Handling Failed Payments](#handling-failed-payments)
- [Testing](#testing)

<a name="introduction"></a>
## 시작하기

라라벨 캐셔 패들은 [Paddle](https://paddle.com) 구독 결제 서비스에 대한 손쉽고 편리한 인터페이스를 제공합니다. 패들은 당신이 두려워하는 거의 모든 상용 구독 청구 코드를 처리합니다. 기본 구독 관리 외에도 캐셔는 쿠폰, 구독 교환, 구독 "수량", 취소 유예 기간 등을 처리 할 수 있습니다.

캐셔로 작업하는 동안 Paddle의 [사용자 가이드](https://developer.paddle.com/guides) 및 [API 문서](https://developer.paddle.com/api-reference/intro)도 참조하는 것이 좋습니다.

<a name="upgrading-cashier"></a>
## 캐셔 업그레이드

캐셔를 새 버전으로 업그레이드 할 때는 [업그레이드 가이드](https://github.com/laravel/cashier-paddle/blob/master/UPGRADE.md)를 자세히 검토하세요.

<a name="installation"></a>
## 설치하기

먼저 Composer의 패들 용 캐셔 패키지가 필요합니다.

    composer require laravel/cashier-paddle

> {note} 캐셔가 모든 Paddle 이벤트를 올바르게 처리하도록하려면 [캐셔의 웹훅 처리 설정](#handling-paddle-webhooks)을 기억하세요.

#### Database Migrations

캐셔 서비스 프로바이더는 자체적으로 가지고 있는 데이터베이스 마이그레이션 디렉토리를 등록하므로 패키지를 설치 한 후 데이터베이스를 마이그레이션해야합니다. 캐셔 마이그레이션은 새로운 `customers`테이블을 생성합니다. 또한 고객의 모든 구독을 저장하기 위해 새로운 `subscriptions`테이블이 생성됩니다. 마지막으로 모든 영수증 정보를 저장하기 위해 새로운 `receipts` 테이블이 생성됩니다.

    php artisan migrate

캐셔 패키지와 함께 제공되는 마이그레이션을 수정해야하는 경우 Artisan 명령 `vendor:publish`를 사용하여 파일을 생성 할 수 있습니다.

    php artisan vendor:publish --tag="cashier-migrations"

캐셔의 마이그레이션이 실행되는 것을 완전히 방지하려면 캐셔가 제공하는 `ignoreMigrations`를 사용할 수 있습니다. 일반적으로 이 메서드는 `AppServiceProvider`의 `register` 메서드에서 호출해야합니다.

    use Laravel\Paddle\Cashier;

    Cashier::ignoreMigrations();

<a name="configuration"></a>
## 환경설정

<a name="billable-model"></a>
### 청구 가능 모델

캐셔를 사용하기 전에 유저 모델에 `Billable` trait를 추가해야합니다. 이 trait는 구독 생성, 쿠폰 적용 및 결제 방법 정보 업데이트와 같은 일반적인 결제 작업을 수행 할 수있는 다양한 메서드를 제공합니다.

    use Laravel\Paddle\Billable;

    class User extends Authenticatable
    {
        use Billable;
    }

유저가 아닌 청구 가능한 엔티티가 있는 경우 해당 클래스에 trait를 추가 할 수도 있습니다.

    use Laravel\Paddle\Billable;

    class Team extends Model
    {
        use Billable;
    }

<a name="api-keys"></a>
### API Keys

다음으로 `.env` 파일에서 패들의 키를 구성해야합니다. Paddle 제어판에서 Paddle API 키를 찾을 수 있습니다.

    PADDLE_VENDOR_ID=your-paddle-vendor-id
    PADDLE_VENDOR_AUTH_CODE=your-paddle-vendor-auth-code
    PADDLE_PUBLIC_KEY="your-paddle-public-key"

<a name="paddle-js"></a>
### Paddle JS

Paddle은 자체 JavaScript 라이브러리를 사용하여 Paddle 체크 아웃 위젯을 생성합니다. 애플리케이션 레이아웃의 닫는 `</ head>`태그 바로 앞에 `@paddleJS` 지시문을 배치하여 JavaScript 라이브러리를 로드 할 수 있습니다.

    <head>
        ...

        @paddleJS
    </head>

<a name="currency-configuration"></a>
### 통화 설정하기

캐셔의 기본 통화는 미국 달러(USD)입니다. `CASHIER_CURRENCY` 환경 변수를 설정하여 기본 통화를 변경할 수 있습니다.

    CASHIER_CURRENCY=EUR

캐셔의 통화를 설정하는 것 외에도 송장에 표시 할 화폐 값을 형식화 할 때 사용할 로케일을 지정할 수도 있습니다. 내부적으로 Cashier는 [PHP의 `NumberFormatter` 클래스](https://www.php.net/manual/en/class.numberformatter.php)를 사용하여 통화 로케일을 설정합니다.

    CASHIER_CURRENCY_LOCALE=nl_BE

> {note} `en` 이외의 로케일을 사용하려면 `ext-intl` PHP 익스텐션이 서버에 설치 및 설정되어 있는지 확인하십시오.

<a name="core-concepts"></a>
## Core Concepts

<a name="pay-links"></a>
### 결제 링크

Paddle에는 상태 변경을 수행하기위한 광범위한 CRUD API가 없습니다. 따라서 Paddle과의 대부분의 상호 작용은 [결제 위젯](https://developer.paddle.com/guides/how-tos/checkout/paddle-checkout)을 통해 이루어집니다. 결제 위젯을 표시하기 전에 캐셔을 사용하여 "결제 링크"를 생성합니다.

    $user = User::find(1);

    $payLink = $user->newSubscription('default', $premium = 34567)
        ->returnTo(route('home'))
        ->create();

    return view('billing', ['payLink' => $payLink]);

캐셔에는 `paddle-button`블레이드 컴포넌트가 포함되어 있습니다. 결제링크 URL을 이 컴포넌트에 "prop"으로 전달할 수 있습니다. 이 버튼을 클릭하면 Paddle의 결제 위젯이 표시됩니다.

    <x-paddle-button :url="$payLink" class="px-8 py-4">
        Subscribe
    </x-paddle-button>

기본적으로 표준 패들 스타일이 적용된 버튼이 표시됩니다. 컴포넌트에 `data-theme="none"` 속성을 추가하여 모든 패들 스타일을 제거 할 수 있습니다.

    <x-paddle-button :url="$payLink" class="px-8 py-4" data-theme="none">
        Subscribe
    </x-paddle-button>

패들 체크 아웃 위젯은 비동기식입니다. 사용자가 위젯 내에서 구독을 생성하거나 업데이트하면 Paddle은 자체 데이터베이스에서 구독 상태를 적절하게 업데이트 할 수 있도록 애플리케이션 웹훅을 보냅니다. 따라서 Paddle의 상태 변경 요청을 받아서 처리 할 수 있도록 [웹훅 설정](#handling-paddle-webhooks)을 적절하게 설정하는 것이 중요합니다.

구독 상태가 변경된 후 해당 웹훅 수신의 지연은 일반적으로 최소화하지만 사용자의 구독이 결제 완료 후 즉시 사용 가능하지 않을 수 있다는 점을 애플리케이션에서 고려해야합니다.

자세한 내용은 [결제 링크 생성에 대한 Paddle API 문서](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink)를 참고 할 수 있습니다.

<a name="inline-checkout"></a>
### 인라인 체크아웃

"오버레이"스타일의 체크아웃 위젯을 사용하지 않으려는 경우 Paddle에는 위젯을 인라인으로 표시하는 옵션도 있습니다. 이 접근 방식으로 체크 아웃의 HTML 필드를 조정할 수는 없지만 애플리케이션 내에 위젯을 포함 할 수 있습니다.

인라인 체크아웃을 쉽게 시작할 수 있도록 캐셔에는 `paddle-checkout` 블레이드 컴포넌트가 포함되어 있습니다. 시작하려면 [결제 링크를 생성](#pay-links)하고 결제 링크를 컴포넌트의 `override` 속성에 전달하면 됩니다.

    <x-paddle-checkout :override="$payLink" class="w-full" />

인라인 결제 컴포넌트의 높이를 조정하려면 블레이드 컴포넌트에 `height` 속성을 전달하면 됩니다.

    <x-paddle-checkout :override="$payLink" class="w-full" height="500" />

#### 결제링크가 없는 인라인 체크아웃

또는 결제 링크를 사용하는 대신 사용자 지정 옵션으로 위젯을 커스터마이징할 수 있습니다.

    $options = [
        'product' => $productId,
        'title' => 'Product Title',
    ];

    <x-paddle-checkout :options="$options" class="w-full" />

패들에서 사용 가능한 옵션에 대한 자세한 내용은 [Inline Checkout 가이드](https://developer.paddle.com/guides/how-tos/checkout/inline-checkout) 및 해당 [Parameter Reference](https://developer.paddle.com)를 참조하세요.

> {note} 커스텀 옵션을 지정할 때 `passthrough` 옵션도 사용하려면 캐셔가 배열을 JSON 문자열로 자동으로 변환하므로 키 / 값 배열을 제공해야합니다. 또한 `customer_id` 패스 스루 옵션은 내부 캐셔 사용을 위해 예약되어 있습니다.

<a name="user-identification"></a>
### 사용자 식별

Stripe와 달리 Paddle 사용자는 Paddle 계정별로 고유하지 않고 Paddle 전체에서 고유합니다. 이 때문에 Paddle의 API는 현재 이메일 주소와 같은 사용자 세부 정보를 업데이트하는 방법을 제공하지 않습니다. 결제 링크를 생성 할 때 Paddle은 `customer_email` 매개 변수를 사용하여 사용자를 식별합니다. 구독을 만들 때 Paddle은 사용자가 제공 한 이메일을 기존 Paddle 사용자와 일치 시키려고합니다.

이 동작에 비추어 캐셔과 패들을 사용할 때 유의해야 할 몇 가지 중요한 사항이 있습니다. 첫째, 캐셔의 구독이 동일한 애플리케이션 사용자에게 연결되어 있더라도 **Paddle의 내부 시스템 내에서 다른 사용자에게 연결될 수 있음** 을 알아야합니다. 둘째, 각 구독에는 자체 연결된 결제 방법 정보가 있으며 Paddle의 내부 시스템 내에서 다른 이메일 주소를 가질 수도 있습니다 (구독을 만들 때 사용자에게 할당 된 이메일에 따라 다름).

따라서 구독을 표시 할 때는 구독별로 구독에 연결된 이메일 주소 또는 결제 방법 정보를 항상 사용자에게 알려야합니다. 이 정보 검색은 `Subscription` 모델에서 다음 메서드를 통해 실행 할 수 있습니다.

    $subscription = $user->subscription('default');

    $customerEmailAddress = $subscription->paddleEmail();
    $paymentMethod = $subscription->paymentMethod();
    $cardBrand = $subscription->cardBrand();
    $cardLastFour = $subscription->cardLastFour();
    $cardExpirationDate = $subscription->cardExpirationDate();

현재는 Paddle API를 통해 사용자의 이메일 주소를 수정 할 수있는 방법이 없습니다. 사용자가 Paddle 내에서 자신의 이메일 주소를 업데이트하려는 경우, 그렇게 할 수있는 유일한 방법은 Paddle 고객 지원에 문의하는 것입니다. Paddle과 통신 할 때 Paddle이 올바른 사용자를 업데이트하는 데 도움이 되도록 구독의 `paddleEmail`값을 제공해야합니다.

<a name="prices"></a>
## 가격

Paddle을 사용하면 통화별로 가격을 커스터마이징 할 수 있으므로 기본적으로 국가마다 다른 가격을 설정 할 수 있습니다. Cashier Paddle을 사용하면 `productPrices` 메소드를 사용하여 특정 제품의 모든 가격을 검색 할 수 있습니다.

    use Laravel\Paddle\Cashier;

    // Retrieve prices for two products...
    $prices = Cashier::productPrices([123, 456]);

통화는 요청의 IP 주소에 따라 결정됩니다. 그러나 필요하면 특정 국가를 지정하여 가격을 조회 할 수 있습니다.

    use Laravel\Paddle\Cashier;

    // Retrieve prices for two products...
    $prices = Cashier::productPrices([123, 456], ['customer_country' => 'BE']);

가격을 조회 한 후 원하는대로 표시 할 수 있습니다.

    <ul>
        @foreach ($prices as $price)
            <li>{{ $price->product_title }} - {{ $price->price()->gross() }}</li>
        @endforeach
    </ul>

정가 (세금 제외)를 표시하고 세액을 별도로 표시 할 수도 있습니다.

    <ul>
        @foreach ($prices as $price)
            <li>{{ $price->product_title }} - {{ $price->price()->net() }} (+ {{ $price->price()->tax() }} tax)</li>
        @endforeach
    </ul>

구독 플랜의 가격을 조회 한 경우 최초 및 연장결제의 가격을 별도로 표시 할 수 있습니다.

    <ul>
        @foreach ($prices as $price)
            <li>{{ $price->product_title }} - Initial: {{ $price->initialPrice()->gross() }} - Recurring: {{ $price->recurringPrice()->gross() }}</li>
        @endforeach
    </ul>

자세한 내용은 [가격에 대한 Paddle의 API 문서](https://developer.paddle.com/api-reference/checkout-api/prices/getprices)를 참고하세요.

#### 고객

사용자가 이미 고객이고 해당 고객에게 적용되는 가격을 표시하려는 경우 고객 인스턴스에서 직접 가격을 조회 할 수 있습니다.

    use App\User;

    // Retrieve prices for two products...
    $prices = User::find(1)->productPrices([123, 456]);

내부적으로 캐셔는 사용자의 [`paddleCountry` 메소드](#customer-defaults)를 사용하여 통화로 가격을 조회합니다. 예를 들어 미국에 거주하는 사용자는 USD로 가격이 표시되고 벨기에 사용자는 EUR로 가격이 표시됩니다. 일치하는 통화를 찾을 수 없는 경우 제품의 기본 통화가 사용됩니다. 패들 제어판에서 제품 또는 구독 플랜의 모든 가격을 커스터마이징 할 수 있습니다.

#### 쿠폰

쿠폰 할인 후 가격을 표시하도록 선택할 수도 있습니다. `productPrices` 메소드를 호출 할 때 쿠폰이 쉼표로 구분 된 문자열로 전달 될 수 있습니다.


    use Laravel\Paddle\Cashier;

    $prices = Cashier::productPrices([123, 456], ['coupons' => 'SUMMERSALE,20PERCENTOFF']);

그런 다음 `price` 메서드를 사용하여 계산 된 가격을 표시합니다.

    <ul>
        @foreach ($prices as $price)
            <li>{{ $price->product_title }} - {{ $price->price()->gross() }}</li>
        @endforeach
    </ul>

`listPrice`메소드를 사용하여 원래 나열된 가격 (쿠폰 할인 제외)을 표시 할 수 있습니다.

    <ul>
        @foreach ($prices as $price)
            <li>{{ $price->product_title }} - {{ $price->listPrice()->gross() }}</li>
        @endforeach
    </ul>

> {note} 가격 API를 사용할 때 Paddle은 일회성 구매 제품에만 쿠폰을 적용 할 수 있으며 구독 플랜에는 적용 할 수 없습니다.

<a name="customers"></a>
## 고객

<a name="customer-defaults"></a>
### 고객 기본값

캐셔을 사용하면 결제 링크를 만들 때 고객에게 유용한 기본값을 설정할 수 있습니다. 이러한 기본값을 설정하면 고객이 결제 위젯의 결제 부분으로 즉시 이동할 수 있도록 고객의 이메일 주소, 국가 및 우편 번호를 미리 채워 둘 수 있습니다. 청구 가능한 사용자에 대해 다음 메서드를 재정의하여 이러한 기본값을 설정할 수 있습니다.

    /**
     * Get the customer's email address to associate with Paddle.
     *
     * @return string|null
     */
    public function paddleEmail()
    {
        return $this->email;
    }

    /**
     * Get the customer's country to associate with Paddle.
     *
     * This needs to be a 2 letter code. See the link below for supported countries.
     *
     * @return string|null
     * @link https://developer.paddle.com/reference/platform-parameters/supported-countries
     */
    public function paddleCountry()
    {
        //
    }

    /**
     * Get the customer's postcode to associate with Paddle.
     *
     * See the link below for countries which require this.
     *
     * @return string|null
     * @link https://developer.paddle.com/reference/platform-parameters/supported-countries#countries-requiring-postcode
     */
    public function paddlePostcode()
    {
        //
    }

이러한 기본값은 [결제 링크](#pay-links)를 생성하는 캐셔의 모든 작업에 사용됩니다.

<a name="subscriptions"></a>
## 구독

<a name="creating-subscriptions"></a>
### 구독 생성

구독을 생성하려면 먼저 청구 가능한 모델의 인스턴스를 조회하세요. 일반적으로 `App\User`의 인스턴스가 됩니다. 모델 인스턴스를 조회 한 후에는 `newSubscription` 메서드를 사용하여 모델의 구독 결제 링크를 만들 수 있습니다.

    $user = User::find(1);

    $payLink = $user->newSubscription('default', $premium = 12345)
        ->returnTo(route('home'))
        ->create();

    return view('billing', ['payLink' => $payLink]);

`newSubscription` 메소드에 전달 된 첫 번째 인수는 구독의 이름이어야합니다. 애플리케이션이 단일 구독 만 제공하는 경우이를 `default` 또는 `primary`라고 부를 수 있습니다. 두 번째 인수는 사용자가 구독하는 특정 플랜입니다. 이 값은 Paddle의 플랜 식별자와 일치해야합니다. `returnTo` 메소드는 사용자가 체크 아웃을 성공적으로 완료 한 후 리디렉션되는 URL을 입력받습니다.

`create`메서드는 결제 버튼을 생성하는 데 사용할 수있는 결제 링크를 생성합니다. 결제 버튼은 캐셔 패들과 함께 제공되는 `paddle-button` 블레이드 컴포넌트를 사용하여 생성 할 수 있습니다.

    <x-paddle-button :url="$payLink" class="px-8 py-4">
        Subscribe
    </x-paddle-button>

사용자가 결제를 완료하면 `subscription_created` 웹훅이 Paddle에서 발송됩니다. 캐셔는 이 웹훅을 받고 고객의 구독을 설정합니다. 모든 웹훅이 애플리케이션에서 제대로 수신되고 처리되는지 확인하려면 [웹훅 처리 설정](#handling-paddle-webhooks)이 올바르게 설정되어 있는지 확인하세요.

#### 추가적인 세부사항

추가적인 고객 또는 구독의 세부 사항를 지정하려면 키 / 값 배열로 `create` 메소드에 전달하면됩니다.

    $payLink = $user->newSubscription('default', $monthly = 12345)
        ->returnTo(route('home'))
        ->create([
            'vat_number' => $vatNumber,
        ]);

Paddle에서 지원하는 추가 필드에 대해 자세히 알아 보려면 [결제 링크 생성](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink)에 대한 Paddle의 문서를 확인하세요.

#### 쿠폰

구독을 생성 할 때 쿠폰을 적용하려면 `withCoupon` 메소드를 사용할 수 있습니다.

    $payLink = $user->newSubscription('default', $monthly = 12345)
        ->returnTo(route('home'))
        ->withCoupon('code')
        ->create();

#### Metadata

`withMetadata` 메소드를 사용하여 메타 데이터 배열을 전달할 수도 있습니다.

    $payLink = $user->newSubscription('default', $monthly = 12345)
        ->returnTo(route('home'))
        ->withMetadata(['key' => 'value'])
        ->create();

> {note} 메타 데이터 제공시 `subscription_name`을 메타 데이터 키로 사용하지 마세요. 이 키는 캐셔 내부 용으로 예약되어 있습니다.

<a name="checking-subscription-status"></a>
### 구독 상태 확인

사용자가 귀하의 애플리케이션을 구독하면 다양하고 편리한 메서드들을 사용하여 구독 상태를 확인할 수 있습니다. 먼저 구독이 현재 평가 기간 내에 있더라도 사용자에게 활성 구독이 있는 경우 `subscribed` 메소드는 `true`를 반환합니다.

    if ($user->subscribed('default')) {
        //
    }

`subscribed` 메서드는 또한 [route middleware](/docs/{{version}}/middleware)에 대한 훌륭한 보조도구가 되어 사용자의 구독 상태에 따라 라우트 및 컨트롤러에 대한 접근을 필터링 할 수 있습니다.

    public function handle($request, Closure $next)
    {
        if ($request->user() && ! $request->user()->subscribed('default')) {
            // This user is not a paying customer...
            return redirect('billing');
        }

        return $next($request);
    }

사용자가 아직 평가 기간 내에 있는지 확인하려면 `onTrial`메서드를 사용할 수 있습니다. 이 메서드는 사용자에게 아직 평가판 사용 중이라는 경고를 표시하는 데 유용 할 수 있습니다.

    if ($user->subscription('default')->onTrial()) {
        //
    }

`subscribedToPlan` 메소드는 주어진 Paddle 계획 ID를 기반으로 사용자가 주어진 계획에 가입했는지 여부를 결정하는 데 사용될 수 있습니다. 이 예제에서는 사용자의 `default` 구독이 월간 요금제에 적극적으로 구독하고 있는지 확인합니다.

    if ($user->subscribedToPlan($monthly = 12345, 'default')) {
        //
    }

배열을 `subscribedToPlan` 메소드에 전달하면 사용자의 `default` 구독이 월간 또는 연간 요금제를 적극적으로 구독하고 있는지 확인할 수 있습니다.

    if ($user->subscribedToPlan([$monthly = 12345, $yearly = 54321], 'default')) {
        //
    }

`recurring`메서드를 사용하여 사용자가 현재 구독하고 있으며 더 이상 평가 기간 내에 있지 않은지 확인할 수 있습니다.

    if ($user->subscription('default')->recurring()) {
        //
    }

#### 취소된 구독상태

사용자가 한때 활성 구독자 였지만 구독을 취소했는지 확인하려면 `cancelled` 메소드를 사용할 수 있습니다.

    if ($user->subscription('default')->cancelled()) {
        //
    }

또한 사용자가 구독을 취소했지만 구독이 완전히 만료 될 때까지의 "유예 기간"에 남아 있는지 확인할 수도 있습니다. 예를 들어 사용자가 원래 3 월 10 일에 만료 될 예정이었던 3 월 5 일에 구독을 취소하면 사용자는 3 월 10 일까지 "유예 기간"중에 있습니다. 이 기간 동안 `subscribed` 메소드는 여전히 `true`를 반환합니다.

    if ($user->subscription('default')->onGracePeriod()) {
        //
    }

사용자가 구독을 취소했고 더 이상 "유예 기간"에 있지 않은지 확인하려면 `ended`메소드를 사용할 수 있습니다.

    if ($user->subscription('default')->ended()) {
        //
    }

#### 구독 스코프

대부분의 구독 상태는 쿼리 스코프로도 사용할 수 있으므로 지정한 상태의 구독을 데이터베이스에서 쉽게 조회 할 수 있습니다.

    // Get all active subscriptions...
    $subscriptions = Subscription::query()->active()->get();

    // Get all of the cancelled subscriptions for a user...
    $subscriptions = $user->subscriptions()->cancelled()->get();

사용 가능한 스코프의 전체 목록은 아래에서 확인할 수 있습니다.

    Subscription::query()->active();
    Subscription::query()->onTrial();
    Subscription::query()->notOnTrial();
    Subscription::query()->pastDue();
    Subscription::query()->recurring();
    Subscription::query()->ended();
    Subscription::query()->paused();
    Subscription::query()->notPaused();
    Subscription::query()->onPausedGracePeriod();
    Subscription::query()->notOnPausedGracePeriod();
    Subscription::query()->cancelled();
    Subscription::query()->notCancelled();
    Subscription::query()->onGracePeriod();
    Subscription::query()->notOnGracePeriod();

#### 연체 상태

구독 결제에 실패하면 `past_due`로 표시됩니다. 구독이 이 상태이면 고객이 결제 정보를 업데이트 할 때까지 활성화되지 않습니다. 구독 인스턴스에서 `pastDue` 메소드를 사용하여 구독 기한이 지난 지 확인할 수 있습니다.

    if ($user->subscription('default')->pastDue()) {
        //
    }

구독 기한이 지난 경우 사용자에게 [결제 정보를 업데이트](#updating-payment-information)하도록 안내해야합니다. [패들 구독 설정](https://vendors.paddle.com/subscription-settings)에서 기한이 지난 구독을 처리하는 방법을 설정 할 수 있습니다.

구독이 `past_due` 인 경우에도 활성 상태로 간주되도록하려면 캐셔에서 제공하는 `keepPastDueSubscriptionsActive` 메서드를 사용할 수 있습니다. 일반적으로 이 메서드는 `AppServiceProvider`의 `register` 메서드에서 호출해야합니다.

    use Laravel\Paddle\Cashier;

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        Cashier::keepPastDueSubscriptionsActive();
    }

> {note} 구독이 `past_due` 상태 인 경우 결제 정보가 업데이트 될 때까지 변경할 수 없습니다. 따라서 구독이 `past_due`상태 일 때 `swap` 및 `updateQuantity` 메소드는 예외를 발생시킵니다.

<a name="subscription-single-charges"></a>
### 일회성 구독 청구

일회성 구독 요금을 사용하면 구독에 추가로 일회성 요금을 구독자에게 청구 할 수 있습니다.

    $response = $user->subscription('default')->charge(12.99, 'Support Add-on');

[일회성 청구](#single-charges)와 달리 이 방법은 구독에 대해 고객의 저장된 결제 수단으로 즉시 청구됩니다. 청구 금액은 항상 구독이 현재 설정된 통화로 표시됩니다.

<a name="updating-payment-information"></a>
### 결제 정보 업데이트

Paddle은 항상 구독 마다 결제 방법을 저장합니다. 구독에 대한 기본 결제 수단을 업데이트하려면 먼저 구독 모델에서 `updateUrl`메서드를 사용하여 구독의 '업데이트용 URL'을 생성해야합니다.

    $user = App\User::find(1);

    $updateUrl = $user->subscription('default')->updateUrl();

그런 다음 생성 된 URL을 캐셔가 제공하는 `paddle-button` 블레이드 컴포넌트와 함께 사용하여 사용자가 패들 위젯을 시작하고 결제 정보를 업데이트 할 수 있습니다.

    <x-paddle-button :url="$updateUrl" class="px-8 py-4">
        Update Card
    </x-paddle-button>

사용자가 정보 업데이트를 완료하면 `subscription_updated` 웹훅이 Paddle에 의해 전달되고 구독 세부 정보가 애플리케이션의 데이터베이스에서 업데이트됩니다.

<a name="changing-plans"></a>
### 플랜 변경

사용자가 애플리케이션을 구독 한 후 때때로 새 구독 플랜으로 변경하고 싶을 수 있습니다. 사용자를 새 구독으로 바꾸려면 Paddle 요금제의 식별자를 구독의 `swap` 메서드에 전달해야합니다.

    $user = App\User::find(1);

    $user->subscription('default')->swap($premium = 34567);

사용자가 평가판을 사용중인 경우 평가 기간이 유지됩니다. 또한 구독에 대한 "수량"이있는 경우 해당 수량도 유지됩니다.

요금제를 바꾸고 사용자가 현재 사용중인 평가판 기간을 취소하려면 `skipTrial` 메서드를 사용할 수 있습니다.

    $user->subscription('default')
            ->skipTrial()
            ->swap($premium = 34567);

다음 결제주기를 기다리지 않고 요금제를 바꾸고 즉시 사용자에게 인보이스를 보내려면 `swapAndInvoice` 메서드를 사용할 수 있습니다.

    $user = App\User::find(1);

    $user->subscription('default')->swapAndInvoice($premium = 34567);

#### 비례 배분하기

기본적으로 Paddle은 요금제간에 전환 할 때 요금을 비례 배분합니다. `noProrate`메서드를 사용하면 요금을 일할 계산하지 않고 구독을 업데이트 할 수 있습니다.

    $user->subscription('default')->noProrate()->swap($premium = 34567);

<a name="subscription-quantity"></a>
### 구독 수량

때때로 구독은 "수량"의 영향을받습니다. 예를 들어 애플리케이션에서 계정 당 **사용자 당** 월 10 달러를 청구 할 수 있습니다. 구독 수량을 쉽게 늘리거나 줄이려면 `incrementQuantity` 및 `decrementQuantity` 메소드를 사용하십시오.

    $user = User::find(1);

    $user->subscription('default')->incrementQuantity();

    // Add five to the subscription's current quantity...
    $user->subscription('default')->incrementQuantity(5);

    $user->subscription('default')->decrementQuantity();

    // Subtract five to the subscription's current quantity...
    $user->subscription('default')->decrementQuantity(5);

또는 `updateQuantity` 메소드를 사용하여 특정 수량을 설정할 수 있습니다.

    $user->subscription('default')->updateQuantity(10);

`noProrate`메소드를 사용하여 요금을 일할 계산하지 않고 구독 수량을 업데이트 할 수 있습니다.

    $user->subscription('default')->noProrate()->updateQuantity(10);

<a name="pausing-subscriptions"></a>
### 구독 일시 중지

구독을 일시 중지하려면 사용자의 구독에서 `pause` 메소드를 호출합니다.

    $user->subscription('default')->pause();

구독이 일시 중지되면 캐셔는 자동으로 데이터베이스에 `paused_from` 컬럼을 설정합니다. 이 컬럼은 `paused`메소드가 `true`를 반환하기 시작해야하는 시기를 확인하는 데 사용됩니다. 예를 들어 고객이 3월 1일에 구독을 일시 중지했지만 구독이 3월 5일까지 반복되지 않도록 예약 된 경우 `paused`메서드는 3월 5일까지 계속 `false`를 반환합니다.

`onPausedGracePeriod`메소드를 사용하여 사용자가 구독을 일시 중지했지만 여전히 "유예 기간"에 있는지 확인할 수 있습니다.

    if ($user->subscription('default')->onPausedGracePeriod()) {
        //
    }

일시 중지 된 구독을 재개하려면 사용자의 구독에 대해 `unpause` 메소드를 호출 할 수 있습니다.

    $user->subscription('default')->unpause();

> {note} 일시 중지 된 구독은 수정할 수 없습니다. 다른 플랜으로 교체하거나 수량을 업데이트하려면 먼저 구독을 재개해야합니다.

<a name="cancelling-subscriptions"></a>
### 구독 취소

구독을 취소하려면 사용자의 구독에서 `cancel` 메서드를 호출하세요.

    $user->subscription('default')->cancel();

구독이 취소되면 캐셔는 자동으로 데이터베이스에 `ends_at` 컬럼을 설정합니다. 이 컬럼은 `subscribed`메소드가 `false`를 반환하기 시작해야하는 시기를 확인하는 데 사용됩니다. 예를 들어 고객이 3월 1일에 구독을 취소했지만 구독이 3월 5일까지 종료되도록 예약되지 않은 경우 `subscribed`메서드는 3월 5일까지 계속 `true`를 반환합니다.

`onGracePeriod` 메소드를 사용하여 사용자가 구독을 취소했지만 여전히 '유예 기간'에 있는지 확인할 수 있습니다.

    if ($user->subscription('default')->onGracePeriod()) {
        //
    }

구독을 즉시 취소하려면 사용자의 구독에서 `cancelNow` 메서드를 호출 할 수 있습니다.

    $user->subscription('default')->cancelNow();

> {note} 취소 후에는 Paddle의 구독을 재개 할 수 없습니다. 고객이 구독을 재개하려면 새 구독을 구독해야합니다.

<a name="subscription-trials"></a>
## 구독 평가판

<a name="with-payment-method-up-front"></a>
### 결제수단 등록 후 시작하기

> {note} 결제 수단 세부 정보를 미리 평가하고 수집하는 동안 Paddle은 요금제 교환이나 수량 업데이트와 같은 구독의 변경을 방지합니다. 평가판 중에 고객이 요금제를 교체 할 수 있도록 하려면 구독을 취소하고 다시 만들어야합니다.

결제 수단 정보를 미리 수집하면서 고객에게 평가판 기간을 제공하려면 구독 결제 링크를 만들 때 `trialDays`메서드를 사용해야합니다.

    $user = User::find(1);

    $payLink = $user->newSubscription('default', $monthly = 12345)
                ->returnTo(route('home'))
                ->trialDays(10)
                ->create();

    return view('billing', ['payLink' => $payLink]);

이 방법은 데이터베이스 내의 구독 기록에 평가 기간 종료 날짜를 설정하고 이 날짜 이후까지 고객에게 청구를 시작하지 않도록 Paddle에 지시합니다.

> {note} 평가판 종료일 이전에 고객의 구독을 취소하지 않으면 평가판이 만료되는 즉시 요금이 청구되므로 사용자에게 평가판 종료일을 알려야합니다.

사용자 인스턴스의 `onTrial`메서드 또는 구독 인스턴스의 `onTrial`메서드를 사용하여 사용자가 평가 기간 내에 있는지 확인할 수 있습니다. 아래 두 가지 예는 동일한 동작을 합니다.

    if ($user->onTrial('default')) {
        //
    }

    if ($user->subscription('default')->onTrial()) {
        //
    }

#### 패들 / 캐셔에서 평가기간 정의

Paddle 대시 보드에서 플랜의 평가 기간을 정의하거나 항상 캐셔을 사용하여 명시적으로 전달할 수 있습니다. Paddle에서 플랜의 평가 기간을 정의하기로 선택한 경우 과거에 구독 한 고객에 대한 새 구독을 포함하여 새로운 구독은 명시 적으로 `trialDays(0)`를 호출하지 않는 한 항상 평가 기간을 받게됩니다.

<a name="without-payment-method-up-front"></a>
### 결제수단 등록없이 시작하기

사용자의 결제 수단 정보를 미리 수집하지 않고 평가 기간을 제공하려면 사용자에게 첨부 된 고객 기록의 `trial_ends_at` 컬럼을 원하는 평가판 종료 날짜로 설정할 수 있습니다. 이는 일반적으로 사용자 등록 중에 수행됩니다.

    $user = User::create([
        // Other user properties...
    ]);

    $user->createAsCustomer([
        'trial_ends_at' => now()->addDays(10)
    ]);

캐셔는 이러한 유형의 평가판이 기존 구독에 연결되어 있지 않기 때문에 "일반 평가판"이라고합니다. `User` 인스턴스의 `onTrial` 메소드는 현재 날짜가 `trial_ends_at` 값을 지나지 않았다면 `true`를 반환합니다.

    if ($user->onTrial()) {
        // User is within their trial period...
    }

사용자가 "일반"평가 기간 내에 있고 아직 실제 구독을 생성하지 않았음을 구체적으로 알고 싶다면 `onGenericTrial`메서드를 사용할 수도 있습니다.

    if ($user->onGenericTrial()) {
        // User is within their "generic" trial period...
    }

사용자를 위한 실제 구독을 만들 준비가 되면 평소와 같이 `newSubscription` 메서드를 사용할 수 있습니다.

    $user = User::find(1);

    $payLink = $user->newSubscription('default', $monthly = 12345)
        ->returnTo(route('home'))
        ->create();

> {note} Paddle 구독이 생성 된 후에는 평가 기간을 연장하거나 수정할 수 없습니다.

<a name="handling-paddle-webhooks"></a>
## 패들 웹훅 처리

> {tip} [Valet의 `share` 명령어](https://laravel.com/docs/{{version}}/valet#sharing-sites)를 사용하여 로컬 개발 중에 웹훅을 테스트 할 수 있습니다.

Paddle은 웹훅을 통해 다양한 이벤트를 애플리케이션에 알릴 수 있습니다. 기본적으로 캐셔의 웹훅 컨트롤러를 가리키는 경로는 캐셔 서비스 프로바이더를 통해 구성됩니다. 이 컨트롤러는 들어오는 모든 웹훅 요청을 처리합니다.

기본적으로 이 컨트롤러는 결제 실패가 너무 많은 구독을 취소 ([Paddle 구독 설정에 정의 됨](https://vendors.paddle.com/subscription-settings)), 구독 업데이트 및 결제 수단 변경을 자동으로 처리합니다. 그러나 곧 알게 되겠지만 이 컨트롤러를 확장하여 원하는 웹훅 이벤트를 처리 할 수 있습니다.

애플리케이션에서 Paddle 웹훅을 처리 할 수 있도록하려면 [Paddle 제어판에서 웹훅 URL 구성](https://vendors.paddle.com/alerts-webhooks)을 확인하세요. 기본적으로 Cashier의 웹훅 컨트롤러는 `/paddle/webhook`URL 라우트로 수신됩니다. Paddle 제어판에서 구성해야하는 모든 웹훅의 전체 목록은 다음과 같습니다.

- 구독 생성
- 구독 업데이트
- 구독 삭제
- 결제 성공
- 구독 결제 성공

> {note} 캐셔에 포함 된 [webhook 서명 확인](/docs/{{version}}/cashier-paddle#verifying-webhook-signatures) 미들웨어로 들어오는 요청을 보호하세요.

#### 웹훅 및 CSRF 보호

Paddle 웹훅은 Laravel의 [CSRF 보호](/docs/{{version}}/csrf)를 우회해야하므로 `VerifyCsrfToken` 미들웨어에 URI를 예외로 나열하거나 `web` 미들웨어 그룹의 외부에 라우트로 추가해야합니다.

    protected $except = [
        'paddle/*',
    ];

<a name="defining-webhook-event-handlers"></a>
### Webhook 이벤트 핸들러 정의

캐셔는 청구 실패시 구독 취소를 자동으로 처리하지만 처리하려는 추가 웹훅 이벤트가 있는 경우 `WebhookController`를 확장해야합니다. 메서드 이름은 캐셔의 예상 규칙과 일치해야합니다. 특히 메서드 앞에는 `handle` 과 처리하려는 웹훅의 "캐멀 케이스"이름이 접두사로 추가되어야합니다. 예를 들어 `payment_succeeded`웹훅을 처리하려면 컨트롤러에 `handlePaymentSucceeded`메소드를 추가해야합니다.

    <?php

    namespace App\Http\Controllers;

    use Laravel\Paddle\Http\Controllers\WebhookController as CashierController;

    class WebhookController extends CashierController
    {
        /**
         * Handle payment succeeded.
         *
         * @param  array  $payload
         * @return void
         */
        public function handlePaymentSucceeded($payload)
        {
            // Handle The Event
        }
    }

다음으로 `routes/web.php` 파일 내에서 캐셔 컨트롤러에 대한 라우트를 정의합니다. 캐셔에 포함 된 라우트를 덮어 씁니다.

    Route::post(
        'paddle/webhook',
        '\App\Http\Controllers\WebhookController'
    );

캐셔는 웹훅이 수신되면 `Laravel\Paddle\Events\WebhookReceived` 이벤트를, 웹훅이 처리되면 `Laravel\Paddle\Events\WebhookHandled` 이벤트를 내 보냅니다. 두 이벤트 모두 Paddle webhook의 전체 페이로드를 포함합니다.

캐셔는 또한 수신 된 웹훅 유형 전용 이벤트를 발생시킵니다. Paddle의 전체 페이로드 외에도 청구 가능 모델, 구독 또는 영수증과 같은 웹훅을 처리하는 데 사용 된 관련 모델도 포함됩니다.

- `PaymentSucceeded`
- `SubscriptionPaymentSucceeded`
- `SubscriptionCreated`
- `SubscriptionUpdated`
- `SubscriptionCancelled`

선택적으로 `.env` 파일에서 `CASHIER_WEBHOOK` env 변수를 설정하여 기본 내장 웹훅 경로를 재정의 할 수도 있습니다. 이 값은 웹훅 경로의 전체 URL이어야하며 Paddle 제어판에 설정된 URL과 일치해야합니다.

    CASHIER_WEBHOOK=https://example.com/my-paddle-webhook-url

<a name="handling-failed-subscriptions"></a>
### 실패한 구독

고객의 신용 카드가 만료되면 어떻게 될까요? 걱정하지 마세요. 캐셔의 Webhook 컨트롤러가 고객의 구독을 취소합니다. 실패한 지불은 컨트롤러에 의해 자동으로 캡처되고 처리됩니다. Paddle이 구독이 실패했다고 판단하면 컨트롤러는 고객의 구독을 취소합니다 (일반적으로 세 번의 결제 시도 실패 후에 처리됩니다).

<a name="verifying-webhook-signatures"></a>
### Webhook 서명 확인

웹훅을 보호하려면 [Paddle의 웹훅 서명](https://developer.paddle.com/webhook-reference/verifying-webhooks)을 사용할 수 있습니다. 편의를 위해 캐셔는 들어오는 Paddle webhook 요청이 유효한지 확인하는 미들웨어를 자동으로 포함합니다.

웹훅 확인을 사용하려면 `.env` 파일에 `PADDLE_PUBLIC_KEY` 환경 변수가 설정되어 있는지 확인하십시오. 공개 키는 Paddle 계정 대시 보드에서 검색 할 수 있습니다.

<a name="single-charges"></a>
## 일회성 청구

<a name="simple-charge"></a>
### 기본 청구

고객에 대해 "일회성"청구를 하려면 청구 가능 모델 인스턴스에서 `charge`메서드를 사용하여 요금에 대한 결제 링크를 생성 할 수 있습니다. `charge`메서드는 첫 번째 인수로 청구 금액 (float)을, 두 번째 인수로 결제 설명을 입력받습니다.

    $payLink = $user->charge(12.99, 'Product Title');

    return view('pay', ['payLink' => $payLink]);

결제 링크를 생성 한 후 캐셔가 제공하는 `paddle-button`블레이드 컴포넌트를 사용하여 사용자가 패들 위젯을 시작하고 청구를 완료 할 수 있습니다.

    <x-paddle-button :url="$payLink" class="px-8 py-4">
        Buy
    </x-paddle-button>

`charge` 메소드는 세 번째 인수로 배열을 허용하므로 원하는 모든 옵션을 기본 Paddle 결제 링크 생성에 전달할 수 있습니다. 청구시 사용할 수있는 옵션에 대한 자세한 내용은 [Paddle 문서](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink)를 참조하세요.

    $payLink = $user->charge(12.99, 'Product Title', [
        'custom_option' => $value,
    ]);

`cashier.currency` 설정 옵션에 지정된 통화로 청구됩니다. 기본적으로 이것은 USD로 설정됩니다. `.env` 파일에서 `CASHIER_CURRENCY`를 설정하여 기본 통화를 재정의 할 수 있습니다.

    CASHIER_CURRENCY=EUR

Paddle의 동적 가격 매칭 시스템을 사용하여 [통화 당 가격을 재정의](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink#price-overrides) 할 수도 있습니다. 이렇게하려면 고정 금액 대신 가격 배열을 전달합니다.

    $payLink = $user->charge([
        'USD:19.99',
        'EUR:15.99',
    ], 'Product Title');

<a name="charging-products"></a>
### 제품 결제

Paddle 내에 구성된 특정 제품에 대해 "일회성"청구를 하려면 청구 가능 모델 인스턴스에서 `chargeProduct`메소드를 사용하여 결제 링크를 생성 할 수 있습니다.

    $payLink = $user->chargeProduct($productId);

    return view('pay', ['payLink' => $payLink]);

그런 다음 사용자가 Paddle 위젯을 초기화 할 수 있도록 `paddle-button` 컴포넌트에 대한 결제 링크를 제공 할 수 있습니다.

    <x-paddle-button :url="$payLink" class="px-8 py-4">
        Buy
    </x-paddle-button>

`chargeProduct` 메소드는 두 번째 인수로 배열을 허용하므로 원하는 옵션을 기본 Paddle 결제 링크 생성에 전달할 수 있습니다. 청구를 생성 할 때 사용할 수있는 옵션에 대해서는 [Paddle 문서](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink)를 참조하세요.

    $payLink = $user->chargeProduct($productId, [
        'custom_option' => $value,
    ]);

<a name="refunding-orders"></a>
### 주문 환불

패들 주문을 환불해야하는 경우 `refund`메서드를 사용할 수 있습니다. 이 메서드는 패들 주문 ID를 첫 번째 인수로 받습니다. `receipts`메소드를 사용하여 특정 청구 가능 항목에 대한 영수증을 검색 할 수 있습니다.

    $receipt = $user->receipts()->first();

    $refundRequestId = $user->refund($receipt->order_id);

선택적으로 환불 할 특정 금액과 환불 사유를 지정할 수도 있습니다.

    $receipt = $user->receipts()->first();

    $refundRequestId = $user->refund(
        $receipt->order_id, 5.00, 'Unused product time'
    );

> {tip} Paddle 지원팀에 문의 할 때 `$refundRequestId`를 환불 참조로 사용할 수 있습니다.

<a name="receipts"></a>
## 영수증

`receipts` 메소드를 사용하여 청구 가능 모델의 영수증 배열을 쉽게 검색 할 수 있습니다.

    $receipts = $user->receipts();

고객에 대한 영수증을 나열 할 때 영수증의 헬퍼 메서드를 사용하여 관련 영수증 정보를 표시 할 수 있습니다. 예를 들어, 테이블에 모든 영수증을 나열하여 사용자가 영수증을 쉽게 다운로드 할 수 있도록 할 수 있습니다.

    <table>
        @foreach ($receipts as $receipt)
            <tr>
                <td>{{ $receipt->paid_at->toFormattedDateString() }}</td>
                <td>{{ $receipt->amount() }}</td>
                <td><a href="{{ $receipt->receipt_url }}" target="_blank">Download</a></td>
            </tr>
        @endforeach
    </table>

<a name="past-and-upcoming-payments"></a>
### 과거 및 향후 지급

`lastPayment` 및 `nextPayment` 메소드를 사용하여 반복 구독에 대한 고객의 과거 또는 향후 결제를 표시 할 수 있습니다.

    $subscription = $user->subscription('default');

    $lastPayment = $subscription->lastPayment();
    $nextPayment = $subscription->nextPayment();

이 두 방법 모두 `Laravel\Paddle\Payment` 의 인스턴스를 반환합니다. 그러나 `nextPayment` 는 결제주기가 종료되면 (예 : 구독이 취소 된 경우) `null`을 반환합니다.

    Next payment: {{ $nextPayment->amount() }} due on {{ $nextPayment->date()->format('d/m/Y') }}

<a name="handling-failed-payments"></a>
## 실패한 결제 처리

만료 된 카드 또는 자금이 부족한 카드와 같은 다양한 이유로 구독 결제가 실패합니다. 이 경우 Paddle에서 결제 실패를 처리하도록 하는 것이 좋습니다. 구체적으로 Paddle 대시 보드에서 [Paddle의 자동 결제 이메일 설정](https://vendors.paddle.com/subscription-settings) 할 수 있습니다.

또는 Paddle 대시 보드의 Webhook 설정에 있는 옵션 [`subscription_payment_failed`](https://developer.paddle.com/webhook-reference/subscription-alerts/subscription-payment-failed) 을 찾아 "구독 결제 실패"를 활성화하여 보다 정확한 커스터마이징을 할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use Laravel\Paddle\Http\Controllers\WebhookController as CashierController;

    class WebhookController extends CashierController
    {
        /**
         * Handle subscription payment failed.
         *
         * @param  array  $payload
         * @return void
         */
        public function handleSubscriptionPaymentFailed($payload)
        {
            // Handle the failed subscription payment...
        }
    }

<a name="testing"></a>
## Testing

Paddle에는 현재 적절한 CRUD API가 없으므로 결제 흐름을 수동으로 테스트해야 합니다. Paddle에는 샌드박스 개발자 환경이 없기 때문에 카드 청구는 실시간 청구입니다. 이 문제를 해결하려면 테스트 중에 100% 할인 쿠폰 또는 무료 제품을 사용하는 것이 좋습니다.
