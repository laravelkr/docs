# 데이터베이스 : 페이지네이션
- [시작하기](#introduction)
- [기본적인 사용법](#basic-usage)-
    - [쿼리 빌더 결과를 페이징 하기](#paginating-query-builder-results)
    - [Eloquent 결과를 페이징 하기](#paginating-eloquent-results)
    - [수동으로 Paginator 생성하기](#manually-creating-a-paginator)
- [페이지네이션 결과 출력하기](#displaying-pagination-results)
    - [페이지네이션 결과를 JSON으로 변환하기](#converting-results-to-json)
- [페이지네이션 뷰 파일 수정하기](#customizing-the-pagination-view)
- [페이지네이터 인스턴스 메소드](#paginator-instance-methods)

<a name="introduction"></a>
## 시작하기

다른 프레임 워크에서는 페이지 출력 처리를 하는것은 굉장히 성가신 일입니다. 라라벨의 페이지네이터는 별다른 설정없이도 [쿼리 빌더](/docs/{{version}}/queries) 와 [Eloquent ORM](/docs/{{version}}/eloquent)에 통합되어 있고, 간편하고 손쉽게 데이터베이스 결과를 페이지네이션 하는데 사용할 수 있습니다. 페이지네이터에 의해서 생성되는 HTML은 [부트스트랩 CSS 프레임워크](https://getbootstrap.com/)와 호환됩니다.

<a name="basic-usage"></a>
## 기본적인 사용법

<a name="paginating-query-builder-results"></a>
### 쿼리 빌더 결과를 페이징 하기

데이터들의 페이징을 처리 할 수 있는 몇가지 방법이 있습니다. 가장 쉬운 방법은 `paginate` 메소드를 [쿼리 빌더](/docs/{{version}}/queries) 혹은 [Eloquent 쿼리](/docs/{{version}}/eloquent)에서 사용하는 것 입니다. `paginate` 메소드는 자동으로 사용자가 확인하고 있는 현재의 페이지를 기준으로 하여 limit 과 offset을 결정합니다. 기본적으로 현재의 페이지는 HTTP 요청의 `page` 쿼리 스트링 인자 값에 의해서 결정됩니다. 이 값은 라라벨에 의해서 자동으로 확인되고, 또한 자동으로 paginator 에 의해서 생성되는 링크에 추가됩니다.

이 예제에서 `pagenate` 에 전달되는 인자는 여러분이 "한 페이지당" 표시하고자 하는 항목들의 개수 입니다. 이 경우에는 페이지당 `15`개의 항목들을 표시하도록 지정해 보겠습니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Support\Facades\DB;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Show all of the users for the application.
         *
         * @return Response
         */
        public function index()
        {
            $users = DB::table('users')->paginate(15);

            return view('user.index', ['users' => $users]);
        }
    }

> {note} 현재, 라라벨에서 groupBy 문을 사용하는 쿼리는 pagination 이 효율적으로 실행되지 않습니다. 만약 `groupBy`와 함께 paginate 를 사용해야 한다면, 수동으로 paginator 을 생성하여 쿼리를 질의하는것을 권장합니다.

#### "간단한 페이징 작업"

만약 "다음(Next)" 과 "이전(Prev)" 링크 만을 페이징 된 화면에서 보여주고자 한다면, 더 효율적으로 쿼리를 수행하는 `simplePaginate` 메소드를 사용할 수 있습니다. 이 메소드는 뷰를 렌더링 할 때 각 페이지 링크를 보여줄 필요가 없는 큰 규모의 데이터 셋을 처리할 때 유용합니다.

    $users = DB::table('users')->simplePaginate(15);

<a name="paginating-eloquent-results"></a>
### Eloquent 결과를 페이징 하기

또한 여러분은 [Eloquent](/docs/{{version}}/eloquent) 쿼리 에서도 페이징 처리를 할 수 있습니다. 다음 예제에서는 `User` 모델을 페이지 별로 `15`개로 페이지를 나누어 보여줄 것입니다. 보시다 시피 문법은 쿼리 빌더 결과를 페이징 하는 것과 같이 직관적입니다.

    $users = App\User::paginate(15);

`where` 절과 같은 조건을 쿼리에 지정한 후 `paginate` 를 호출 할 수 있습니다.

    $users = User::where('votes', '>', 100)->paginate(15);

또한 Elqouent 모델에 대해서 페이지를 구성할 때에도, `simplePaginate` 메소드를 사용할 수 있습니다.

    $users = User::where('votes', '>', 100)->simplePaginate(15);

<a name="manually-creating-a-paginator"></a>
### 수동으로 Paginator 생성하기

때로는 여러분은 항목들의 배열을 전달하면서 수동으로 pagination 인스턴스를 생성하고자 할 수도 있습니다. 필요에 따라 `Illuminate\Pagination\Paginator` 또는  `Illuminate\Pagination\LengthAwarePaginator` 인스턴스를 생성하여 구성할 수 있습니다.

`Paginator` 클래스는 결과 셋에 설정되어있는 항목의 총수를 알 필요는 없습니다. 따라서 클래스 마지막 페이지의 인덱스를 가져오는 메소드를 가지고 있지 않습니다. 그에 반해서 `LengthAwarePaginator` 는 `Paginator` 과 거의 같은 인자를 전달 받지만, 결과 셋에 설정되어있는 전체 항목의 개수를 필요로 합니다.

다시 말해, `Paginator` 는 쿼리 빌더와 Eloquent에 대한 `simplePaginate` 메소드에 대응 되고, `LengthAwarePaginator` 는 paginate 에 대응하고 있습니다.

> {note} 수동으로 paginator 인스턴스를 생성할 때에는, 직접 paginator 에 전달되는 결과 배열을 "잘라" 주어야만 합니다. 어떻게 해야할지 잘 모르겠다면, [array_slice](https://secure.php.net/manual/en/function.array-slice.php) PHP 함수를 참고하십시오.

<a name="displaying-pagination-results"></a>
## 페이지네이션 결과 출력하기

`paginate` 메소드를 호출하면, 여러분은 `Illuminate\Pagination\LengthAwarePaginator` 인스턴스를 전달 받습니다. `simplePaginate` 메소드를 호출 할 때에는 `Illuminate\Pagination\Paginator`의 인스턴스를 전달 받습니다. 이러한 인스턴스들은 결과 셋을 나타내는 다양한 메소드를 제공합니다. 이러한 헬퍼 메소드를 제공하는 것 외에도 paginator 인스턴스는 iterators 이며 배열처럼 반복문에서 사용할 수 있습니다. 따라서 결과를 얻은 뒤에, 그 결과와 페이지 링크를 [Blade](/docs/{{version}}/blade)에서 사용할 수 있습니다.

    <div class="container">
        @foreach ($users as $user)
            {{ $user->name }}
        @endforeach
    </div>

    {{ $users->links() }}

`links` 메소드는 결과 셋에서 페이지 링크를 렌더링 할 것입니다. 각각의 링크에는 이미 `page` 쿼리 스트링 변수가 포함되어 있을 것입니다. `links` 메소드에 의해서 생성되는 HTML은 [Bootstrap CSS framework](https://getbootstrap.com)와 호환된 다는 것을 기억하십시오.

#### 사용자 지정 Paginator URI 구성하기

`withPath` 메소드는 paginator 가 링크를 생성할 때 사용자가 지정한 URI를 구성할 수 있게 해줍니다. 예를 들어 paginator 가 `http://example.com/custom/url?page=N`와 같은 링크를 생성하게 하려면 `withPath` 메소드 인자에 `custom/url` 을 전달해야 합니다.

    Route::get('users', function () {
        $users = App\User::paginate(15);

        $users->withPath('custom/url');

        //
    });

#### 페이지 링크에 추가하기

`appends` 메소드를 사용하면 페이지 링크에 쿼리 스트링을 추가할 수 있습니다. 예를 들어, 각각의 페이지 링크에 `&sort=votes`를 추가하려면, 다음과 같이 `appends` 를 호출해야 합니다.

    {{ $users->appends(['sort' => 'votes'])->links() }}

만약 "hash fragment"를 페이지 번호의 링크에 추가하고 싶다면 `fragment` 메소드를 사용하면 됩니다. 예를 들어 각각의 페이지 링크에 `#foo`를 추가하고자 한다면, 다음과 같이 `fragment` 메소드를 호출하면 됩니다.

    {{ $users->fragment('foo')->links() }}

#### 페이지네이션 링크 창 조정하기

paginator가 URL "창"의 각 사이드에 표시하는 추가 링크의 수를 변경 할 수 있습니다. 기본적으로 세 개의 링크가 기본 paginator 링크의 각 사이드에 표시됩니다. 그러나 `onEachSide` 메소드를 사용하여 이 숫자를 변경 할 수 있습니다 :

    {{ $users->onEachSide(5)->links() }}

<a name="converting-results-to-json"></a>
### 결과를 JSON으로 변환하기

라라벨의 paginator 결과 클래스는 `Illuminate\Contracts\Support\Jsonable` 인터페이스 contract을 구현하고 `toJson` 메소드를 제공하고 있어서, pagination 결과를 JSON으로 아주 쉽게 변환할 수 있습니다. 또한 라우트나 컨트롤러 액션에서 paginator 인스턴스를 JSON으로 변환할 수도 있습니다.

    Route::get('users', function () {
        return App\User::paginate();
    });

paginator로 부터 변환된 JSON은 `total`, `current_page`, `last_page` 및 여러가지의 메타정보를 포함하고 있을 것입니다. 실제 결과 객체는 JSON 배열의 `data` 키를 통해서 사용이 가능합니다. 다음은 라우트에서 paginator 인스턴스를 반환하여 생성된 JSON 의 예제 입니다.

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
                // Result Object
            },
            {
                // Result Object
            }
       ]
    }

<a name="customizing-the-pagination-view"></a>
## 페이지네이션 뷰 파일 수정하기

기본적으로 페이지네이션 링크를 출력하기 위해서 렌더링 하는 뷰파일들은 부트스트랩 CSS 프레임워크와 호환됩니다. 하지만 부트스트랩을 사용하지 않는다면, 이 링크들을 렌더링 하기 위한 고유한 뷰파일을 자유롭게  정의할 수 있습니다. 페이지네이터 인스턴스에서 `links` 메소드를 호출할 때, 메소드의 첫번째 인자로 뷰파일의 이름을 전달하면 됩니다.

    {{ $paginator->links('view.name') }}

    // Passing data to the view...
    {{ $paginator->links('view.name', ['foo' => 'bar']) }}

하지만 페이지네이션 뷰를 수정하는데 보다 손쉬운 방법은 뷰파일들을 `vendor:publish` 명령어를 사용하여 `resources/views/vendor` 디렉토리로 내보내는 것입니다.

    php artisan vendor:publish --tag=laravel-pagination

이 명령어는 뷰파일들을 `resources/views/vendor/pagination`로 옮겨넣습니다. 이 디렉토리 안에 있는 `bootstrap-4.blade.php` 파일은 페이지네이션 기본 뷰와 일치합니다. 페이지네이션 HTML을 수정하려면 이 파일을 편집하면 됩니다.

다른 파일을 기본 페이지네이션 뷰로 사용하고자 한다면, `AppServiceProvider` 파일안에서 페이지네이터의 `defaultView` 그리고 `defaultSimpleView`메소드를 사용하면 됩니다.

    use Illuminate\Pagination\Paginator;

    public function boot()
    {
        Paginator::defaultView('view-name');

        Paginator::defaultSimpleView('view-name');
    }

<a name="paginator-instance-methods"></a>
## 페이지네이터 인스턴스 메소드

각각의 페이지네이터 인스턴스는 다음의 메소드를 통해서 추가적인 페이지네이션 정보를 제공합니다.

메소드  |  설명
-------  |  -----------
`$results->count()`  |  현재 페이지의 항목 수를 가져옵니다.
`$results->currentPage()`  |  현재 페이지 번호를 가져옵니다.
`$results->firstItem()`  |  결과에서 첫 번째 항목의 결과 번호를 가져옵니다.
`$results->getOptions()`  |  페이지네이션의 옵션을 가져옵니다.
`$results->getUrlRange($start, $end)`  |  다양한 페이지네이션 URL을 만듭니다.
`$results->hasMorePages()`  |  여러 페이지로 분할하기에 충분한 항목이 있는지 확인합니다.
`$results->items()`  |  현재 페이지의 항목을 가져옵니다.
`$results->lastItem()`  |  결과의 마지막 항목의 결과 번호를 가져옵니다.
`$results->lastPage()`  |  마지막으로 사용 가능한 페이지의 페이지 번호를 가져옵니다. (`simplePaginate`를 사용할 때는 사용할 수 없습니다.)
`$results->nextPageUrl()`  |  다음 페이지의 URL을 가져옵니다.
`$results->onFirstPage()`  |  페이지네이터가 첫 페이지에 있는지 확인합니다.
`$results->perPage()`  |  페이지 당 표시 할 항목 수입니다.
`$results->previousPageUrl()`  |  이전 페이지의 URL을 가져옵니다.
`$results->total()`  |  데이터 저장소에서 일치하는 항목의 총 개수를 가져옵니다. (`simplePaginate`를 사용할 때는 사용할 수 없습니다.)
`$results->url($page)`  |  지정된 페이지 번호의 URL을 가져옵니다.
