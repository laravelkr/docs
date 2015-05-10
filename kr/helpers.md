# Helper Functions 헬퍼 함수

- [Arrays 배열](#arrays)
- [Paths 경로](#paths)
- [Routing 라우팅](#routing)
- [Strings 문자열](#strings)
- [URLs URL](#urls)
- [Miscellaneous 기타](#miscellaneous)

<a name="arrays"></a>
## Arrays 배열

### array_add

The `array_add` function adds a given key / value pair to the array if the given key doesn't already exist in the array. `array_add` 함수는 배열 내에 키가 존재하지 않는 경우 주어진 key/value 쌍을 배열에 추가합니다. 

	$array = ['foo' => 'bar'];

	$array = array_add($array, 'key', 'value');

### array_divide

The `array_divide` function returns two arrays, one containing the keys, and the other containing the values of the original array. `array_divide` 원래의 배열에서 키들을 담고 있는 배열과 값들을 담고 있는 배열, 총 2개의 배열들을 반환합니다. 

	$array = ['foo' => 'bar'];

	list($keys, $values) = array_divide($array);

### array_dot

The `array_dot` function flattens a multi-dimensional array into a single level array that uses "dot" notation to indicate depth. 
`array_dot` 함수는 다차원 배열을 ‘점(.)’으로 배열 깊이를 표기하면서 단일 레벨 배열로 만듭니다.

	$array = ['foo' => ['bar' => 'baz']];

	$array = array_dot($array);

	// ['foo.bar' => 'baz'];

### array_except

The `array_except` method removes the given key / value pairs from the array.
`array_except` 메소드는 주어진 키 / 값 쌍을 배열에서 제거합니다.

	$array = array_except($array, ['keys', 'to', 'remove']);

### array_fetch

The `array_fetch` method returns a flattened array containing the selected nested element.
`array_fetch` 메소드는 선택한 중첩된 요소를 포함하는 납작한 1차원 배열을 반환합니다.

	$array = [
		['developer' => ['name' => 'Taylor']],
		['developer' => ['name' => 'Dayle']]
	];

	$array = array_fetch($array, 'developer.name');

	// ['Taylor', 'Dayle'];

### array_first

The `array_first` method returns the first element of an array passing a given truth test.
`array_first` 메소드는 넘겨진 배열 중 주어진 테스트(조건)를 만족하는 첫번째 요소를 반환합니다.

	$array = [100, 200, 300];

	$value = array_first($array, function($key, $value)
	{
		return $value >= 150;
	});

A default value may also be passed as the third parameter:
기본 값(Default)은 세번째 매개 변수를 통해 지정할 수 있습니다.

	$value = array_first($array, $callback, $default);

### array_last

The `array_last` method returns the last element of an array passing a given truth test.
`array_last` 메소드는 넘겨진 배열 중 주어진 테스트(조건)를 만족하는 마지막 요소를 반환합니다.

	$array = [350, 400, 500, 300, 200, 100];

	$value = array_last($array, function($key, $value)
	{
		return $value > 350;
	});

	// 500

A default value may also be passed as the third parameter:
기본 값(Default)은 세번째 매개 변수를 통해 지정할 수 있습니다.

	$value = array_last($array, $callback, $default);

### array_flatten

The `array_flatten` method will flatten a multi-dimensional array into a single level.
`array_flatten` 메소드는 다차원 배열을 단일 레벨의 1차원 배열로 만듭니다.

	$array = ['name' => 'Joe', 'languages' => ['PHP', 'Ruby']];

	$array = array_flatten($array);

	// ['Joe', 'PHP', 'Ruby'];

### array_forget

The `array_forget` method will remove a given key / value pair from a deeply nested array using "dot" notation.
`array_forget` 메소드는 “점(.)”표기법을 사용하여 중첩 배열로부터 주어진 키/ 값 쌍을 지웁니다.

	$array = ['names' => ['joe' => ['programmer']]];

	array_forget($array, 'names.joe');

### array_get

The `array_get` method will retrieve a given value from a deeply nested array using "dot" notation.
`array_get` 메소드는  “점(.)”표기법으로 중첩 배열로부터 주어진 값을 찾습니다.

	$array = ['names' => ['joe' => ['programmer']]];

	$value = array_get($array, 'names.joe');

	$value = array_get($array, 'names.john', 'default');

> **Note:** Want something like `array_get` but for objects instead? Use `object_get`.
**노트:** `array_get`과 유사한 형태로 오브젝트에서 사용하고 싶으신가요? `object_get` 함수를 사용해보세요.

### array_only

The `array_only` method will return only the specified key / value pairs from the array.
`array_only` 메소드는 특정한 키 / 값 쌍만을 배열로부터 반환합니다.

	$array = ['name' => 'Joe', 'age' => 27, 'votes' => 1];

	$array = array_only($array, ['name', 'votes']);

### array_pluck

The `array_pluck` method will pluck a list of the given key / value pairs from the array.
`array_pluck` 메소드는 배열로부터 주어진 키 / 값 쌍의 리스트를 뽑아냅니다.

	$array = [['name' => 'Taylor'], ['name' => 'Dayle']];

	$array = array_pluck($array, 'name');

	// ['Taylor', 'Dayle'];

### array_pull

The `array_pull` method will return a given key / value pair from the array, as well as remove it.
`array_pull` 메소드는 배열에서 주어진 키 / 값 쌍을 반환함과 동시에 제거합니다.

	$array = ['name' => 'Taylor', 'age' => 27];

	$name = array_pull($array, 'name');

### array_set

The `array_set` method will set a value within a deeply nested array using "dot" notation.
`array_set` 메소드는 “점(.)” 표기법을 사용하여 중첩 배열 내에 값을 설정합니다.

	$array = ['names' => ['programmer' => 'Joe']];

	array_set($array, 'names.editor', 'Taylor');

### array_sort

The `array_sort` method sorts the array by the results of the given Closure.
`array_sort` 메소드는 주어진 클로져의 결과 값으로 배열을 정렬합니다.

	$array = [
		['name' => 'Jill'],
		['name' => 'Barry']
	];

	$array = array_values(array_sort($array, function($value)
	{
		return $value['name'];
	}));

### array_where

Filter the array using the given Closure.
주어진 클로져를 사용하여 배열을 필터링 합니다.

	$array = [100, '200', 300, '400', 500];

	$array = array_where($array, function($key, $value)
	{
		return is_string($value);
	});

	// Array ( [1] => 200 [3] => 400 )

### head

Return the first element in the array. 
배열의 첫번째 요소를 반환합니다.

	$first = head($this->returnsArray('foo'));

### last

Return the last element in the array. Useful for method chaining.
배열의 마지막 요소를 반완합니다. 메소드 체인을 사용하는데 유용합니다.

	$last = last($this->returnsArray('foo'));

<a name="paths"></a>
## Paths

### app_path

Get the fully qualified path to the `app` directory.
`app` 폴더에 대한 완전한 경로를 얻습니다.

	$path = app_path();

### base_path

Get the fully qualified path to the root of the application install.
어플리케이션 설치 루트의 완전한 경로를 얻습니다.

### config_path

Get the fully qualitifed path to the `config` directory.
`config` 디렉토리의 완전한 경로를 얻습니다. 

### public_path

Get the fully qualified path to the `public` directory.
`public`폴더의 완전한 경로를 얻습니다.

### storage_path

Get the fully qualified path to the `storage` directory.
`storage` 폴더의 완전한 경로를 얻습니다.

<a name="routing"></a>
## Routing

### get

Register a new GET route with the router.
새로운 GET 라우트를 등록합니다.

	get('/', function() { return 'Hello World'; });

### post

Register a new POST route with the router.
새로운 POST 라우트를 등록합니다.

	post('foo/bar', 'FooController@action');

### put

Register a new PUT route with the router.
새로운  PUT 라우트를 등록합니다.

	put('foo/bar', 'FooController@action');

### patch

Register a new PATCH route with the router.
새로운 PATCH 라우트를 등록합니다.

	patch('foo/bar', 'FooController@action');

### delete

Register a new DELETE route with the router.
새로운 DELETE 라우트를 등록합니다.

	delete('foo/bar', 'FooController@action');
	
### resource

Register a new RESTful resource route with the router.
새로운 RESTful 라우트를 등록합니다.

	resource('foo', 'FooController');

<a name="strings"></a>
## Strings

### camel_case

Convert the given string to `camelCase`.
주어진 문자열을 `camelCase` 형태로 변환합니다.

	$camel = camel_case('foo_bar');

	// fooBar

### class_basename

Get the class name of the given class, without any namespace names.
어떤 네임스페이스 이름 없이 주어진 클래스의 클래스 명을 가져옵니다.

	$class = class_basename('Foo\Bar\Baz');

	// Baz

### e

Run `htmlentities` over the given string, with UTF-8 support.
주어진 문자열에 대해 `htmlentities`를 실행합니다. (UTF-8 지원)

	$entities = e('<html>foo</html>');

### ends_with

Determine if the given haystack ends with a given needle.
주어진 haystack(건초더미)가 주어진 바늘(needle)로 끝나는지 알아냅니다.

	$value = ends_with('This is my name', 'name');

### snake_case

Convert the given string to `snake_case`.
주어진 문자열을 `snake_case` 형태로 변환합니다.

	$snake = snake_case('fooBar');

	// foo_bar

### str_limit

Limit the number of characters in a string.
문자열의 단어 길이를 제한합니다.

	str_limit($value, $limit = 100, $end = '...')

Example:

	$value = str_limit('The PHP framework for web artisans.', 7);

	// The PHP...

### starts_with

Determine if the given haystack begins with the given needle.
주어진 haystack(건초더미)가 주어진 바늘(needle)로 시작하는지 알아냅니다.

	$value = starts_with('This is my name', 'This');

### str_contains

Determine if the given haystack contains the given needle.
주어진 haystack(건초더미)에 주어진 바늘(needle)이 포함되어있는지 찾습니다.

	$value = str_contains('This is my name', 'my');

### str_finish

Add a single instance of the given needle to the haystack. Remove any extra instances.

	$string = str_finish('this/string', '/');

	// this/string/

### str_is

Determine if a given string matches a given pattern. Asterisks may be used to indicate wildcards.
주어진 문자열이 주어진 패턴에 일치하는지 확인합니다. 별표는 wildcards를 가르키는데 사용될 수 있습니다.

	$value = str_is('foo*', 'foobar');

### str_plural

Convert a string to its plural form (English only).
복수 형태로 문자열을 변환합니다. (영어만, English Only)

	$plural = str_plural('car');

### str_random

Generate a random string of the given length.
주어진 길이만큼의 랜덤 문자열을 생성합니다.

	$string = str_random(40);

### str_singular

Convert a string to its singular form (English only).
단수 형태로 문자열을 변환합니다. (영어만, English Only)

	$singular = str_singular('cars');

### str_slug

Generate a URL friendly "slug" from a given string.
주어진 문자열에서 URL에 친숙한 “slug”를 생성합니다.

	str_slug($title, $separator);

Example:

	$title = str_slug("Laravel 5 Framework", "-");

	// laravel-5-framework

### studly_case

Convert the given string to `StudlyCase`.
주어진 문자열을 `StudlyCase` 형태로 변환합니다.

	$value = studly_case('foo_bar');

	// FooBar

### trans

Translate a given language line. Alias of `Lang::get`.
주어진 언어 문장을 번역합니다. `Lang::get` 의 별칭입니다.

	$value = trans('validation.required'):

### trans_choice

Translate a given language line with inflection. Alias of `Lang::choice`.
억양, 어조(inflection)를 사용하여 주어진 언어 문장을 번역합니다. `Lang::choice`의 별칭입니다.

	$value = trans_choice('foo.bar', $count);

<a name="urls"></a>
## URLs

### action

Generate a URL for a given controller action.
주어진 controller action으로 URL을 생성합니다.

	$url = action('HomeController@getIndex', $params);

### route

Generate a URL for a given named route.
주어진 라우트 이름으로 URL을 생성합니다.

	$url = route('routeName', $params);

### asset

Generate a URL for an asset.
asset을 위한 URL을 생성합니다.

	$url = asset('img/photo.jpg');

### secure_asset

Generate a URL for an asset using HTTPS.
HTTPS를 사용하여 asset을 위한 URL을 생성합니다.

	echo secure_asset('foo/bar.zip', $title, $attributes = []);

### secure_url

Generate a fully qualified URL to a given path using HTTPS.
주어진 경로에 대한 완전한 URL을 HTTPS를 사용하여 생성합니다.

	echo secure_url('foo/bar', $parameters = []);

### url

Generate a fully qualified URL to the given path.
주어진 경로에 대한 완전한 URL을 생성합니다.

	echo url('foo/bar', $parameters = [], $secure = null);

<a name="miscellaneous"></a>
## Miscellaneous

### csrf_token

Get the value of the current CSRF token.
현재 CSRF token 값을 얻습니다.

	$token = csrf_token();

### dd

Dump the given variable and end execution of the script.
주어진 변수와 end execution of the script 를 Dump 합니다.

	dd($value);

### elixir

Get the path to a versioned Elixir file.
versioned Elixir file의 경로를 얻습니다.

	elixir($file);

### env

Gets the value of an environment variable or return a default value.
환경 변수값을 가져오거나 기본 값을 반환합니다.

	env('APP_ENV', 'production')

### event

Fire an event.
이벤트를 실행합니다.

	event('my.event');

### value

If the given value is a `Closure`, return the value returned by the `Closure`. Otherwise, return the value.
만약 주어진 값이 `Closuer`이라면 `Closure`에 의해 반환된 값을 반환합니다.  그렇지 않으면 값을 반환합니다.

	$value = value(function() { return 'bar'; });

### view

Get a View instance for the given view path.
주어진 view 경로의 view 인스턴스를 가져옵니다.

	return view('auth.login');

### with

Return the given object.
주어진 object를 반환합니다.

	$value = with(new Foo)->doWork();
