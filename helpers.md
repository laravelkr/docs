# 헬퍼 함수들

- [소개](#introduction)
- [사용 가능한 메소드들](#available-methods)

<a name="introduction"></a>
## 소개

라라벨은 다양한 글로벌 "헬퍼" PHP 함수들을 포함하고 있습니다. 그 중 많은 기능은 프레임워크가 스스로 사용하지만 여러분의 어플리케이션에도 자유롭게 사용하실 수 있습니다. 

<a name="available-methods"></a>
## 사용 가능한 메소드

<style>
    .collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    .collection-method-list a {
        display: block;
    }
</style>

### 배열

- [array_add](#method-array-add)
- [array_collapse](#method-array-collapse)
- [array_divide](#method-array-divide)
- [array_dot](#method-array-dot)
- [array_except](#method-array-except)
- [array_first](#method-array-first)
- [array_flatten](#method-array-flatten)
- [array_forget](#method-array-forget)
- [array_get](#method-array-get)
- [array_has](#method-array-has)
- [array_last](#method-array-last)
- [array_only](#method-array-only)
- [array_pluck](#method-array-pluck)
- [array_prepend](#method-array-prepend)
- [array_pull](#method-array-pull)
- [array_set](#method-array-set)
- [array_sort](#method-array-sort)
- [array_sort_recursive](#method-array-sort-recursive)
- [array_where](#method-array-where)
- [array_wrap](#method-array-wrap)
- [head](#method-head)
- [last](#method-last)

### 경로

- [app_path](#method-app-path)
- [base_path](#method-base-path)
- [config_path](#method-config-path)
- [database_path](#method-database-path)
- [mix](#method-mix)
- [public_path](#method-public-path)
- [resource_path](#method-resource-path)
- [storage_path](#method-storage-path)

### 문자열

- [camel_case](#method-camel-case)
- [class_basename](#method-class-basename)
- [e](#method-e)
- [ends_with](#method-ends-with)
- [kebab_case](#method-kebab-case)
- [snake_case](#method-snake-case)
- [str_limit](#method-str-limit)
- [str_after](#method-str-after)
- [str_contains](#method-str-contains)
- [str_finish](#method-str-finish)
- [str_is](#method-str-is)
- [str_plural](#method-str-plural)
- [str_random](#method-str-random)
- [str_singular](#method-str-singular)
- [str_slug](#method-str-slug)
- [studly_case](#method-studly-case)
- [title_case](#method-title-case)
- [trans](#method-trans)
- [trans_choice](#method-trans-choice)

### URLs

- [action](#method-action)
- [asset](#method-asset)
- [secure_asset](#method-secure-asset)
- [route](#method-route)
- [secure_url](#method-secure-url)
- [url](#method-url)

### 기타

- [abort](#method-abort)
- [abort_if](#method-abort-if)
- [abort_unless](#method-abort-unless)
- [auth](#method-auth)
- [back](#method-back)
- [bcrypt](#method-bcrypt)
- [cache](#method-cache)
- [collect](#method-collect)
- [config](#method-config)
- [csrf_field](#method-csrf-field)
- [csrf_token](#method-csrf-token)
- [dd](#method-dd)
- [dispatch](#method-dispatch)
- [env](#method-env)
- [event](#method-event)
- [factory](#method-factory)
- [info](#method-info)
- [logger](#method-logger)
- [method_field](#method-method-field)
- [old](#method-old)
- [redirect](#method-redirect)
- [request](#method-request)
- [response](#method-response)
- [retry](#method-retry)
- [session](#method-session)
- [value](#method-value)
- [view](#method-view)

<a name="method-listing"></a>
## 메소드 목록

<style>
    #collection-method code {
        font-size: 14px;
    }

    #collection-method:not(.first-collection-method) {
        margin-top: 50px;
    }
</style>

<a name="arrays"></a>
## 배열

<a name="method-array-add"></a>
#### `array_add()` {#collection-method .first-collection-method}

`array_add` 함수는 배열 내에 키가 존재하지 않는 경우 주어진 key/value 쌍을 배열에 추가합니다:

    $array = array_add(['name' => 'Desk'], 'price', 100);

    // ['name' => 'Desk', 'price' => 100]

<a name="method-array-collapse"></a>
#### `array_collapse()` {#collection-method}

`array_collapse` 함수는 배열들의 배열(여러개의 배열)을 하나의 배열로 통합합니다:

    $array = array_collapse([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);

    // [1, 2, 3, 4, 5, 6, 7, 8, 9]

<a name="method-array-divide"></a>
#### `array_divide()` {#collection-method}

`array_divide` 함수는 원래의 배열에서 키(key)들을 담고 있는 배열과 값(value)들을 담고 있는 배열, 총 2개의 배열들을 반환합니다:

    list($keys, $values) = array_divide(['name' => 'Desk']);

    // $keys: ['name']

    // $values: ['Desk']

<a name="method-array-dot"></a>
#### `array_dot()` {#collection-method}

`array_dot` 함수는 다차원 배열을 ‘점(.)’으로 배열 깊이를 표기하면서 단일 레벨의 배열로 만듭니다:

    $array = array_dot(['foo' => ['bar' => 'baz']]);

    // ['foo.bar' => 'baz'];

<a name="method-array-except"></a>
#### `array_except()` {#collection-method}

`array_except` 메소드는 주어진 키 / 값 쌍을 배열에서 제거합니다.

    $array = ['name' => 'Desk', 'price' => 100];

    $array = array_except($array, ['price']);

    // ['name' => 'Desk']

<a name="method-array-first"></a>
#### `array_first()` {#collection-method}

`array_first` 함수는 넘겨진 배열 중 주어진 조건을 만족하는 첫번째 요소를 반환합니다:

    $array = [100, 200, 300];

    $value = array_first($array, function ($value, $key) {
        return $value >= 150;
    });

    // 200

메소드에 세번째 파라미터로 기본 값을 지정할 수 있습니다. 배열의 어떠한 값도 조건을 통과하지 못했을 때 이 값이 반환됩니다: 

    $value = array_first($array, $callback, $default);

<a name="method-array-flatten"></a>
#### `array_flatten()` {#collection-method}

`array_flatten` 함수는 다차원 배열을 단일 레벨의 1차원 배열로 만듭니다.

    $array = ['name' => 'Joe', 'languages' => ['PHP', 'Ruby']];

    $array = array_flatten($array);

    // ['Joe', 'PHP', 'Ruby'];

<a name="method-array-forget"></a>
#### `array_forget()` {#collection-method}

`array_forget` 함수는 “점(.)”표기법을 사용하여 중첩 배열로부터 주어진 키/ 값 쌍을 제거합니다:

    $array = ['products' => ['desk' => ['price' => 100]]];

    array_forget($array, 'products.desk');

    // ['products' => []]

<a name="method-array-get"></a>
#### `array_get()` {#collection-method}

`array_get` 함수는  “점(.)”표기법으로 중첩 배열로부터 주어진 값을 찾습니다:

    $array = ['products' => ['desk' => ['price' => 100]]];

    $value = array_get($array, 'products.desk');

    // ['price' => 100]

`array_get` 함수는 특정 키를 찾지 못한 경우 반환되는 기본값을 지정할 수도 있습니다.

    $value = array_get($array, 'names.john', 'default');

<a name="method-array-has"></a>
#### `array_has()` {#collection-method}

`array_has` 함수는 "점(.)" 표기를 이용하여 배열에 주어진 아이템 또는 아이템들이 존재하는지 확인합니다: 

    $array = ['product' => ['name' => 'desk', 'price' => 100]];

    $hasItem = array_has($array, 'product.name');

    // true

    $hasItems = array_has($array, ['product.price', 'product.discount']);

    // false

<a name="method-array-last"></a>
#### `array_last()` {#collection-method}

`array_last` 함수는 전달된 조검을 통과하는 아이템의 가장 마지막 요소를 반환합니다:

    $array = [100, 200, 300, 110];

    $value = array_last($array, function ($value, $key) {
        return $value >= 150;
    });

    // 300

<a name="method-array-only"></a>
#### `array_only()` {#collection-method}

`array_only` 함수는 특정한 키 / 값 쌍만을 배열로부터 반환합니다:

    $array = ['name' => 'Desk', 'price' => 100, 'orders' => 10];

    $array = array_only($array, ['name', 'price']);

    // ['name' => 'Desk', 'price' => 100]

<a name="method-array-pluck"></a>
#### `array_pluck()` {#collection-method}

`array_pluck` 함수는 배열로부터 주어진 키 / 값 쌍의 리스트를 뽑아냅니다:

    $array = [
        ['developer' => ['id' => 1, 'name' => 'Taylor']],
        ['developer' => ['id' => 2, 'name' => 'Abigail']],
    ];

    $array = array_pluck($array, 'developer.name');

    // ['Taylor', 'Abigail'];

리스트만들어 지는 결과가 어떻게 키로 변환될지 지정할 수도 있습니다: 

    $array = array_pluck($array, 'developer.name', 'developer.id');

    // [1 => 'Taylor', 2 => 'Abigail'];

<a name="method-array-prepend"></a>
#### `array_prepend()` {#collection-method}

`array_prepend` 함수는 배열의 시작부분에 아이템을 추가할 것입니다: 

    $array = ['one', 'two', 'three', 'four'];

    $array = array_prepend($array, 'zero');

    // $array: ['zero', 'one', 'two', 'three', 'four']

<a name="method-array-pull"></a>
#### `array_pull()` {#collection-method}

`array_pull` 함수는 배열에서 주어진 키 / 값 쌍을 반환함과 동시에 제거합니다:

    $array = ['name' => 'Desk', 'price' => 100];

    $name = array_pull($array, 'name');

    // $name: Desk

    // $array: ['price' => 100]

<a name="method-array-set"></a>
#### `array_set()` {#collection-method}

`array_set` 함수는 "점(.)" 표기법을 이용하여 중첩된 배열 내에 값을 설정합니다: 

    $array = ['products' => ['desk' => ['price' => 100]]];

    array_set($array, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 200]]]

<a name="method-array-sort"></a>
#### `array_sort()` {#collection-method}

`array_sort` 함수는 주어진 클로져의 결과 값으로 배열을 정렬합니다:

    $array = [
        ['name' => 'Desk'],
        ['name' => 'Chair'],
    ];

    $array = array_values(array_sort($array, function ($value) {
        return $value['name'];
    }));

    /*
        [
            ['name' => 'Chair'],
            ['name' => 'Desk'],
        ]
    */

<a name="method-array-sort-recursive"></a>
#### `array_sort_recursive()` {#collection-method}

`array_sort_recursive` 함수는 `sort` 함수를 이용하여 반복적으로 배열을 정렬합니다: 

    $array = [
        [
            'Roman',
            'Taylor',
            'Li',
        ],
        [
            'PHP',
            'Ruby',
            'JavaScript',
        ],
    ];

    $array = array_sort_recursive($array);

    /*
        [
            [
                'Li',
                'Roman',
                'Taylor',
            ],
            [
                'JavaScript',
                'PHP',
                'Ruby',
            ]
        ];
    */

<a name="method-array-where"></a>
#### `array_where()` {#collection-method}

`array_where` 함수는 주어진 클로져를 사용하여 배열을 필터링합니다: 

    $array = [100, '200', 300, '400', 500];

    $array = array_where($array, function ($value, $key) {
        return is_string($value);
    });

    // [1 => 200, 3 => 400]

<a name="method-array-wrap"></a>
#### `array_wrap()` {#collection-method}

`array_wrap` 함수는 주어진 값을 배열로 만듭니다. 만약 함수에 전달된 값이 배열이라면, 결과에는 변경사항이 없습니다:

    $string = 'Laravel';

    $array = array_wrap($string);

    // [0 => 'Laravel']

<a name="method-head"></a>
#### `head()` {#collection-method}

`head` 함수는 배열의 첫번째 요소를 반환합니다:

    $array = [100, 200, 300];

    $first = head($array);

    // 100

<a name="method-last"></a>
#### `last()` {#collection-method}

`last` 함수는 배열의 마지막 요소를 반환합니다:

    $array = [100, 200, 300];

    $last = last($array);

    // 300

<a name="paths"></a>
## 경로

<a name="method-app-path"></a>
#### `app_path()` {#collection-method}

`app_path` 함수는 `app` 디렉토리에 대한 절대 경로를 반환합니다. `app_path` 함수를 사용하면 어플리케이션 디렉토리에서 특정 파일의 절대 경로를 생성할 수도 있습니다:

    $path = app_path();

    $path = app_path('Http/Controllers/Controller.php');

<a name="method-base-path"></a>
#### `base_path()` {#collection-method}

`base_path` 함수는 프로젝트의 루트 디렉토리에 대한 절대 경로를 반환합니다. `base_path` 함수를 사용하여 프로젝트 루트 디렉토리에 대한 특정 파일의 절대 경로를 생성할 수도 있습니다:

    $path = base_path();

    $path = base_path('vendor/bin');

<a name="method-config-path"></a>
#### `config_path()` {#collection-method}

`config_path` 함수는 어플리케이션의 `config` 디렉토리에 대한 절대 경로를 반환합니다: 

    $path = config_path();

<a name="method-database-path"></a>
#### `database_path()` {#collection-method}

`database_path` 함수는 어플리케이션의 데이터베이스 디렉토리에 대한 절대 경로를 반환합니다: 

    $path = database_path();

<a name="method-mix"></a>
#### `mix()` {#collection-method}

`mix` 함수는 [버전이 지정된 Mix](/docs/{{version}}/mix) 파일에 대한 경로를 반환합니다: 

    mix($file);

<a name="method-public-path"></a>
#### `public_path()` {#collection-method}

`public_path` 함수는 `public` 디렉토리에 대한 절대경로를 반환합니다: 

    $path = public_path();

<a name="method-resource-path"></a>
#### `resource_path()` {#collection-method}

`resource_path` 함수는 `resources` 디렉토리에 대한 절대경로를 반환합니다. `resource_path` 함수를 사용하여 주어진 파일에 대한 경로를 생성할 수도 있습니다:

    $path = resource_path();

    $path = resource_path('assets/sass/app.scss');

<a name="method-storage-path"></a>
#### `storage_path()` {#collection-method}

`storage_path` 함수는 `storage` 디렉토리에 대한 절대경로를 반환합니다. `storage_path` 함수를 사용하여 주어진 파일에 대한 경로를 생성할 수도 있습니다: 

    $path = storage_path();

    $path = storage_path('app/file.txt');

<a name="strings"></a>
## 문자열

<a name="method-camel-case"></a>
#### `camel_case()` {#collection-method}

`camel_case` 함수는 주어진 문자열을 `camelCase` 형태로 변환합니다: 

    $camel = camel_case('foo_bar');

    // fooBar

<a name="method-class-basename"></a>
#### `class_basename()` {#collection-method}

`class_basename`은 클래스의 네임스페이스를 제거한 클래스의 클래스 명을 반환합니다: 

    $class = class_basename('Foo\Bar\Baz');

    // Baz

<a name="method-e"></a>
#### `e()` {#collection-method}

`e` 함수는 주어진 문자열에 PHP의 `htmlspecialchars` 함수를 `duuble_encode` 옵션이 `false` 인 형태로 실행한 결과를 반환합니다:

    echo e('<html>foo</html>');

    // &lt;html&gt;foo&lt;/html&gt;

<a name="method-ends-with"></a>
#### `ends_with()` {#collection-method}

`ends_with` 함수는 주어진 문자열이 특정 값으로 끝나는지 알아냅니다: 

    $value = ends_with('This is my name', 'name');

    // true

<a name="method-kebab-case"></a>
#### `kebab_case()` {#collection-method}

`kebab_case` 함수는 주어진 문자열을 `kebab-case`로 변환합니다:
 (역자주 : 단어와 단어를 '-'로 연결한 형태)

    $value = kebab_case('fooBar');

    // foo-bar

<a name="method-snake-case"></a>
#### `snake_case()` {#collection-method}

`snake_case` 함수는 주어진 문자열을 `snake_case` 형태로 변환합니다:

    $snake = snake_case('fooBar');

    // foo_bar

<a name="method-str-limit"></a>
#### `str_limit()` {#collection-method}

`str_limit` 함수는 문자열의 문자 수를 제한합니다. 함수는 문자열을 첫번째 인자로 받고 반환되는 최대 문자의 길이를 두번째 인자로 받습니다: 

    $value = str_limit('The PHP framework for web artisans.', 7);

    // The PHP...

<a name="method-starts-with"></a>
#### `starts_with()` {#collection-method}

`starts_with` 함수는 문자열이 주어진 문자열으로 시작하는지 판별합니다: 

    $value = starts_with('This is my name', 'This');

    // true

<a name="method-str-after"></a>
#### `str_after()` {#collection-method}

The `str_after` function returns everything after the given value in a string:

`str_after` 함수는 문자열에서 주어진 문자열 다음의 모든 값을 반환합니다:

    $value = str_after('This is: a test', 'This is:');

    // ' a test'

<a name="method-str-contains"></a>
#### `str_contains()` {#collection-method}

`str_contains` 함수는 주어진 문자열이 특정 문자열을 포함하는지 판별합니다: 

    $value = str_contains('This is my name', 'my');

    // true

또한 주어진 문자열이 특정 문자열을 포함하고 있는지 판별하기 위한 배열을 전달할 수도 있습니다: 

    $value = str_contains('This is my name', ['my', 'foo']);

    // true

<a name="method-str-finish"></a>
#### `str_finish()` {#collection-method}

`str_finish` 함수는 문자열 뒤에 주어진 값을 추가합니다: 

    $string = str_finish('this/string', '/');

    // this/string/

<a name="method-str-is"></a>
#### `str_is()` {#collection-method}

`str_is` 함수는 주어진 문자열이 주어진 패턴과 대응되는지 확인합니다. 와일드카드를 표시하기 위해 별표를 사용할 수 있습니다: 

    $value = str_is('foo*', 'foobar');

    // true

    $value = str_is('baz*', 'foobar');

    // false

<a name="method-str-plural"></a>
#### `str_plural()` {#collection-method}

`str_plural` 함수는 문자열을 복수형태로 변환합니다. 이 함수는 현재 영어에만 적용 가능합니다: 

    $plural = str_plural('car');

    // cars

    $plural = str_plural('child');

    // children

문자열의 단일 혹은 복수 형태를 조회하기 위해서, 함수의 두번째 인자로 정수를 전달할 수 있습니다: 

    $plural = str_plural('child', 2);

    // children

    $plural = str_plural('child', 1);

    // child

<a name="method-str-random"></a>
#### `str_random()` {#collection-method}

`str_random` 함수는 지정된 길이의 문자열을 무작위로 생성합니다. 이 함수는 PHP의 `random_bytes` 함수를 사용합니다:

    $string = str_random(40);

<a name="method-str-singular"></a>
#### `str_singular()` {#collection-method}

`str_singular` 함수는 문자열을 단수 형태로 변환합니다. 이 함수는 현재 영어에만 적용 가능합니다: 

    $singular = str_singular('cars');

    // car

<a name="method-str-slug"></a>
#### `str_slug()` {#collection-method}

`str_slug` 함수는 주어진 문자열로부터 URL에 알맞은 "slug"를 생성합니다: 

    $title = str_slug('Laravel 5 Framework', '-');

    // laravel-5-framework

<a name="method-studly-case"></a>
#### `studly_case()` {#collection-method}

`studly_case` 함수는 주어진 문자열을 `StudlyCase` 형태로 변환합니다: 

    $value = studly_case('foo_bar');

    // FooBar

<a name="method-title-case"></a>
#### `title_case()` {#collection-method}

`title_case` 함수는 주어진 문자열을 `Title Case` 로 변환합니다(단어별로 앞글자를 대문자, 단어 사이를 공백이 포함되는 형태):

    $title = title_case('a nice title uses the correct case');

    // A Nice Title Uses The Correct Case

<a name="method-trans"></a>
#### `trans()` {#collection-method}

`trans` 함수는 [다국어 파일](/docs/{{version}}/localization)을 이용하여 주어진 언어를 번역합니다:

    echo trans('validation.required'):

<a name="method-trans-choice"></a>
#### `trans_choice()` {#collection-method}

`trans_choice` 함수는 주어진, 수량(단수, 복수 처리)을 이용하여 주어진 언어를 번역합니다: 

    $value = trans_choice('foo.bar', $count);

<a name="urls"></a>
## URLs

<a name="method-action"></a>
#### `action()` {#collection-method}

`action` 함수는 주어진 컨트롤러 메소드로 URL을 생성합니다. 컨트롤러는 전체 네임스페이스를 전달하지 않아도 됩니다. 대신, `App\Http\Controllers` 네임스페이스에 따른 컨트롤러 클래스 이름을 전달하면 됩니다.: 

    $url = action('HomeController@getIndex');

메소드가 라우트 파라미터를 받아들인다면, 두번째 인자로 메소드에 전달하십시오: 

    $url = action('UserController@profile', ['id' => 1]);

<a name="method-asset"></a>
#### `asset()` {#collection-method}

HTTP요청의 현재 scheme(HTTP나 HTTPS)을 이용하여 asset을 사용하기 위한 URL을 생성합니다: 

    $url = asset('img/photo.jpg');

<a name="method-secure-asset"></a>
#### `secure_asset()` {#collection-method}

HTTPS를 이용하여 asset을 사용하기 위한 URL을 생성합니다: 

    echo secure_asset('foo/bar.zip', $title, $attributes = []);

<a name="method-route"></a>
#### `route()` {#collection-method}

`route` 함수는 주어진 라우트 이름으로 URL을 생성합니다: 

    $url = route('routeName');

라우트가 파라미터를 가진다면 파라미터를 두번째 인자로 메소드에 전달하세요: 

    $url = route('routeName', ['id' => 1]);

기본적으로 `route` 함수는 절대경로 URL을 생성합니다. 만약 상태경로의 URL을 생성하려면 세번째 인자를 `false`로 전달하면 됩니다:

    $url = route('user/profile', ['id' => 1], false);

<a name="method-secure-url"></a>
#### `secure_url()` {#collection-method}

`secure_url` 함수는 주어진 경로에 대한 전체 HTTPS URL을 생성합니다:

    echo secure_url('user/profile');

    echo secure_url('user/profile', [1]);

<a name="method-url"></a>
#### `url()` {#collection-method}

`url` 함수는 주어진 경로에 대한 전체 URL을 생성합니다: 

    echo url('user/profile');

    echo url('user/profile', [1]);

경로를 전달하지 않으면, `Illuminate\Routing\UrlGenerator` 인스턴스가 반환됩니다:

    echo url()->current();
    echo url()->full();
    echo url()->previous();

<a name="miscellaneous"></a>
## 기타 함수들

<a name="method-abort"></a>
#### `abort()` {#collection-method}

`abort` 함수는 Exception 핸들러에 의해서 렌더링 될 수 있는 HTTP exception 을 발생시킵니다:

    abort(401);

exception 의 응답 텍스트를 제공할 수도 있습니다:

    abort(401, 'Unauthorized.');

<a name="method-abort-if"></a>
#### `abort_if()` {#collection-method}

`abort_if` 함수는 주어진 조건식이 `true` 일때 HTTP exception을 발생시킵니다: 

    abort_if(! Auth::user()->isAdmin(), 403);

<a name="method-abort-unless"></a>
#### `abort_unless()` {#collection-method}

`abort_unless` 함수는 주어진 조건식이 `false` 일때 HTTP exception 을 발생시킵니다: 

    abort_unless(Auth::user()->isAdmin(), 403);

<a name="method-auth"></a>
#### `auth()` {#collection-method}

`auth` 함수는 authenticator 인스턴스를 반환합니다. 편의를 위하여 `Auth` 파사드 대신 이용할 수 있습니다: 

    $user = auth()->user();

<a name="method-back"></a>
#### `back()` {#collection-method}

`back()` 함수는 사용자의 이전 위치로 리다이렉트하는 응답을 생성합니다: 

    return back();

<a name="method-bcrypt"></a>
#### `bcrypt()` {#collection-method}

`bcrypt` 함수는 Bcrypt를 이용하여 주어진 값을 해시 처리합니다. `Hash` 파사드 대신 사용할 수 있습니다: 

    $password = bcrypt('my-secret-password');

<a name="method-cache"></a>
#### `cache()` {#collection-method}

`cache` 함수는 캐시로 부터 값을 가져오는데 사용할 수 있습니다. 캐시에서 주어진 키가 존재하지 않는 경우, 옵션으로 전달된 기본값(두번째인자)가 반환됩니다

    $value = cache('key');

    $value = cache('key', 'default');

함수에 키 / 값으로 된 배열을 전달하여 캐시에 아이템을 추가할 수 있습니다. 또한 캐시에 값이 얼마나 유지되어야 하는지에 대한 시간(분)을 숫자로 전달할 수도 있습니다.

    cache(['key' => 'value'], 5);

    cache(['key' => 'value'], Carbon::now()->addSeconds(10));

<a name="method-collect"></a>
#### `collect()` {#collection-method}

`collect` 함수는 주어진 배열로부터 [collection](/docs/{{version}}/collections) 인스턴스를 생성합니다: 

    $collection = collect(['taylor', 'abigail']);

<a name="method-config"></a>
#### `config()` {#collection-method}

`config` 함수는 환경 설정 변수의 값을 가져옵니다. 설정 값은 파일명과 접근하고자 하는 옵션을 포함하는 "점(.)" 문법(syntax)를 사용하여 접근할 수 있습니다. 설정 옵션이 존재하지 않는다면 지정된 기본값이 반환됩니다: 

    $value = config('app.timezone');

    $value = config('app.timezone', $default);


`config` 헬퍼는 키 / 값 쌍을 런타임에 전달하는 방법으로 설정 변수를 설정할 수도 있습니다: 

    config(['app.debug' => true]);

<a name="method-csrf-field"></a>
#### `csrf_field()` {#collection-method}

`csrf_field` 함수는 CSRF 토큰 값을 포함하는 HTML `hidden` Input 필드를 생성합니다. 예를 들어 [Blade syntax](/docs/{{version}}/blade)에서 사용할 수 있습니다: 

    {{ csrf_field() }}

<a name="method-csrf-token"></a>
#### `csrf_token()` {#collection-method}

`csrf_token` 함수는 현재 CSRF 토큰의 값을 조회합니다: 

    $token = csrf_token();

<a name="method-dd"></a>
#### `dd()` {#collection-method}

`dd` 함수는 주어진 변수들을 Dump 처리하고 스크립트의 실행을 중단합니다: 

    dd($value);

    dd($value1, $value2, $value3, ...);

스크립트 실행을 중단하고 싶지 않다면, `dump` 함수를 사용하십시오:

    dump($value);

<a name="method-dispatch"></a>
#### `dispatch()` {#collection-method}

`dispatch` 함수는 라라벨의 [job queue](/docs/{{version}}/queues)에 새로운 작업을 추가합니다: 

    dispatch(new App\Jobs\SendEmails);

<a name="method-env"></a>
#### `env()` {#collection-method}

`env` 함수는 환경 변수의 값을 가져오거나 기본값을 반환합니다: 

    $env = env('APP_ENV');

    // Return a default value if the variable doesn't exist...
    $env = env('APP_ENV', 'production');

<a name="method-event"></a>
#### `event()` {#collection-method}

`event` 함수는 주어진 [event](/docs/{{version}}/events)를 리스너들에게 보냅니다: 

    event(new UserRegistered($user));

<a name="method-factory"></a>
#### `factory()` {#collection-method}

`factory` 함수는 주어진 클래스, 이름, 양을 위한 모델 팩토리 빌더를 생성합니다. [testing](/docs/{{version}}/database-testing#writing-factories)이나 [seeding](/docs/{{version}}/seeding#using-model-factories) 중에 이용할 수 있습니다: 

    $user = factory(App\User::class)->make();

<a name="method-info"></a>
#### `info()` {#collection-method}

`info` 함수는 로그에 정보를 기록합니다:

    info('Some helpful information!');

문맥에대한 데이터를 함수에 배열로 전달할 수도 있습니다:

    info('User login attempt failed.', ['id' => $user->id]);

<a name="method-logger"></a>
#### `logger()` {#collection-method}

`logger` 함수는 로그에 `debug` 로그 레벨을 기록하는데 사용합니다:

    logger('Debug message');

문맥에대한 데이터를 함수에 배열로 전달할 수도 있습니다:

    logger('User has logged in.', ['id' => $user->id]);

함수에 아무런 값이 전달되지 않으면 [logger](/docs/{{version}}/errors#logging) 인스턴스가 반환됩니다:

    logger()->error('You are not allowed here.');

<a name="method-method-field"></a>
#### `method_field()` {#collection-method}

`method_field` 함수는 HTTP 메소드 형식의 가짜(spoof) 값을 포함하는 HTML `hidden` Input 필드를 생성합니다. 예를 들어 [Blade syntax](/docs/{{version}}/blade)에서 사용할 수 있습니다: 

    <form method="POST">
        {{ method_field('DELETE') }}
    </form>

<a name="method-old"></a>
#### `old()` {#collection-method}

`old` 함수는 세션에 저장된 오래된(flashed) 입력 값을 [조회](/docs/{{version}}/requests#retrieving-input)합니다: 
    $value = old('value');

    $value = old('value', 'default');

<a name="method-redirect"></a>
#### `redirect()` {#collection-method}

`redirect` 함수는 HTTP 응답을 리다이렉션 처리 하거나, 인자 없이 호출되는 경우 리디렉터 인스턴스를 반환합니다: 

    return redirect('/home');

    return redirect()->route('route.name');

<a name="method-request"></a>
#### `request()` {#collection-method}

`request` 함수는 현재의 [요청](/docs/{{version}}/requests) 인스턴스를 반환하거나 입력 아이템을 가져옵니다: 

    $request = request();

    $value = request('key', $default = null)

<a name="method-response"></a>
#### `response()` {#collection-method}

`response` 함수는 [응답](/docs/{{version}}/responses) 인스턴스를 생성하거나 응답 팩토리의 인스턴스를 가져옵니다: 

    return response('Hello World', 200, $headers);

    return response()->json(['foo' => 'bar'], 200, $headers);

<a name="method-retry"></a>
#### `retry()` {#collection-method}

`retry` 함수는 주어진 최대 횟수가 될 때까지 주어진 콜백을 실행하려고 시도합니다. 콜백이 예외-exception를 던지지 않는다면, 결과 값이 반환됩니다. 콜백이 예외-exception를 던지면 자동으로 다시 시도됩니다. 최대 재시도 횟수를 초과하면 예외가 던져집니다.

    return retry(5, function () {
        // Attempt 5 times while resting 100ms in between attempts...
    }, 100);


<a name="method-session"></a>
#### `session()` {#collection-method}

`session` 함수는 세션 값을 얻거나 지정하는 데에 사용할 수 있습니다. 

    $value = session('key');

키 / 값 쌍들의 배열을 함수로 전달하여 값을 설정할 수 있습니다: 

    session(['chairs' => 7, 'instruments' => 3]);

함수에 아무런 값도 전달되지 않는다면 세션 스토어가 반환됩니다: 

    $value = session()->get('key');

    session()->put('key', $value);

<a name="method-value"></a>
#### `value()` {#collection-method}

`value` 함수의 행동은 단순히 자신에게 주어진 값을 반환합니다. 하지만 함수에 `Closure`를 전달하면 `Closure`가 실행되고 그 결과물이 반환됩니다: 

    $value = value(function () {
        return 'bar';
    });

<a name="method-view"></a>
#### `view()` {#collection-method}

`view` 함수는 [view](/docs/{{version}}/views) 인스턴스를 조회합니다: 

    return view('auth.login');

