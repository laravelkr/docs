# 데이터베이스 : 페이지네이션

- [시작하기](#introduction)
- [기본 사용법](#basic-usage)
    - [쿼리 빌더 결과를 페이징 하기](#paginating-query-builder-results)
    - [Eloquent 결과를 페이징 하기](#paginating-eloquent-results)
    - [커서 페이지 매기기](#cursor-pagination)
    - [수동으로 Paginator 만들기](#manually-creating-a-paginator)
    - [사용자 정의 페이지네이션 URL](#customizing-pagination-urls)
- [페이지네이션 결과 표시](#displaying-pagination-results)
    - [페이지네이션 링크 창 조정](#adjusting-the-pagination-link-window)
    - [결과를 JSON 으로 변환](#converting-results-to-json)
- [사용자 정의 페이지네이션 뷰 파일](#customizing-the-pagination-view)
    - [부트스트랩 사용](#using-bootstrap)
- [Paginator와 LengthAwarePaginator 인스턴스 메소드](#paginator-instance-methods)
- [Cursor Paginator 인스턴스 메소드](#cursor-paginator-instance-methods)

<a name="introduction"></a>
## 시작하기

다른 프레임 워크에서는 페이지 출력 처리를 하는것은 굉장히 성가신 일입니다. 페이지네이션에 대한 라라벨의 접근방식이 새로운 바람이 되기를 바랍니다. 라라벨의 페이지네이션은 별다른 설정 없이 [쿼리 빌더](/docs/{{version}}/queries) 와 [Eloquent ORM](/docs/{{version}}/eloquent)에 통합되어 편리하고 사용하기 쉬운 데이타베이스 페이지네이션을 제공합니다.

기본적으로 paginator 에서 생성된 HTML은 [Tailwind CSS 프레임워크](https://tailwindcss.com/) 와 호환됩니다. 그러나 부트스트랩 페이지네이션 지원도 사용할 수 있습니다.

<a name="basic-usage"></a>
## 기본 사용법

<a name="paginating-query-builder-results"></a>
### 쿼리 빌더 결과를 페이징 하기

항목에 페이지를 매기는 방법에는 여러 가지가 있습니다. 가장 간단한 방법은 [쿼리 빌더](/docs/{{version}}/queries) 또는 [Eloquent 쿼리](/docs/{{version}}/eloquent)에서 `paginate` 메소드를 사용하는 것입니다. `paginate` 메소드는 사용자가 보고 있는 현재 페이지를 기반으로 쿼리의 "limit" 및 "offset" 설정을 자동으로 처리합니다. 기본적으로 현재 페이지는 HTTP 요청의 `page` 쿼리 문자열 인수 값으로 감지됩니다. 이 값은 라라벨에 의해 자동으로 감지되며 paginator 에 의해 생성된 링크에도 자동으로 삽입됩니다.

이 예제에서 `paginate` 메소드에 전달된 유일한 인수는 "페이지당" 표시하려는 항목의 갯수입니다. 이 경우 페이지당 `15`개의 항목을 표시하도록 지정해 보겠습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Support\Facades\DB;

    class UserController extends Controller
    {
        /**
         * Show all application users.
         *
         * @return \Illuminate\Http\Response
         */
        public function index()
        {
            return view('user.index', [
                'users' => DB::table('users')->paginate(15)
            ]);
        }
    }

<a name="simple-pagination"></a>
#### 심플 페이징

`paginate` 메서드는 데이터베이스에서 레코드를 검색하기 전에 쿼리와 일치하는 총 레코드 수를 계산합니다. 이것은 paginator 가 총 몇 페이지의 레코드가 있는지 알기 위해 수행됩니다. 그러나 애플리케이션의 UI에 총 페이지 수를 표시하지 않으려면 레코드 카운트 쿼리가 필요하지 않습니다.

따라서 애플리케이션의 UI에 간단한 "다음" 및 "이전" 링크만 표시해야 하는 경우 `simplePaginate` 메서드를 사용하여 효율적인 단일 쿼리를 수행할 수 있습니다.

    $users = DB::table('users')->simplePaginate(15);

<a name="paginating-eloquent-results"></a>
### Eloquent 결과를 페이징 하기

[Eloquent](/docs/{{version}}/eloquent) 쿼리에 페이지를 매길 수도 있습니다. 이 예제에서는 `App\Models\User` 모델에 페이지를 매기고 페이지당 15개의 레코드를 표시할 계획임을 나타냅니다. 보시다시피 구문은 쿼리 빌더 결과를 페이징하는 것과 거의 동일합니다.

    use App\Models\User;

    $users = User::paginate(15);

물론 `where` 절과 같이 쿼리에 다른 제약 조건을 설정한 후 `paginate` 메서드를 호출할 수 있습니다.

    $users = User::where('votes', '>', 100)->paginate(15);

Eloquent 모델의 페이지를 매길 때 `simplePaginate` 메소드를 사용할 수도 있습니다.

    $users = User::where('votes', '>', 100)->simplePaginate(15);

마찬가지로 `cursorPaginate` 메소드를 사용하여 Eloquent 모델에 커서를 페이지네이션할 수 있습니다.

    $users = User::where('votes', '>', 100)->cursorPaginate(15);

<a name="multiple-paginator-instances-per-page"></a>
#### 페이지당 여러 Paginator 인스턴스

애플리케이션에서 렌더링하는 단일 화면에 두 개의 개별 paginator 를 렌더링해야 하는 경우가 있습니다. 그러나 두 paginator 인스턴스가 `page` 쿼리 문자열 매개변수를 사용하여 현재 페이지를 저장하는 경우 두 paginator 인스턴스가 충돌합니다. 이 충돌을 해결하기 위해 `paginate`, `simplePaginate` 및 `cursorPaginate` 메소드에 제공된 세 번째 인수를 통해 paginator 의 현재 페이지를 저장하는 데 사용하려는 쿼리 문자열 매개변수의 이름을 전달할 수 있습니다.

    use App\Models\User;

    $users = User::where('votes', '>', 100)->paginate(
        $perPage = 15, $columns = ['*'], $pageName = 'users'
    );

<a name="cursor-pagination"></a>
### 커서 페이지 매기기

`paginate` 및 `simplePaginate`가 SQL "offset" 절을 사용하여 쿼리를 생성하는 동안 커서 페이지네이션은 쿼리에 포함된 정렬된 열의 값을 비교하는 "where" 절을 구성하여 작동하며, 라라벨의 모든 페이지네이션 방법 중에서 가장 효율적인 데이터베이스 성능을 제공합니다. 이 페이지네이션 방법은 대규모 데이터셋 및 "무한" 스크롤링 사용자 인터페이스에 특히 적합합니다.

페이지네이션에 의해 생성된 URL 은 쿼리 문자열에 페이지 번호를 포함하는 오프셋 기반과 달리 커서 기반 페이지네이션은 쿼리 문자열에 "cursor" 문자열을 배치합니다. 커서는 다음 페이지를 매긴 쿼리가 페이지를 매기기 시작해야 하는 위치와 페이지를 매길 방향을 포함하는 인코딩된 문자열입니다.

```nothing
http://localhost/users?cursor=eyJpZCI6MTUsIl9wb2ludHNUb05leHRJdGVtcyI6dHJ1ZX0
```

쿼리 빌더가 제공하는 `cursorPaginate` 메소드를 통해 커서 기반 paginator 인스턴스를 생성할 수 있습니다. 이 메서드는 `Illuminate\Pagination\CursorPaginator` 의 인스턴스를 반환합니다.

    $users = DB::table('users')->orderBy('id')->cursorPaginate(15);

cursor paginator 인스턴스를 검색한 후에는 일반적으로 `paginate` 및 `simplePaginate` 메서드를 사용할 때와 같이 [페이지네이션 결과 출력하기](#displaying-pagination-results)할 수 있습니다. cursor paginator 에서 제공하는 인스턴스 메소드에 대한 자세한 내용은 [cursor paginator 인스턴스 메소드 문서](#cursor-paginator-instance-methods)를 참조하십시오.

> {노트} 커서 페이지 매기기를 활용하려면 쿼리에 "order by" 절이 포함되어야 합니다.

<a name="cursor-vs-offset-pagination"></a>
#### 커서 vs. 오프셋 페이지네이션

오프셋 페이지네이션과 커서 페이지 페이지네이션의 차이점을 설명하기 위해 몇 가지 예제 SQL 쿼리를 살펴보겠습니다. 다음 쿼리는 모두 `id`로 정렬된 `users` 테이블에 대한 결과의 "두 번째 페이지"를 표시합니다.

```sql
# Offset Pagination...
select * from users order by id asc limit 15 offset 15;

# Cursor Pagination...
select * from users where id > 15 order by id asc limit 15;
```

커서 페이지네이션 쿼리는 오프셋 페이지네이션에 비해 다음과 같은 이점을 제공합니다.

- 큰 데이터 세트의 경우 "order by" 열이 인덱싱되면 커서 페이지네이션이 더 나은 성능을 제공합니다. 이는 "offset" 절이 이전에 일치하는 모든 데이터를 검색하기 때문입니다.
- 쓰기가 잦은 데이터 세트의 경우 사용자가 현재 보고 있는 페이지에 결과가 최근에 추가되거나 삭제된 경우 오프셋 페이지네이션이 레코드를 건너뛰거나 중복을 표시할 수 있습니다.

그러나 커서 페이지네이션에는 다음과 같은 제한 사항이 있습니다.

- `simplePaginate`와 마찬가지로 커서 페이지네이션은 "다음" 및 "이전" 링크를 표시하는 데만 사용할 수 있으며 페이지 번호가 있는 링크 생성을 지원하지 않습니다.
- 순서가 최소한 하나의 고유한 열 또는 고유한 열의 조합을 기반으로 해야 합니다. `null` 값이 있는 열은 지원되지 않습니다.
- "order by" 절의 쿼리 표현식은 별칭이 지정되고 "select" 절에도 추가된 경우에만 지원됩니다.

<a name="manually-creating-a-paginator"></a>
### 수동으로 Paginator 만들기

때로는 수동으로 페이지네이션 인스턴스를 만들고 메모리에 이미 있는 항목의 배열을 전달하고 싶을 수 있습니다. 필요에 따라 `Illuminate\Pagination\Paginator`, `Illuminate\Pagination\LengthAwarePaginator` 또는 `Illuminate\Pagination\CursorPaginator` 인스턴스를 생성하면 됩니다.

`Paginator` 및 `CursorPaginator` 클래스는 결과 집합의 총 항목 수를 알 필요가 없습니다. 그러나 이 때문에 이러한 클래스에는 마지막 페이지의 인덱스를 검색하는 메서드가 없습니다. `LengthAwarePaginator`는 `Paginator`와 거의 동일한 인수를 허용합니다. 그러나 결과 집합의 총 항목 수를 계산해야 합니다.

즉, `Paginator` 는 쿼리 빌더의 `simplePaginate` 메서드에 해당하고, `CursorPaginator` 는 `cursorPaginate` 메서드에 해당하고, `LengthAwarePaginator` 는 `paginate` 메서드에 해당합니다.

> {노트} 수동으로 paginator 인스턴스를 생성할 때 paginator 에 전달한 결과 배열을 수동으로 "slice" 해야 합니다. 어떻게 하는지 잘 모르겠다면 [array_slice](https://secure.php.net/manual/en/function.array-slice.php) PHP 함수를 확인하세요.

<a name="customizing-pagination-urls"></a>
### 사용자 정의 페이지네이션 URL

기본적으로 paginator 에 의해 생성된 링크는 현재 요청의 URI 와 일치합니다. 그러나 paginator 의 `withPath` 메소드를 사용하면 링크를 생성할 때 paginator 가 사용하는 URI 를 사용자 정의할 수 있습니다. 예를 들어 paginator 가 `http://example.com/admin/users?page=N` 과 같은 링크를 생성하도록 하려면 `/admin/users`를 `withPath` 메소드에 전달해야 합니다.

    use App\Models\User;

    Route::get('/users', function () {
        $users = User::paginate(15);

        $users->withPath('/admin/users');

        //
    });

<a name="appending-query-string-values"></a>
#### 쿼리 문자열 값 추가

`appends` 메소드를 사용하여 페이지네이션 링크의 쿼리 문자열에 추가할 수 있습니다. 예를 들어, 각 페이지네이션 링크에 `sort=votes`를 추가하려면 `appends`를 다음과 같이 호출해야 합니다.

    use App\Models\User;

    Route::get('/users', function () {
        $users = User::paginate(15);

        $users->appends(['sort' => 'votes']);

        //
    });

현재 요청의 모든 쿼리 문자열 값을 페이지네이션 링크에 추가하려면 `withQueryString` 메서드를 사용할 수 있습니다.

    $users = User::paginate(15)->withQueryString();

<a name="appending-hash-fragments"></a>
#### hash fragment 추가

paginator 에 의해 생성된 URL 에 "hash fragment"을 추가해야 하는 경우 `fragment` 메서드를 사용할 수 있습니다. 예를 들어, 각 페이지네이션 링크의 끝에 `#users`를 추가하려면 다음과 같이 `fragment` 메소드를 호출해야 합니다.

    $users = User::paginate(15)->fragment('users');

<a name="displaying-pagination-results"></a>
## 페이지네이션 결과 표시

`paginate` 메소드를 호출하면 `Illuminate\Pagination\LengthAwarePaginator`의 인스턴스를 반환하고 `simplePaginate` 메소드를 호출하면 `Illuminate\Pagination\Paginator` 인스턴스를 반환합니다. 그리고 마지막으로 `cursorPaginate` 메소드를 호출하면 `Illuminate\Pagination\CursorPaginator` 인스턴스가 반환됩니다.

이러한 개체는 결과 집합을 설명하는 여러 메서드를 제공합니다. 이러한 도우미 메서드 외에도 paginator 인스턴스는 iterators 이며 배열처럼 반복문에서 사용할 수 있습니다. 따라서 결과를 검색한 후에는 [블레이드](/docs/{{version}}/blade)를 사용하여 결과를 표시하고 페이지 링크를 렌더링할 수 있습니다.

```html
<div class="container">
    @foreach ($users as $user)
        {{ $user->name }}
    @endforeach
</div>

{{ $users->links() }}
```

`links` 메소드는 결과 세트의 나머지 페이지에 대한 링크를 렌더링합니다. 이러한 각 링크에는 이미 적절한 `page` 쿼리 문자열 변수가 포함되어 있습니다. `link` 방식으로 생성된 HTML 은 [Tailwind CSS 프레임워크](https://tailwindcss.com)와 호환됩니다.

<a name="adjusting-the-pagination-link-window"></a>
### 페이지네이션 링크 창 조정

페이지네이션이 페이지네이션 링크를 표시하면 현재 페이지 번호, 앞, 뒤 페이지의 3페이지에 대한 링크가 표시됩니다. `onEachSide` 메서드를 사용하여 paginator 에 의해 생성된 링크의 중간 슬라이딩 창 내에서 현재 페이지의 각 면에 표시되는 추가 링크 수를 제어할 수 있습니다.

    {{ $users->onEachSide(5)->links() }}

<a name="converting-results-to-json"></a>
### 결과를 JSON 으로 변환

라라벨 페이네이터 클래스는 `Illuminate\Contracts\Support\Jsonable` 인터페이스 계약을 구현하고 `toJson` 메소드를 노출하므로 페이지네이션 결과를 JSON 으로 변환하는 것이 매우 쉽습니다. 라우트 또는 컨트롤러 작업에서 반환하여 paginator 인스턴스를 JSON 으로 변환할 수도 있습니다.

    use App\Models\User;

    Route::get('/users', function () {
        return User::paginate();
    });

paginator 의 JSON 에는 `total`, `current_page`, `last_page` 등과 같은 메타 정보가 포함됩니다. 결과 레코드는 JSON 배열의 `data` 키를 통해 사용할 수 있습니다. 다음은 경로에서 paginator 인스턴스를 반환하여 생성된 JSON 의 예입니다.

    {
       "total": 50,
       "per_page": 15,
       "current_page": 1,
       "last_page": 4,
       "first_page_url": "http://laravel.app?page=1",
       "last_page_url": "http://laravel.app?page=4",
       "next_page_url": "http://laravel.app?page=2",
       "prev_page_url": null,
       "path": "http://laravel.app",
       "from": 1,
       "to": 15,
       "data":[
            {
                // Record...
            },
            {
                // Record...
            }
       ]
    }

<a name="customizing-the-pagination-view"></a>
## 사용자 정의 페이지네이션 뷰 파일

기본적으로 페이지네이션 링크를 표시하도록 렌더링된 뷰는 [Tailwind CSS](https://tailwindcss.com) 프레임워크와 호환됩니다. 그러나 Tailwind 를 사용하지 않는 경우 이러한 링크들을 렌더링 하기 위한 고유한 뷰파일을 자유롭게 정의할 수 있습니다. paginator 인스턴스에서 `links` 메소드를 호출할 때 뷰 이름을 메소드의 첫 번째 인수로 전달할 수 있습니다.

    {{ $paginator->links('view.name') }}

    // Passing additional data to the view...
    {{ $paginator->links('view.name', ['foo' => 'bar']) }}

그러나 페이지네이션 뷰를 사용자 정의하는 가장 쉬운 방법은 `vendor:publish` 명령을 사용하여 `resources/views/vendor` 디렉토리로 내보내는 것입니다.

    php artisan vendor:publish --tag=laravel-pagination

이 명령은 애플리케이션의 `resources/views/vendor/pagination` 디렉토리에 옮겨넣습니다. 이 디렉토리 내의 `tailwind.blade.php` 파일은 기본 페이지네이션 뷰에 해당합니다. 이 파일을 편집하여 페이지네이션 HTML 을 수정할 수 있습니다.

다른 파일을 기본 페이지네이션 뷰로 지정하려면 `App\Providers\AppServiceProvider` 클래스의 `boot` 메소드 내에서 paginator 의 `defaultView` 및 `defaultSimpleView` 메소드를 호출할 수 있습니다.

    <?php

    namespace App\Providers;

    use Illuminate\Pagination\Paginator;
    use Illuminate\Support\Facades\Blade;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Paginator::defaultView('view-name');

            Paginator::defaultSimpleView('view-name');
        }
    }

<a name="using-bootstrap"></a>
### 부트스트랩 사용

라라벨에는 [Bootstrap CSS](https://getbootstrap.com/) 를 사용하여 빌드된 페이지네이션 뷰가 포함되어 있습니다. 기본 Tailwind 뷰 대신 이러한 뷰를 사용하려면 `App\Providers\AppServiceProvider` 클래스의 `boot` 메소드 내에서 paginator 의 `useBootstrap` 메소드를 호출할 수 있습니다.

    use Illuminate\Pagination\Paginator;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Paginator::useBootstrap();
    }

<a name="paginator-instance-methods"></a>
## Paginator 와 LengthAwarePaginator 인스턴스 메소드

각 Paginator 인스턴스는 다음 메서드를 통해 추가 페이지네이션 정보를 제공합니다.

메소드  |  설명
-------  |  -----------
`$paginator->count()`  |  현재 페이지의 항목 수를 가져옵니다.
`$paginator->currentPage()`  |  현재 페이지 번호를 가져옵니다.
`$paginator->firstItem()`  |  결과에서 첫 번째 항목의 결과 번호를 가져옵니다.
`$paginator->getOptions()`  |  페이지네이션의 옵션을 가져옵니다.
`$paginator->getUrlRange($start, $end)`  |  다양한 페이지네이션 URL을 만듭니다.
`$paginator->hasPages()`  |  여러 페이지로 분할하기에 충분한 항목이 있는지 확인합니다.
`$paginator->hasMorePages()`  |  데이터 저장소에 더 많은 항목이 있는지 확인합니다.
`$paginator->items()`  |  현재 페이지의 항목을 가져옵니다.
`$paginator->lastItem()`  |  결과의 마지막 항목의 결과 번호를 가져옵니다.
`$paginator->lastPage()`  |  마지막으로 사용 가능한 페이지의 페이지 번호를 가져옵니다. (`simplePaginate` 를 사용할 때는 사용할 수 없습니다.).
`$paginator->nextPageUrl()`  |  다음 페이지의 URL을 가져옵니다.
`$paginator->onFirstPage()`  |  paginator 가 첫 페이지에 있는지 확인합니다.
`$paginator->perPage()`  |  페이지 당 표시 할 항목 수입니다.
`$paginator->previousPageUrl()`  |  이전 페이지의 URL을 가져옵니다.
`$paginator->total()`  |  데이터 저장소에서 일치하는 항목의 총 개수를 가져옵니다. (`simplePaginate`를 사용할 때는 사용할 수 없습니다.)
`$paginator->url($page)`  |  지정된 페이지 번호의 URL을 가져옵니다.
`$paginator->getPageName()`  |  페이지를 저장하는 데 사용되는 쿼리 문자열 변수를 가져옵니다.
`$paginator->setPageName($name)`  |  페이지를 저장하는 데 사용되는 쿼리 문자열 변수를 설정합니다.

<a name="cursor-paginator-instance-methods"></a>
## Cursor Paginator 인스턴스 메소드

각 Cursor Paginator 인스턴스는 다음 메서드를 통해 추가 페이지네이션 정보를 제공합니다.

메소드  |  설명
-------  |  -----------
`$paginator->count()`  |  현재 페이지의 항목 수를 가져옵니다.
`$paginator->cursor()`  |  현재 cursor 인스턴스를 가져옵니다.
`$paginator->getOptions()`  |  Get the paginator options.
`$paginator->hasPages()`  |  여러 페이지로 분할하기에 충분한 항목이 있는지 확인합니다.
`$paginator->hasMorePages()`  |  데이터 저장소에 더 많은 항목이 있는지 확인합니다.
`$paginator->getCursorName()`  |  cursor 를 저장하는 데 사용되는 쿼리 문자열 변수를 가져옵니다.
`$paginator->items()`  |  현재 페이지의 항목을 가져옵니다.
`$paginator->nextCursor()`  |  다음 항목 세트에 대한 cursor 인스턴스를 가져옵니다.
`$paginator->nextPageUrl()`  |  다음 페이지의 URL을 가져옵니다.
`$paginator->onFirstPage()`  |  paginator 가 첫 페이지에 있는지 확인합니다.
`$paginator->perPage()`  |  페이지 당 표시 할 항목 수입니다.
`$paginator->previousCursor()`  |  이전 항목 세트에 대한 cursor 인스턴스를 가져옵니다.
`$paginator->previousPageUrl()`  |  이전 페이지의 URL을 가져옵니다.
`$paginator->setCursorName()`  |  cursor 를 저장할 쿼리 문자열 변수를 설정합니다.
`$paginator->url($cursor)`  |  지정된 cursor 인스턴스의 URL을 가져옵니다.
