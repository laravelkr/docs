# 데이터베이스: 쿼리 빌더

- [시작하기](#introduction)
- [데이터베이스 쿼리 실행하기](#running-database-queries)
    - [결과 분활 하기](#chunking-results)
    - [결과 지연 스트리밍](#streaming-results-lazily)
    - [Aggregates-집계문](#aggregates)
- [Select-선택 구문](#select-statements)
- [Raw 표현식](#raw-expressions)
- [Joins-조인](#joins)
- [Unions-유니온](#unions)
- [기본 Where 절](#basic-where-clauses)
    - [Where 절](#where-clauses)
    - [Or Where 절](#or-where-clauses)
    - [JSON Where 절](#json-where-clauses)
    - [추가 Where 절](#additional-where-clauses)
    - [논리적 그룹화](#logical-grouping)
- [보다 복잡한 Where 절](#advanced-where-clauses)
    - [존재여부를 판단하는 Where 절](#where-exists-clauses)
    - [서브쿼리 Where 절](#subquery-where-clauses)
- [Ordering, Grouping, Limit & Offset](#ordering-grouping-limit-and-offset)
    - [Ordering](#ordering)
    - [Grouping](#grouping)
    - [Limit & Offset](#limit-and-offset)
- [Conditional-조건적 where 절](#conditional-clauses)
- [Insert-삽입 구문](#insert-statements)
    - [Upserts](#upserts)
- [Update-수정 구문](#update-statements)
    - [JSON 컬럼 업데이트](#updating-json-columns)
    - [Increment-증가 & Decrement-감소](#increment-and-decrement)
- [Delete-삭제 구문](#delete-statements)
- [Pessimistic Locking](#pessimistic-locking)
- [디버깅](#debugging)

<a name="introduction"></a>
## 시작하기

라라벨의 데이터베이스 쿼리 빌더는 데이터베이스 쿼리들을 만들고 운영하는데 다양한 인터페이스의 편의 기능을 제공합니다. 애플리케이션에서 대부분의 데이터베이스 작업을 수행하는 데 사용할 수 있으며 Laravel에서 지원하는 모든 데이터베이스 시스템과 완벽하게 작동합니다.

라라벨의 쿼리 빌더는 PDO 파라미터 바인딩을 사용하여 SQL injection 공격을 방지합니다. 따라서 쿼리 빌더에 쿼리 바인딩으로 전달된 문자열을 정리할 필요가 없습니다.

> {note} PDO는 컬럼명 바인딩을 지원하지 않습니다. 따라서 "order by" 컬럼을 포함하여 사용자 입력이 쿼리에서 참조하는 컬럼 이름을 지시하도록 허용해서는 안됩니다.

<a name="running-database-queries"></a>
## 데이터베이스 쿼리 실행하기

<a name="retrieving-all-rows-from-a-table"></a>
#### 한 테이블에서 모든 행들을 가져오기

`DB` 파사드에서 제공하는 `table` 메소드를 사용하여 쿼리를 시작할 수 있습니다. `table` 메소드는 주어진 테이블에 대한 쿼리 빌더 인스턴스를 반환하고, 다양한 제약을 메소드 체이닝 형태로 구성할 수 있으며, 마지막으로 `get` 메소드를 사용하여 쿼리 결과를 검색할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Support\Facades\DB;

    class UserController extends Controller
    {
        /**
         * Show a list of all of the application's users.
         *
         * @return \Illuminate\Http\Response
         */
        public function index()
        {
            $users = DB::table('users')->get();

            return view('user.index', ['users' => $users]);
        }
    }

`get` 메소드는 각각의 결과가 PHP `stdClass` 객체의 인스턴스로 구성된 쿼리의 결과를 `Illuminate\Support\Collection`의 인스턴스로 반환합니다. 객체의 속성(property)으로 컬럼에 접근하여 각 컬의 값에 액세스할 수 있습니다.

    use Illuminate\Support\Facades\DB;

    $users = DB::table('users')->get();

    foreach ($users as $user) {
        echo $user->name;
    }

> {tip} 라라벨 컬렉션은 데이터를 매핑하고 축소를 위한 매우 강력한 메소드를 다양하게 제공합니다 라라벨 컬렉션의 더 많은 정보를 확인하고 싶다면 [컬렉션 문서](/docs/{{version}}/collections)를 확인하세요.

<a name="retrieving-a-single-row-column-from-a-table"></a>
#### 테이블에서 하나의 결과 / 컬럼 가져오기

데이터베이스 테이블에서 하나의 row을 가져오고자 한다면, `DB` 파사드의 `first` 메소드를 사용하면 됩니다. 이 메소드는 하나의 `stdClass` 객체를 반환할 것입니다.

    $user = DB::table('users')->where('name', 'John')->first();

    return $user->email;

전체 row 가 필요하지 않다면, `value` 메소드를 사용하여 레코드에서 하나의 값만 추출할 수 있습니다. 메소드는 컬럼의 직접적인 값을 반환할 것입니다.

    $email = DB::table('users')->where('name', 'John')->value('email');

`id` 컬럼 값으로 단일 행을 검색하려면 `find` 메소드를 사용하십시오.

    $user = DB::table('users')->find(3);

<a name="retrieving-a-list-of-column-values"></a>
#### 컬럼 값의 목록 조회하기

한개의 컬럼의 값들을 포함하고 있는 `Illuminate\Support\Collection`의 인스턴스를 조회하고자 한다면, `pluck` 메소드를 사용할 수 있습니다. 이 예제에서는 유저의 타이틀 컬렉션을 조회할 것입니다.

    use Illuminate\Support\Facades\DB;

    $titles = DB::table('users')->pluck('title');

    foreach ($titles as $title) {
        echo $title;
    }

`pluck` 메소드에 두 번째 인수를 입력하여 결과 컬렉션이 키로 사용하는 컬럼을 지정할 수 있습니다 .

    $titles = DB::table('users')->pluck('title', 'name');

    foreach ($titles as $name => $title) {
        echo $title;
    }

<a name="chunking-results"></a>
### 결과 분할하기

데이터베이스 레코드가 많은 작업을 수행해야 한다면, `DB` 파사드가 제공하는 `chunk` 메소드를 사용하는 것을 고려하십시오. 이 메소드는 한번에 결과에 대한 하나의 작은 청크로 획득하고, 각각의 청크를 `Closure` 를 통해서 처리합니다. 예제로 `users` 테이블을 한번에 100개의 레코드 청크로 전체 검색해 보겠습니다.

    use Illuminate\Support\Facades\DB;

    DB::table('users')->orderBy('id')->chunk(100, function ($users) {
        foreach ($users as $user) {
            //
        }
    });

클로저에서 `false`를 반환하여, 더이상의 청크를 처리하지 않도록 중단할 수 있습니다.

    DB::table('users')->orderBy('id')->chunk(100, function ($users) {
        // Process the records...

        return false;
    });

결과를 청킹 하는 동안 데이터베이스 레코드를 업데이트하는 경우 청크 결과가 예상치 못한 방식으로 변경될 수 있습니다. 청킹 중 검색된 레코드를 업데이트 하려는 경우 항상 `chunkById` 메소드를 사용하는 것이 좋습니다. 이 메소드는 레코드의 기본 키를 기반으로 결과에 자동으로 페이징 합니다.

    DB::table('users')->where('active', false)
        ->chunkById(100, function ($users) {
            foreach ($users as $user) {
                DB::table('users')
                    ->where('id', $user->id)
                    ->update(['active' => true]);
            }
        });

> {note} 청크 콜백 내에서 레코드를 업데이트하거나 삭제할 때 기본 키나 외래 키를 변경하면 청크 쿼리에 영향을 줄 수 있습니다. 이로 인해 잠재적으로 레코드가 청크 결과에 포함되지 않을 수 있습니다.

<a name="streaming-results-lazily"></a>
### 결과 지연 스트리밍

`lazy` 메소드는 쿼리를 청크로 실행한다는 점에서 [`chunk` 메소드](#chunking-results)와 유사하게 작동합니다. 그러나 `lazy()` 메소드는 각 청크를 콜백으로 전달하는 대신 결과를 단일 스트림으로 상호 작용할 수 있도록 [`LazyCollection`](/docs/{{version}}/collections#lazy-collections)으로 반환합니다.

```php
use Illuminate\Support\Facades\DB;

DB::table('users')->orderBy('id')->lazy()->each(function ($user) {
    //
});
```

또 다시 레코드를 반복하면서 업데이트하려는 경우 `lazyById` 또는 `lazyByIdDesc` 메소드를 사용하는 것이 좋습니다. 이 메소드들은 레코드의 기본 키를 기반으로 결과에 자동으로 페이징 합니다.

```php
DB::table('users')->where('active', false)
    ->lazyById()->each(function ($user) {
        DB::table('users')
            ->where('id', $user->id)
            ->update(['active' => true]);
    });
```

> {note} 레코드를 반복하며 업데이트하거나 삭제할 때 기본 키나 외래 키를 변경하면 청크 쿼리에 영향을 줄 수 있습니다. 이로 인해 잠재적으로 레코드가 결과에 포함되지 않을 수 있습니다.

<a name="aggregates"></a>
### Aggregates-집계문

쿼리 빌더는 `count`, `max`, `min`, `avg`,`sum`과 같은 집계 값을 검색하는 다양한 메소드를 제공합니다. 이 메소드들은 쿼리를 생성한 뒤에 호출하면 됩니다.

    use Illuminate\Support\Facades\DB;

    $users = DB::table('users')->count();

    $price = DB::table('orders')->max('price');

당연하게도 다른 절과 결합하여 집계 값이 계산되는 방식을 미세 조정할 수 있습니다.

    $price = DB::table('orders')
                    ->where('finalized', 1)
                    ->avg('price');

<a name="determining-if-records-exist"></a>
#### 레코드가 존재하는지 확인하기

쿼리 조건에 맞는 레코드가 존재하는지 확인하기 위해서 `count` 메소드를 사용하는 대신에, `exists` 와 `doesntExist` 메소드를 사용할 수 있습니다.

    if (DB::table('orders')->where('finalized', 1)->exists()) {
        // ...
    }

    if (DB::table('orders')->where('finalized', 1)->doesntExist()) {
        // ...
    }

<a name="select-statements"></a>
## Select-선택 구문

<a name="specifying-a-select-clause"></a>
#### Select 절 지정하기

항상 데이터베이스의 테이블에서 모든 컬럼을 조회하지는 않을 것입니다. `select` 메소드를 사용하여 쿼리에 대한 사용자 정의 "select" 절을 지정할 수 있습니다.

    use Illuminate\Support\Facades\DB;

    $users = DB::table('users')
                ->select('name', 'email as user_email')
                ->get();

`distinct` 메소드는 쿼리가 고유한 결과를 반환하도록 강제할 수 있습니다.

    $users = DB::table('users')->distinct()->get();

이미 쿼리 빌더 인스턴스를 가지고 있고 존재하는 select 절에 선택할 컬럼을 추가하려면, `addSelect` 메소드를 사용할 수 있습니다.

    $query = DB::table('users')->select('name');

    $users = $query->addSelect('age')->get();

<a name="raw-expressions"></a>
## Raw 표현식

때로는 쿼리에 임의의 문자열을 삽입해야 하는 경우도 있습니다. raw 문자 표현식을 생성하기 위해 `DB` 파사드가 제공하는 `raw` 메소드를 사용할 수 있습니다

    $users = DB::table('users')
                 ->select(DB::raw('count(*) as user_count, status'))
                 ->where('status', '<>', 1)
                 ->groupBy('status')
                 ->get();

> {note} 이러한 구문들은 쿼리를 문자열 형태로 주입하기 때문에, SQL 인젝션에 취약하지 않도록 특별히 주의해야 합니다!

<a name="raw-methods"></a>
### Raw 메소드

`DB::raw` 를 사용하는 대신, 쿼리의 다양한 부분을 raw 표현식으로 대체하기 위해서 다음의 메소드를 사용할 수 있습니다. **라라벨은 raw 표션식을 사용하는 쿼리가 SQL 인젝션 취약성으로부터 보호된다는 것을 보장할 수 없습니다.**

<a name="selectraw"></a>
#### `selectRaw`

`selectRaw` 메소드는 `addSelect(DB::raw(...))` 대신 사용할 수 있습니다. 이 메소드는 옵션 배열을 두번째 인자로 받습니다.

    $orders = DB::table('orders')
                    ->selectRaw('price * ? as price_with_tax', [1.0825])
                    ->get();

<a name="whereraw-orwhereraw"></a>
#### `whereRaw / orWhereRaw`

`whereRaw` 와 `orWhereRaw` 메소드는 쿼리에 raw "where" 절을 삽입하는데 사용할 수 있습니다. 이 메소드는 두번째 인자로 (옵션값) 바인딩 배열을 받을 수 있습니다.

    $orders = DB::table('orders')
                    ->whereRaw('price > IF(state = "TX", ?, 100)', [200])
                    ->get();

<a name="havingraw-orhavingraw"></a>
#### `havingRaw / orHavingRaw`

`havingRaw` 와 `orHavingRaw` 메소드는 `having` 절의 값으로 raw 문자열을 제공하는데 사용됩니다. 이 메소드는 두번째 인자로 (옵션값) 바인딩 배열을 받을 수 있습니다.

    $orders = DB::table('orders')
                    ->select('department', DB::raw('SUM(price) as total_sales'))
                    ->groupBy('department')
                    ->havingRaw('SUM(price) > ?', [2500])
                    ->get();

<a name="orderbyraw"></a>
#### `orderByRaw`

`orderByRaw` 메소드는 `order by` 절의 값을 raw 한 문자열로 제공하는데 사용합니다.

    $orders = DB::table('orders')
                    ->orderByRaw('updated_at - created_at DESC')
                    ->get();

<a name="groupbyraw"></a>
### `groupByRaw`

`groupByRaw` 메소드는 원시 문자열을 `group by` 절의 값으로 제공하는 데 사용될 수 있습니다.

    $orders = DB::table('orders')
                    ->select('city', 'state')
                    ->groupByRaw('city, state')
                    ->get();

<a name="joins"></a>
## Joins-조인

<a name="inner-join-clause"></a>
#### Inner Join 절

쿼리 빌더를 사용해서 쿼리에 조인 절(join clauses)을 추가할 수 있습니다. 기본적인 "inner join" 을 수행하기 위해서는 쿼리 빌더 인스턴스의 `join` 메소드를 사용하면 됩니다. `join` 메소드에 전달된 첫 번째 인수는 조인해야 하는 테이블의 이름이고 나머지 인수는 조인에 대한 컬럼 제약 조건을 지정합니다. 단일 쿼리에서 여러 테이블을 조인할 수도 있습니다.

    use Illuminate\Support\Facades\DB;

    $users = DB::table('users')
                ->join('contacts', 'users.id', '=', 'contacts.user_id')
                ->join('orders', 'users.id', '=', 'orders.user_id')
                ->select('users.*', 'contacts.phone', 'orders.price')
                ->get();

<a name="left-join-right-join-clause"></a>
#### Left Join / Right Join 절

"inner join" 대신 "left join" 또는 "right join" 을 수행하고자 한다면, `leftJoin` 또는 `rightJoin` 메소드를 사용하십시오. 이 메소드들은 `join` 메소드와 동일한 구성을 가집니다.

    $users = DB::table('users')
                ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
                ->get();

    $users = DB::table('users')
                ->rightJoin('posts', 'users.id', '=', 'posts.user_id')
                ->get();

<a name="cross-join-clause"></a>
#### Cross Join 절

`crossJoin` 메소드를 사용하여 "cross Join"을 수행할 수 있습니다. cross join은 첫 번째 테이블과 조인된 테이블 사이의 곱집합(cartesian product)을 생성합니다.

    $sizes = DB::table('sizes')
                ->crossJoin('colors')
                ->get();

<a name="advanced-join-clauses"></a>
#### 보다 복잡한 Join 절

보다 복잡한 join 절도 지정할 수 있습니다. 시작하려면 `join` 메소드의 두 번째 인자로 클로저를 전달하십시오. 클로저는 `Illuminate\Database\Query\JoinClause`의 인스턴스를 전달받아 `join` 절에 제약사항을 지정할 것입니다.

    DB::table('users')
            ->join('contacts', function ($join) {
                $join->on('users.id', '=', 'contacts.user_id')->orOn(...);
            })
            ->get();

join에 "where" 절을 사용 하려면 `JoinClause` 인스턴스가 제공하는 `where`와 `orWhere` 메소드를 사용할 수 있습니다. 두개의 컬럼을 비교하는 대신 두 메소드는 컬럼을 값과 비교합니다.


    DB::table('users')
            ->join('contacts', function ($join) {
                $join->on('users.id', '=', 'contacts.user_id')
                     ->where('contacts.user_id', '>', 5);
            })
            ->get();

<a name="subquery-joins"></a>
#### 서브쿼리 조인

쿼리와 서브쿼리의 조인을 위해서 `joinSub`, `leftJoinSub`, `rightJoinSub` 메소드를 사용할 수 있습니다. 각각의 메소드는 서브쿼리, 테이블의 별칭(alias), 연관된 컬럼을 정의하는 클로저의 세 가지 인자를 전달 받습니다. 이 예제에서는 각 유저 레코드에 유저가 가장 최근에 게시한 블로그 게시물의 'created_at' 타임스탬프가 포함된 유저 컬렉션을 검색합니다.

    $latestPosts = DB::table('posts')
                       ->select('user_id', DB::raw('MAX(created_at) as last_post_created_at'))
                       ->where('is_published', true)
                       ->groupBy('user_id');

    $users = DB::table('users')
            ->joinSub($latestPosts, 'latest_posts', function ($join) {
                $join->on('users.id', '=', 'latest_posts.user_id');
            })->get();

<a name="unions"></a>
## Unions-유니온

쿼리 빌더는 두 개 또는 그 이상의 쿼리를 "union" 하는 간편한 방법을 제공합니다. 다음과 같이 초기화한 쿼리를 생성하고 추가 쿼리를 결합하기 위하여 `union` 메소드를 사용할 수 있습니다.

    use Illuminate\Support\Facades\DB;

    $first = DB::table('users')
                ->whereNull('first_name');

    $users = DB::table('users')
                ->whereNull('last_name')
                ->union($first)
                ->get();

`union` 메소드 외에도 쿼리 빌더는 `unionAll` 메소드를 제공합니다. `unionAll` 메소드를 사용하여 결합된 쿼리는 중복 결과가 제거되지 않습니다. `unionAll` 메소드는 `union` 메소드와 동일하게 사용할수 있습니다.

<a name="basic-where-clauses"></a>
## 기본 Where 절

<a name="where-clauses"></a>
### Where 절

쿼리 빌더의 `where` 메소드를 사용하여 쿼리에 `where` 절을 추가할 수 있습니다. 가장 기본적인 `where` 메소드 호출 방법에는 3가지 인자가 필요합니다. 첫번째 인자는 컬럼의 이름입니다. 두번째 인자는 데이터베이스가 지원하는 연산자입니다. 마지막으로, 세번째 인자는 컬럼값과 비교할 값입니다.

예를 들어 다음 쿼리는 `votes` 컬럼의 값이 `100` 이고 `age` 컬럼의 값이 `35` 보다 큰 유저를 검색합니다.

    $users = DB::table('users')
                    ->where('votes', '=', 100)
                    ->where('age', '>', 35)
                    ->get();

보다 편리하게 사용하기 위해서, 컬럼이 주어진 값에 대해 `=` 인지 확인하려면, `where` 메소드에 두 번째 인수로 전달할 수 있습니다. 라라벨은 `=` 연산자를 사용할 것으로 가정합니다.

    $users = DB::table('users')->where('votes', 100)->get();

앞서 언급했듯이 데이터베이스 시스템에서 지원하는 모든 연산자를 사용할 수 있습니다.

    $users = DB::table('users')
                    ->where('votes', '>=', 100)
                    ->get();

    $users = DB::table('users')
                    ->where('votes', '<>', 100)
                    ->get();

    $users = DB::table('users')
                    ->where('name', 'like', 'T%')
                    ->get();

`where` 함수에 조건 배열을 전달할 수도 있습니다. 배열의 각 요소는 일반적으로 `where` 메소드에 전달되는 세 개의 인수로 이루어진 배열이어야 합니다.

    $users = DB::table('users')->where([
        ['status', '=', '1'],
        ['subscribed', '<>', '1'],
    ])->get();

> {note} PDO는 바인딩 컬럼 이름을 지원하지 않습니다. 따라서 "order by" 컬럼을 포함하여 쿼리에서 참조하는 컬럼 이름을 사용자 입력이 지시하도록 허용해서는 안 됩니다.

<a name="or-where-clauses"></a>
### Or Where 절

쿼리 빌더의 `where` 메소드를 체이닝 할때 "where" 절은 `and` 연산자를 사용하여 결합됩니다. 하지만 `orWhere` 메소드를 사용하여 `or` 연산자로 쿼리에 절을 결합할 수 있습니다. `orWhere` 메소드는 `where` 메소드와 동일한 인수를 받습니다.

    $users = DB::table('users')
                        ->where('votes', '>', 100)
                        ->orWhere('name', 'John')
                        ->get();

괄호 안에 "or"조건을 그룹화해야하는 경우 `orWhere` 메소드의 첫 번째 인수로 클로저를 전달할 수 있습니다.

    $users = DB::table('users')
                ->where('votes', '>', 100)
                ->orWhere(function($query) {
                    $query->where('name', 'Abigail')
                          ->where('votes', '>', 50);
                })
                ->get();

위의 예제는 아래의 SQL 문을 생성합니다.

```sql
select * from users where votes > 100 or (name = 'Abigail' and votes > 50)
```

> {tip} 글로벌 스코프가 적용될 때 예상치 못한 동작이 실행되는 것을 방지하기 위해서 항상 `orWhere` 그룹을 호출해야합니다.

<a name="json-where-clauses"></a>
### JSON Where 절

라라벨은 JSON 컬럼 타입을 지원하는 데이터베이스의 JSON 컬럼 타입 쿼리를 지원합니다. 현재는 MySQL 5.7 이상, PostgreSQL, SQL Server 2016, 그리고 SQLite 3.9.0 ([JSON1 extension](https://www.sqlite.org/json1.html)과 함께)에 포함되어 있습니다. JSON 컬럼 쿼리를 하기 위해서는 `->` 연산자를 사용하십시오:

    $users = DB::table('users')
                    ->where('preferences->dining->meal', 'salad')
                    ->get();

JSON 배열을 쿼리하기 위해 `whereJsonContains` 를 사용할 수 있습니다. 이 기능은 SQLite 데이터베이스에서는 지원하지 않습니다.

    $users = DB::table('users')
                    ->whereJsonContains('options->languages', 'en')
                    ->get();

어플리케이션이 MySQL 이나 PostgreSQL 데이터베이스를 사용한다면 `whereJsonContains` 메소드에 배열 값을 전달할 수 있습니다.

    $users = DB::table('users')
                    ->whereJsonContains('options->languages', ['en', 'de'])
                    ->get();

`whereJsonLength` 메소드 를 사용하여 JSON 배열을 길이에 따라 쿼리 할 수 있습니다.

    $users = DB::table('users')
                    ->whereJsonLength('options->languages', 0)
                    ->get();

    $users = DB::table('users')
                    ->whereJsonLength('options->languages', '>', 1)
                    ->get();

<a name="additional-where-clauses"></a>
### 추가 Where 절

**whereBetween / orWhereBetween**

`whereBetween` 메소드는 컬럼의 값이 두개의 값 사이의 값인지 확인합니다.

    $users = DB::table('users')
               ->whereBetween('votes', [1, 100])
               ->get();

**whereNotBetween / orWhereNotBetween**

`whereNotBetween` 메소드는 컬럼의 값이 두개의 값 사이가 아닌지 확인합니다.

    $users = DB::table('users')
                        ->whereNotBetween('votes', [1, 100])
                        ->get();

**whereIn / whereNotIn / orWhereIn / orWhereNotIn**

`whereIn` 메소드는 주어진 컬럼의 값이 주어진 배열안에 값에 속해 있는지 확인합니다.

    $users = DB::table('users')
                        ->whereIn('id', [1, 2, 3])
                        ->get();

`whereNotIn` 메소드는 주어진 컬럼의 값이 주어진 배열안에 포함되어 있지 않은지 확인합니다.

    $users = DB::table('users')
                        ->whereNotIn('id', [1, 2, 3])
                        ->get();

> {note} 쿼리에 큰 정수 배열을 추가하는 경우 `whereIntegerInRaw` 또는 `whereIntegerNotInRaw` 메서드를 사용하여 메모리 사용량을 크게 줄일 수 있습니다.

**whereNull / whereNotNull / orWhereNull / orWhereNotNull**

`whereNull` 메소드는 주어진 컬럼의 값이 `NULL` 인지 확인합니다.

    $users = DB::table('users')
                    ->whereNull('updated_at')
                    ->get();

`whereNotNull` 메소드는 주어진 컬럼의 값이 `NULL`이 아닌지 확인합니다.

    $users = DB::table('users')
                    ->whereNotNull('updated_at')
                    ->get();

**whereDate / whereMonth / whereDay / whereYear / whereTime**

`whereDate` 메소드는 컬럼의 값이 date 값인지 비교하는데 사용됩니다.

    $users = DB::table('users')
                    ->whereDate('created_at', '2016-12-31')
                    ->get();

`whereMonth` 메소드는 컬럼의 값이 지정된 달과 같은지 비교하는데 사용됩니다.

    $users = DB::table('users')
                    ->whereMonth('created_at', '12')
                    ->get();

`whereDay` 메소드는 컬럼의 값이 한달의 지정된 일과 같은지 비교하는데 사용됩니다.

    $users = DB::table('users')
                    ->whereDay('created_at', '31')
                    ->get();

`whereYear` 메소드는 컬럼의 값이 지정된 년도와 같은지 비교하는데 사용됩니다.

    $users = DB::table('users')
                    ->whereYear('created_at', '2016')
                    ->get();

`whereTime` 메소드는 컬럼의 값을 특정 시간과 비교하는데 사용할 수 있습니다.

    $users = DB::table('users')
                    ->whereTime('created_at', '=', '11:20:45')
                    ->get();

**whereColumn / orWhereColumn**

`whereColumn` 메소드는 두개의 컬럼이 동일하는 것을 확인하는데 사용할 수 있습니다.

    $users = DB::table('users')
                    ->whereColumn('first_name', 'last_name')
                    ->get();

또한 비교 연산자를 `whereColumn` 메소드에 전달할 수도 있습니다.

    $users = DB::table('users')
                    ->whereColumn('updated_at', '>', 'created_at')
                    ->get();

컬럼 비교식 배열을 `whereColumn` 메서드에 전달할 수도 있습니다. 이 조건들은 `and` 연산자를 사용하여 연결됩니다.

    $users = DB::table('users')
                    ->whereColumn([
                        ['first_name', '=', 'last_name'],
                        ['updated_at', '>', 'created_at'],
                    ])->get();

<a name="logical-grouping"></a>
### 논리적 그룹화

쿼리의 원하는 논리적 그룹화를 달성하기 위해 괄호 안에 여러 "where" 절을 그룹화해야 하는 경우가 있습니다. 예상하지 않은 쿼리 동작을 방지하기 `orWhere` 메소드는 일반적으로 항상 괄호 안에 그룹화해야 합니다. 이를 위해 `where` 메소드에 클로저를 전달할 수 있습니다.

    $users = DB::table('users')
               ->where('name', '=', 'John')
               ->where(function ($query) {
                   $query->where('votes', '>', 100)
                         ->orWhere('title', '=', 'Admin');
               })
               ->get();

위에서 보시다 시피, `where` 메소드에 전달된 하나의 클로저가 쿼리 빌더의 제약조건을 그룹으로 묶고 있습니다. 이 클로저는 괄호로 포함된 제약조건을 설정하는데 사용할 쿼리 빌더 인스턴스를 전달받습니다. 위 예제는 다음과 같은 SQL을 생성할 것입니다.

```sql
select * from users where name = 'John' and (votes > 100 or title = 'Admin')
```

> {note} 글로벌 스코프가 적용될 때 예상치 못한 동작이 실행되는 것을 방지하기 위해서 항상 `orWhere` 그룹을 호출해야합니다.

<a name="advanced-where-clauses"></a>
### 보다 복잡한 Where 절

<a name="where-exists-clauses"></a>
### 존재여부를 판단하는 Where 절

`whereExists` 메소드를 사용하면 "where exists" SQL 절을 작성할 수 있습니다. `whereExists` 메소드는 쿼리 빌더 인스턴스를 인자로 받아들이는 클로저를 받으므로 "exists" 절 내부에 위치해야 하는 쿼리를 정의할 수 있습니다.

    $users = DB::table('users')
               ->whereExists(function ($query) {
                   $query->select(DB::raw(1))
                         ->from('orders')
                         ->whereColumn('orders.user_id', 'users.id');
               })
               ->get();

위의 쿼리는 다음과 같은 SQL을 생성합니다.

```sql
select * from users
where exists (
    select 1
    from orders
    where orders.user_id = users.id
)
```

<a name="subquery-where-clauses"></a>
### 서브쿼리 Where 절

주어진 값에 대한 서브쿼리의 결과를 비교하는 "where" 절을 생성해야 할때, 클로저와 값을 `where` 메서드에 전달하면 됩니다. 예를 들어 다음 쿼리는 주어진 유형의 최근 "membership"을 가진 모든 유저를 검색합니다.

    use App\Models\User;

    $users = User::where(function ($query) {
        $query->select('type')
            ->from('membership')
            ->whereColumn('membership.user_id', 'users.id')
            ->orderByDesc('membership.start_date')
            ->limit(1);
    }, 'Pro')->get();

또는 컬럼을 서브쿼리의 결과와 비교하는 "where" 절을 구성해야 하는 경우 컬럼명, 연산자, 클로저를 `where` 메소드에 전달하여 됩니다. 예를 들어 다음 쿼리는 amount가 평균 미만인 모든 income 레코드를 검색합니다.

    use App\Models\Income;

    $incomes = Income::where('amount', '<', function ($query) {
        $query->selectRaw('avg(i.amount)')->from('incomes as i');
    })->get();

<a name="ordering-grouping-limit-and-offset"></a>
## Ordering, Grouping, Limit & Offset

<a name="ordering"></a>
### Ordering

<a name="orderby"></a>
#### `orderBy` 메소드

`orderBy` 메소드는 주어진 컬럼에 대한 쿼리의 결과를 정렬합니다. `orderBy` 메소드의 첫번째 인자는 정렬하고자 하는 컬럼이며, 두번째 인자는 `asc` 또는 `desc`의 정렬 방식을 결정하는 인자가 됩니다.

    $users = DB::table('users')
                    ->orderBy('name', 'desc')
                    ->get();

여러 컬럼을 기준으로 정렬하려면 필요한 만큼 `orderBy`를 호출하면 됩니다.

    $users = DB::table('users')
                    ->orderBy('name', 'desc')
                    ->orderBy('email', 'asc')
                    ->get();

<a name="latest-oldest"></a>
#### `latest` & `oldest` 메소드

`latest` 와`oldest` 메소드는 손쉽게 날짜를 기반으로 결과를 정렬할 수 있게 해줍니다. 기본적으로 결과는 테이블의 `created_at` 컬럼을 기준으로 정렬됩니다. 또는 정렬에 기준이 되는 컬럼 이름을 전달할 수 있습니다.

    $user = DB::table('users')
                    ->latest()
                    ->first();

<a name="random-ordering"></a>
#### 랜덤 정렬

`inRandomOrder` 메소드는 쿼리 결과를 랜덤하게 정렬하고자 할때 사용할 수 있습니다. 예를 들어, 유저를 랜덤하게 가져올 때 사용할 수 있습니다.

    $randomUser = DB::table('users')
                    ->inRandomOrder()
                    ->first();

<a name="removing-existing-orderings"></a>
#### 존재하는 정렬 제거

`reorder` 메소드는 이전 쿼리에 적용된 모든 "order by" 절을 제거합니다.

`reorder`

`reorder` 메소드를 사용하면 기존의 모든 정렬을 제거하고 선택적으로 새 정렬을 적용 할 수 있습니다. 예를 들어 다음과 같이 기존 정렬을 모두 제거 할 수 있습니다.

    $query = DB::table('users')->orderBy('name');

    $unorderedUsers = $query->reorder()->get();

기존의 모든 "order by" 절을 제거하고 쿼리에 완전히 새로운 순서를 적용하기 위해 `reorder` 메소드를 호출할 때 컬럼과 정렬 순서을 지정할 수 있습니다.

    $query = DB::table('users')->orderBy('name');

    $usersOrderedByEmail = $query->reorder('email', 'desc')->get();

<a name="grouping"></a>
### Grouping

<a name="groupby-having"></a>
#### `groupBy` & `having` 메소드

예상할 수 있듯 `groupBy` 와 `having` 메소드는 쿼리 결과를 그룹화하는데 사용합니다. `having` 메소드는 `where` 메소드와 사용방법이 비슷합니다.

    $users = DB::table('users')
                    ->groupBy('account_id')
                    ->having('account_id', '>', 100)
                    ->get();

`haveBetween` 메소드를 사용하여 주어진 범위 내에서 결과를 필터링할 수 있습니다.

    $report = DB::table('orders')
                    ->selectRaw('count(id) as number_of_orders, customer_id')
                    ->groupBy('customer_id')
                    ->havingBetween('number_of_orders', [5, 15])
                    ->get();

멀티 컬럼 groupBy를 위해서 `groupBy` 메소드에 여러개의 인자를 전달 할 수 있습니다.

    $users = DB::table('users')
                    ->groupBy('first_name', 'status')
                    ->having('account_id', '>', 100)
                    ->get();

보다 복잡한 `having` 구문에 대해서는 [`havingRaw`](#raw-methods) 메소드를 참고하십시오.

<a name="limit-and-offset"></a>
### Limit & Offset

<a name="skip-take"></a>
#### The `skip` & `take` 메소드

`skip` 과 `take` 메소드를 사용하여 쿼리에서 반환되는 결과 수를 제한하거나 쿼리에서 지정된 수의 결과를 건너뛸 수 있습니다.

    $users = DB::table('users')->skip(10)->take(5)->get();

대안으로 `limit` 과 `offset` 메소드를 사용할 수도 있습니다. 이 메소드는 각각 `take` 및 `skip` 메소드와 기능적으로 동일합니다.

    $users = DB::table('users')
                    ->offset(10)
                    ->limit(5)
                    ->get();

<a name="conditional-clauses"></a>
## Conditional-조건적 where 절

때로는 특정 쿼리 절이 다른 조건에 따라 쿼리에 적용되기를 원할 수 있습니다. 예를 들어, 현재의 HTTP 요청에서 주어진 입력값이 존재할 때에만 `where` 구문을 적용하고 싶을 수도 있습니다. 이 경우 `when` 메소드를 사용할 수 있습니다.

    $role = $request->input('role');

    $users = DB::table('users')
                    ->when($role, function ($query, $role) {
                        return $query->where('role_id', $role);
                    })
                    ->get();

`when` 메소드는 첫번째 파라미터가 `true` 일때 주어진 클로저를 실행합니다. 첫번째 파라미터가 `false` 라면 클로저는 실행되지 않을 것입니다. 따라서 위의 예제에서는 `when` 메소드에 지정된 클로저는 들어오는 요청에 `role` 필드가 있고 `true`로 평가되는 경우에만 호출됩니다.

`when` 메소드의 세번째 인수로 또다른 클로저를 전달할 수 있습니다. 이 클로저는 첫번째 파라미터가 `false` 일때만 실행됩니다. 다음은 이 기능을 어떻게 사용하는지 보여주기 위한 예로, 쿼리의 기본 정렬을 구성한 것입니다.

    $sortByVotes = $request->input('sort_by_votes');

    $users = DB::table('users')
                    ->when($sortByVotes, function ($query, $sortByVotes) {
                        return $query->orderBy('votes');
                    }, function ($query) {
                        return $query->orderBy('name');
                    })
                    ->get();

<a name="insert-statements"></a>
## Insert-삽입 구문

The query builder also provides an `insert` method that may be used to insert records into the database table. The `insert` method accepts an array of column names and values:
쿼리 빌더는 데이터베이스 테이블에 레코드를 삽입하는 데 사용하는 `insert` 메서드도 제공합니다. `insert` 메소드는 컬럼의 이름과 값으로 이루어진 배열을 인자로 전달받습니다.

    DB::table('users')->insert([
        'email' => 'kayla@example.com',
        'votes' => 0
    ]);

배열들의 배열(여러 개의 배열) 전달하여 한 번에 여러 레코드를 삽입할 수 있습니다. 각 배열은 테이블에 삽입되어야 하는 레코드를 나타냅니다.

    DB::table('users')->insert([
        ['email' => 'picard@example.com', 'votes' => 0],
        ['email' => 'janeway@example.com', 'votes' => 0],
    ]);

`insertOrIgnore` 메소드는 데이터베이스에 레코드를 삽입하는 동안 오류를 무시합니다.

    DB::table('users')->insertOrIgnore([
        ['id' => 1, 'email' => 'sisko@example.com'],
        ['id' => 2, 'email' => 'archer@example.com'],
    ]);

> {note} `insertOrIgnore` 중복 레코드를 무시하고 데이터베이스 엔진에 따라 다른 유형의 오류도 무시할 수 있습니다. 예를들어 `insertOrIgnore`는 [MySQL strict mode 우회](https://dev.mysql.com/doc/refman/en/sql-mode.html#ignore-effect-on-execution)합니다.

<a name="auto-incrementing-ids"></a>
#### Auto-Incrementing IDs

테이블이 자동 증가(auto-incrementing) id를 가지고 있다면 `insertGetId` 메소드를 사용하여 레코드를 추가하고, 추가된 ID를 획득할 수 있습니다.

    $id = DB::table('users')->insertGetId(
        ['email' => 'john@example.com', 'votes' => 0]
    );

> {note} PostgreSQL에서 `insertGetId` 메소드를 사용하는 경우 auto-incrementing 컬럼의 이름은 `id` 이어야 합니다. 다른 이름의 ID를 원한다면는 `insertGetId` 메소드의 두 번째 인자로 컬럼 이름을 전달 하십시오.

<a name="upserts"></a>
### Upserts

`upsert` 메소드는 존재하지 않는 레코드는 삽입하고 이미 존재하는 레코드는 지정한 값으로 업데이트합니다. 메소드의 첫 번째 인수는 삽입하거나 업데이트할 값으로 구성됩니다. 두 번째 인수는 연결된 테이블 내에서 레코드를 고유하게 식별할 수 있는 컬럼을 나열합니다. 메서드의 세 번째이자 마지막 인수는 일치하는 레코드가 데이터베이스에 이미 있는 경우 업데이트해야 하는 컬럼의 배열입니다.

    DB::table('flights')->upsert([
        ['departure' => 'Oakland', 'destination' => 'San Diego', 'price' => 99],
        ['departure' => 'Chicago', 'destination' => 'New York', 'price' => 150]
    ], ['departure', 'destination'], ['price']);

위의 예제에서 라라벨은 두 개의 레코드를 삽입하려고 시도합니다. 동일한 `departure` 및 `destination` 컬럼 값을 가진 레코드가 이미 존재하는 경우 라라벨은 해당 레코드의 `price` 컬럼을 업데이트합니다.

> {note} SQL Server를 제외한 모든 데이터베이스는 `upsert` 메소드의 두 번째 인수의 컬럼은 "primary" 또는 "unique" 인덱스가 있어야 합니다. 또한 MySQL 데이터베이스 드라이버는 `upsert` 메소드의 두 번째 인수를 무시하고 항상 테이블의 "primary" 및 "unique" 인덱스를 사용하여 기존 레코드를 감지합니다.

<a name="update-statements"></a>
## Update-수정 구문

데이터베이스에 레코드를 삽입하는 것 외에도 쿼리 빌더는 `update` 메소드를 사용하여 기존 레코드를 업데이트할 수도 있습니다. `update` 메소드는 `insert` 메소드와 마찬가지로 업데이트할 컬럼을 나타내는 컬럼과 값 쌍의 배열을 받습니다. `update` 메소드는 영향을 받는 행의 수를 반환합니다. `where` 절을 사용하여 `update` 쿼리를 제한할 수 있습니다.

    $affected = DB::table('users')
                  ->where('id', 1)
                  ->update(['votes' => 1]);

<a name="update-or-insert"></a>
#### Update-수정 또는 Insert-삽입

경우에 따라 데이터베이스의 기존 레코드는 업데이트 하고 일치하는 레코드가 없는 경우 레코드를 생성해야 할 수도 있습니다. 이런 시나리오에서는 `updateOrInsert` 메소드를 사용할 수 있습니다. `updateOrInsert` 메소드는 레코드를 찾는 데 사용되는 조건 배열과 업데이트할 컬럼을 나타내는 컬럼 및 값 쌍의 배열의 두 가지 인수를 받습니다.

`updateOrInsert` 메소드는 먼저 첫 번째 인자의 컬럼과 값의 쌍을 사용하여 일치하는 데이터베이스 레코드를 찾습니다. 레코드가 있으면 두 번째 인수의 값으로 업데이트 합니다. 레코드를 찾을 수 없으면 두 인수를 병합한 속성(attribute)으로 새 레코드가 삽입됩니다.

    DB::table('users')
        ->updateOrInsert(
            ['email' => 'john@example.com', 'name' => 'John'],
            ['votes' => '2']
        );

<a name="updating-json-columns"></a>
### JSON 컬럼 업데이트

JSON 컬럼을 업데이트 할때에는 JSON 객체의 해당 키에 엑세스하기 위해서 `->` 문법을 사용해야 합니다. 이 작업은 MySQL 5.7 이상, PostgreSQL 9.5 이상 에서만 지원합니다.

    $affected = DB::table('users')
                  ->where('id', 1)
                  ->update(['options->enabled' => true]);

<a name="increment-and-decrement"></a>
### Increment-증가 & Decrement-감소

쿼리 빌더는 주어진 컬럼의 값을 증가감하는 편리한 메소드를 제공합니다. 두 메소드 모두 수정할 컬럼을 하나 이상의 인수로 받습니다. 컬럼이 증가하거나 감소해야 하는 양을 지정하기 위해 두 번째 인수를 지정할 수 있습니다.

    DB::table('users')->increment('votes');

    DB::table('users')->increment('votes', 5);

    DB::table('users')->decrement('votes');

    DB::table('users')->decrement('votes', 5);

또한 이 작업을 수행하는 동안 업데이트 되어야할 컬럼을 추가적으로 지정할 수도 있습니다.

    DB::table('users')->increment('votes', 1, ['name' => 'John']);

<a name="delete-statements"></a>
## Delete-삭제 구문

쿼리 빌더의 `delete` 메소드를 사용하여 테이블에서 레코드를 삭제할 수 있습니다. `delete` 메소드는 영향을 받는 행의 수를 반환합니다. `delete` 메소드를 호출하기 전에 "where" 절을 추가하여 `delete` 문을 제한할 수 있습니다.

    $deleted = DB::table('users')->delete();

    $deleted = DB::table('users')->where('votes', '>', 100)->delete();

모든 데이터를 삭제하고 자동 증가(auto-incrementing) ID를 0으로 초기화 하는 것과 같이, 테이블 전체를 비우고자 한다면 `truncate` 메소드를 사용할 수 있습니다.

    DB::table('users')->truncate();

<a name="table-truncation-and-postgresql"></a>
#### Table Truncation & PostgreSQL

PostgreSQL 데이터베이스에서 초기화(truncate) 할때 `CASCADE` 동작이 적용됩니다. 즉, 다른 테이블의 모든 외래 키 관련 레코드도 삭제됩니다.

<a name="pessimistic-locking"></a>
## Pessimistic Locking

쿼리 빌더는 `select` 구문에 "Pessimistic Locking-배타적 잠금"을 설정을 할 수 있도록 도와주는 몇가지 기능을 포함하고 있습니다. "공유 lock"과 함께 구문을 실행하기 위해서, `sharedLock` 메소드를 사용할 수 있습니다. 공유 lock 은 선택된 row 가 트랜젝션이 커밋되기 전까지 수정되는 것을 방지합니다.

    DB::table('users')
            ->where('votes', '>', 100)
            ->sharedLock()
            ->get();

대안으로, `lockForUpdate` 메소드를 사용할 수 있습니다. "수정을 위한" lock 선택한 레코드가 수정되거나 다른 공유 lock으로 선택되는 것을 방지합니다.

    DB::table('users')
            ->where('votes', '>', 100)
            ->lockForUpdate()
            ->get();

<a name="debugging"></a>
## Debugging

쿼리를 작성하는 동안 현재 쿼리 바인딩과 SQL을 출력하기 위해 `dd` 또는 `dump` 메소드를 사용 할 수 있습니다. `dd` 메소드는 디버그 정보를 표시하고 요청의 실행을 중단합니다. `dump` 메소드는 디버그 정보를 보여 주지만 요청이 계속 실행되도록합니다.

    DB::table('users')->where('votes', '>', 100)->dd();

    DB::table('users')->where('votes', '>', 100)->dump();
