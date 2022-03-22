# Database: Query Builder
# 데이터베이스: 쿼리 빌더

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Running Database Queries](#running-database-queries)
- [데이터베이스 쿼리 실행하기](#running-database-queries)
  - [Chunking Results](#chunking-results)
  - [결과 분활 하기](#chunking-results)
  - [Streaming Results Lazily](#streaming-results-lazily)
  - [결과 지연 스트리밍](#streaming-results-lazily)
  - [Aggregates](#aggregates)
  - [Aggregates-집계문](#aggregates)
- [Select Statements](#select-statements)
- [Select-선택 구문](#select-statements)
- [Raw Expressions](#raw-expressions)
- [Raw 표현식](#raw-expressions)
- [Joins](#joins)
- [Joins-조인](#joins)
- [Unions](#unions)
- [Unions-유니온](#unions)
- [Basic Where Clauses](#basic-where-clauses)
- [기본 Where 절](#basic-where-clauses)
  - [Where Clauses](#where-clauses)
  - [Where 절](#where-clauses)
  - [Or Where Clauses](#or-where-clauses)
  - [Where Not Clauses](#where-not-clauses)
  - [Where Not 절](#where-not-clauses)
  - [Or Where 절](#or-where-clauses)
  - [JSON Where Clauses](#json-where-clauses)
  - [JSON Where 절](#json-where-clauses)
  - [Additional Where Clauses](#additional-where-clauses)
  - [추가 Where 절](#additional-where-clauses)
  - [Logical Grouping](#logical-grouping)
  - [논리적 그룹화](#logical-grouping)
- [Advanced Where Clauses](#advanced-where-clauses)
- [보다 복잡한 Where 절](#advanced-where-clauses)
  - [Where Exists Clauses](#where-exists-clauses)
  - [존재여부를 판단하는 Where 절](#where-exists-clauses)
  - [Subquery Where Clauses](#subquery-where-clauses)
  - [서브쿼리 Where 절](#subquery-where-clauses)
  - [Full Text Where Clauses](#full-text-where-clauses)
  - [Full Text Where 절](#full-text-where-clauses)
- [Ordering, Grouping, Limit & Offset](#ordering-grouping-limit-and-offset)
- [Ordering, Grouping, Limit & Offset](#ordering-grouping-limit-and-offset)
  - [Ordering](#ordering)
  - [Ordering](#ordering)
  - [Grouping](#grouping)
  - [Grouping](#grouping)
  - [Limit & Offset](#limit-and-offset)
  - [Limit & Offset](#limit-and-offset)
- [Conditional Clauses](#conditional-clauses)
- [Conditional-조건적 where 절](#conditional-clauses)
- [Insert Statements](#insert-statements)
- [Insert-삽입 구문](#insert-statements)
  - [Upserts](#upserts)
  - [Upserts](#upserts)
- [Update Statements](#update-statements)
- [Update-수정 구문](#update-statements)
  - [Updating JSON Columns](#updating-json-columns)
  - [JSON 컬럼 업데이트](#updating-json-columns)
  - [Increment & Decrement](#increment-and-decrement)
  - [Increment-증가 & Decrement-감소](#increment-and-decrement)
- [Delete Statements](#delete-statements)
- [Delete-삭제 구문](#delete-statements)
- [Pessimistic Locking](#pessimistic-locking)
- [Pessimistic Locking](#pessimistic-locking)
- [Debugging](#debugging)
- [디버깅](#debugging)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel's database query builder provides a convenient, fluent interface to creating and running database queries. It can be used to perform most database operations in your application and works perfectly with all of Laravel's supported database systems.

라라벨의 데이터베이스 쿼리 빌더는 데이터베이스 쿼리들을 만들고 운영하는데 다양한 인터페이스의 편의 기능을 제공합니다. 애플리케이션에서 대부분의 데이터베이스 작업을 수행하는 데 사용할 수 있으며 Laravel에서 지원하는 모든 데이터베이스 시스템과 완벽하게 작동합니다.

The Laravel query builder uses PDO parameter binding to protect your application against SQL injection attacks. There is no need to clean or sanitize strings passed to the query builder as query bindings.

라라벨의 쿼리 빌더는 PDO 파라미터 바인딩을 사용하여 SQL injection 공격을 방지합니다. 따라서 쿼리 빌더에 쿼리 바인딩으로 전달된 문자열을 정리할 필요가 없습니다.

> {note} PDO does not support binding column names. Therefore, you should never allow user input to dictate the column names referenced by your queries, including "order by" columns.

> {note} PDO는 컬럼명 바인딩을 지원하지 않습니다. 따라서 "order by" 컬럼을 포함하여 사용자 입력이 쿼리에서 참조하는 컬럼 이름을 지시하도록 허용해서는 안됩니다.

<a name="running-database-queries"></a>
## Running Database Queries
## 데이터베이스 쿼리 실행하기

<a name="retrieving-all-rows-from-a-table"></a>
#### Retrieving All Rows From A Table
#### 한 테이블에서 모든 행들을 가져오기

You may use the `table` method provided by the `DB` facade to begin a query. The `table` method returns a fluent query builder instance for the given table, allowing you to chain more constraints onto the query and then finally retrieve the results of the query using the `get` method:

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

The `get` method returns an `Illuminate\Support\Collection` instance containing the results of the query where each result is an instance of the PHP `stdClass` object. You may access each column's value by accessing the column as a property of the object:

`get` 메소드는 각각의 결과가 PHP `stdClass` 객체의 인스턴스로 구성된 쿼리의 결과를 `Illuminate\Support\Collection`의 인스턴스로 반환합니다. 객체의 속성(property)으로 컬럼에 접근하여 각 컬의 값에 액세스할 수 있습니다.

    use Illuminate\Support\Facades\DB;

    $users = DB::table('users')->get();

    foreach ($users as $user) {
        echo $user->name;
    }

> {tip} Laravel collections provide a variety of extremely powerful methods for mapping and reducing data. For more information on Laravel collections, check out the [collection documentation](/docs/{{version}}/collections).

> {tip} 라라벨 컬렉션은 데이터를 매핑하고 축소를 위한 매우 강력한 메소드를 다양하게 제공합니다 라라벨 컬렉션의 더 많은 정보를 확인하고 싶다면 [컬렉션 문서](/docs/{{version}}/collections)를 확인하세요.

<a name="retrieving-a-single-row-column-from-a-table"></a>
#### Retrieving A Single Row / Column From A Table
#### 테이블에서 하나의 결과 / 컬럼 가져오기

If you just need to retrieve a single row from a database table, you may use the `DB` facade's `first` method. This method will return a single `stdClass` object:

데이터베이스 테이블에서 하나의 row을 가져오고자 한다면, `DB` 파사드의 `first` 메소드를 사용하면 됩니다. 이 메소드는 하나의 `stdClass` 객체를 반환할 것입니다.

    $user = DB::table('users')->where('name', 'John')->first();

    return $user->email;

If you don't need an entire row, you may extract a single value from a record using the `value` method. This method will return the value of the column directly:

전체 row 가 필요하지 않다면, `value` 메소드를 사용하여 레코드에서 하나의 값만 추출할 수 있습니다. 메소드는 컬럼의 직접적인 값을 반환할 것입니다.

    $email = DB::table('users')->where('name', 'John')->value('email');

To retrieve a single row by its `id` column value, use the `find` method:

`id` 컬럼 값으로 단일 행을 검색하려면 `find` 메소드를 사용하십시오.

    $user = DB::table('users')->find(3);

<a name="retrieving-a-list-of-column-values"></a>
#### Retrieving A List Of Column Values
#### 컬럼 값의 목록 조회하기

If you would like to retrieve an `Illuminate\Support\Collection` instance containing the values of a single column, you may use the `pluck` method. In this example, we'll retrieve a collection of user titles:

한개의 컬럼의 값들을 포함하고 있는 `Illuminate\Support\Collection`의 인스턴스를 조회하고자 한다면, `pluck` 메소드를 사용할 수 있습니다. 이 예제에서는 유저의 타이틀 컬렉션을 조회할 것입니다.


    use Illuminate\Support\Facades\DB;

    $titles = DB::table('users')->pluck('title');

    foreach ($titles as $title) {
        echo $title;
    }

You may specify the column that the resulting collection should use as its keys by providing a second argument to the `pluck` method:

`pluck` 메소드에 두 번째 인수를 입력하여 결과 컬렉션이 키로 사용하는 컬럼을 지정할 수 있습니다 .

    $titles = DB::table('users')->pluck('title', 'name');

    foreach ($titles as $name => $title) {
        echo $title;
    }

<a name="chunking-results"></a>
### Chunking Results
### 결과 분할하기

If you need to work with thousands of database records, consider using the `chunk` method provided by the `DB` facade. This method retrieves a small chunk of results at a time and feeds each chunk into a closure for processing. For example, let's retrieve the entire `users` table in chunks of 100 records at a time:

데이터베이스 레코드가 많은 작업을 수행해야 한다면, `DB` 파사드가 제공하는 `chunk` 메소드를 사용하는 것을 고려하십시오. 이 메소드는 한번에 결과에 대한 하나의 작은 청크로 획득하고, 각각의 청크를 `Closure` 를 통해서 처리합니다. 예제로 `users` 테이블을 한번에 100개의 레코드 청크로 전체 검색해 보겠습니다.

    use Illuminate\Support\Facades\DB;

    DB::table('users')->orderBy('id')->chunk(100, function ($users) {
        foreach ($users as $user) {
            //
        }
    });

You may stop further chunks from being processed by returning `false` from the closure:

클로저에서 `false`를 반환하여, 더이상의 청크를 처리하지 않도록 중단할 수 있습니다.

    DB::table('users')->orderBy('id')->chunk(100, function ($users) {
        // Process the records...

        return false;
    });

If you are updating database records while chunking results, your chunk results could change in unexpected ways. If you plan to update the retrieved records while chunking, it is always best to use the `chunkById` method instead. This method will automatically paginate the results based on the record's primary key:

결과를 청킹 하는 동안 데이터베이스 레코드를 업데이트하는 경우 청크 결과가 예상치 못한 방식으로 변경될 수 있습니다. 청킹 중 검색된 레코드를 업데이트 하려는 경우 항상 `chunkById` 메소드를 사용하는 것이 좋습니다. 이 메소드는 레코드의 기본 키를 기반으로 결과에 자동으로 페이징 합니다.

    DB::table('users')->where('active', false)
        ->chunkById(100, function ($users) {
            foreach ($users as $user) {
                DB::table('users')
                    ->where('id', $user->id)
                    ->update(['active' => true]);
            }
        });

> {note} When updating or deleting records inside the chunk callback, any changes to the primary key or foreign keys could affect the chunk query. This could potentially result in records not being included in the chunked results.

> {note} 청크 콜백 내에서 레코드를 업데이트하거나 삭제할 때 기본 키나 외래 키를 변경하면 청크 쿼리에 영향을 줄 수 있습니다. 이로 인해 잠재적으로 레코드가 청크 결과에 포함되지 않을 수 있습니다.

<a name="streaming-results-lazily"></a>
### Streaming Results Lazily
### 결과 지연 스트리밍

The `lazy` method works similarly to [the `chunk` method](#chunking-results) in the sense that it executes the query in chunks. However, instead of passing each chunk into a callback, the `lazy()` method returns a [`LazyCollection`](/docs/{{version}}/collections#lazy-collections), which lets you interact with the results as a single stream:

`lazy` 메소드는 쿼리를 청크로 실행한다는 점에서 [`chunk` 메소드](#chunking-results)와 유사하게 작동합니다. 그러나 `lazy()` 메소드는 각 청크를 콜백으로 전달하는 대신 결과를 단일 스트림으로 상호 작용할 수 있도록 [`LazyCollection`](/docs/{{version}}/collections#lazy-collections)으로 반환합니다.

```php
use Illuminate\Support\Facades\DB;

DB::table('users')->orderBy('id')->lazy()->each(function ($user) {
    //
});
```

Once again, if you plan to update the retrieved records while iterating over them, it is best to use the `lazyById` or `lazyByIdDesc` methods instead. These methods will automatically paginate the results based on the record's primary key:

또 다시 레코드를 반복하면서 업데이트하려는 경우 `lazyById` 또는 `lazyByIdDesc` 메소드를 사용하는 것이 좋습니다. 이 메소드들은 레코드의 기본 키를 기반으로 결과에 자동으로 페이징 합니다.

```php
DB::table('users')->where('active', false)
    ->lazyById()->each(function ($user) {
        DB::table('users')
            ->where('id', $user->id)
            ->update(['active' => true]);
    });
```

> {note} When updating or deleting records while iterating over them, any changes to the primary key or foreign keys could affect the chunk query. This could potentially result in records not being included in the results.

> {note} 레코드를 반복하며 업데이트하거나 삭제할 때 기본 키나 외래 키를 변경하면 청크 쿼리에 영향을 줄 수 있습니다. 이로 인해 잠재적으로 레코드가 결과에 포함되지 않을 수 있습니다.

<a name="aggregates"></a>
### Aggregates
### Aggregates-집계문

The query builder also provides a variety of methods for retrieving aggregate values like `count`, `max`, `min`, `avg`, and `sum`. You may call any of these methods after constructing your query:

쿼리 빌더는 `count`, `max`, `min`, `avg`,`sum`과 같은 집계 값을 검색하는 다양한 메소드를 제공합니다. 이 메소드들은 쿼리를 생성한 뒤에 호출하면 됩니다.

    use Illuminate\Support\Facades\DB;

    $users = DB::table('users')->count();

    $price = DB::table('orders')->max('price');

Of course, you may combine these methods with other clauses to fine-tune how your aggregate value is calculated:

당연하게도 다른 절과 결합하여 집계 값이 계산되는 방식을 미세 조정할 수 있습니다.

    $price = DB::table('orders')
                    ->where('finalized', 1)
                    ->avg('price');

<a name="determining-if-records-exist"></a>
#### Determining If Records Exist
#### 레코드가 존재하는지 확인하기

Instead of using the `count` method to determine if any records exist that match your query's constraints, you may use the `exists` and `doesntExist` methods:

쿼리 조건에 맞는 레코드가 존재하는지 확인하기 위해서 `count` 메소드를 사용하는 대신에, `exists` 와 `doesntExist` 메소드를 사용할 수 있습니다.

    if (DB::table('orders')->where('finalized', 1)->exists()) {
        // ...
    }

    if (DB::table('orders')->where('finalized', 1)->doesntExist()) {
        // ...
    }

<a name="select-statements"></a>
## Select Statements
## Select-선택 구문

<a name="specifying-a-select-clause"></a>
#### Specifying A Select Clause
#### Select 절 지정하기

You may not always want to select all columns from a database table. Using the `select` method, you can specify a custom "select" clause for the query:

항상 데이터베이스의 테이블에서 모든 컬럼을 조회하지는 않을 것입니다. `select` 메소드를 사용하여 쿼리에 대한 사용자 정의 "select" 절을 지정할 수 있습니다.

    use Illuminate\Support\Facades\DB;

    $users = DB::table('users')
                ->select('name', 'email as user_email')
                ->get();

The `distinct` method allows you to force the query to return distinct results:

`distinct` 메소드는 쿼리가 고유한 결과를 반환하도록 강제할 수 있습니다.

    $users = DB::table('users')->distinct()->get();

If you already have a query builder instance and you wish to add a column to its existing select clause, you may use the `addSelect` method:

이미 쿼리 빌더 인스턴스를 가지고 있고 존재하는 select 절에 선택할 컬럼을 추가하려면, `addSelect` 메소드를 사용할 수 있습니다.

    $query = DB::table('users')->select('name');

    $users = $query->addSelect('age')->get();

<a name="raw-expressions"></a>
## Raw Expressions
## Raw 표현식

Sometimes you may need to insert an arbitrary string into a query. To create a raw string expression, you may use the `raw` method provided by the `DB` facade:

때로는 쿼리에 임의의 문자열을 삽입해야 하는 경우도 있습니다. raw 문자 표현식을 생성하기 위해 `DB` 파사드가 제공하는 `raw` 메소드를 사용할 수 있습니다

    $users = DB::table('users')
                 ->select(DB::raw('count(*) as user_count, status'))
                 ->where('status', '<>', 1)
                 ->groupBy('status')
                 ->get();

> {note} Raw statements will be injected into the query as strings, so you should be extremely careful to avoid creating SQL injection vulnerabilities.

> {note} 이러한 구문들은 쿼리를 문자열 형태로 주입하기 때문에, SQL 인젝션에 취약하지 않도록 특별히 주의해야 합니다!

<a name="raw-methods"></a>
### Raw Methods
### Raw 메소드

Instead of using the `DB::raw` method, you may also use the following methods to insert a raw expression into various parts of your query. **Remember, Laravel can not guarantee that any query using raw expressions is protected against SQL injection vulnerabilities.**

`DB::raw` 를 사용하는 대신, 쿼리의 다양한 부분을 raw 표현식으로 대체하기 위해서 다음의 메소드를 사용할 수 있습니다. **라라벨은 raw 표션식을 사용하는 쿼리가 SQL 인젝션 취약성으로부터 보호된다는 것을 보장할 수 없습니다.**


<a name="selectraw"></a>
#### `selectRaw`
#### `selectRaw`

The `selectRaw` method can be used in place of `addSelect(DB::raw(...))`. This method accepts an optional array of bindings as its second argument:

`selectRaw` 메소드는 `addSelect(DB::raw(...))` 대신 사용할 수 있습니다. 이 메소드는 옵션 배열을 두번째 인자로 받습니다.

    $orders = DB::table('orders')
                    ->selectRaw('price * ? as price_with_tax', [1.0825])
                    ->get();

<a name="whereraw-orwhereraw"></a>
#### `whereRaw / orWhereRaw`
#### `whereRaw / orWhereRaw`

The `whereRaw` and `orWhereRaw` methods can be used to inject a raw "where" clause into your query. These methods accept an optional array of bindings as their second argument:

`whereRaw` 와 `orWhereRaw` 메소드는 쿼리에 raw "where" 절을 삽입하는데 사용할 수 있습니다. 이 메소드는 두번째 인자로 (옵션값) 바인딩 배열을 받을 수 있습니다.

    $orders = DB::table('orders')
                    ->whereRaw('price > IF(state = "TX", ?, 100)', [200])
                    ->get();

<a name="havingraw-orhavingraw"></a>
#### `havingRaw / orHavingRaw`
#### `havingRaw / orHavingRaw`

The `havingRaw` and `orHavingRaw` methods may be used to provide a raw string as the value of the "having" clause. These methods accept an optional array of bindings as their second argument:

`havingRaw` 와 `orHavingRaw` 메소드는 `having` 절의 값으로 raw 문자열을 제공하는데 사용됩니다. 이 메소드는 두번째 인자로 (옵션값) 바인딩 배열을 받을 수 있습니다.

    $orders = DB::table('orders')
                    ->select('department', DB::raw('SUM(price) as total_sales'))
                    ->groupBy('department')
                    ->havingRaw('SUM(price) > ?', [2500])
                    ->get();

<a name="orderbyraw"></a>
#### `orderByRaw`
#### `orderByRaw`

The `orderByRaw` method may be used to provide a raw string as the value of the "order by" clause:

`orderByRaw` 메소드는 `order by` 절의 값을 raw 한 문자열로 제공하는데 사용합니다.

    $orders = DB::table('orders')
                    ->orderByRaw('updated_at - created_at DESC')
                    ->get();

<a name="groupbyraw"></a>
### `groupByRaw`
### `groupByRaw`

The `groupByRaw` method may be used to provide a raw string as the value of the `group by` clause:

`groupByRaw` 메소드는 원시 문자열을 `group by` 절의 값으로 제공하는 데 사용될 수 있습니다.

    $orders = DB::table('orders')
                    ->select('city', 'state')
                    ->groupByRaw('city, state')
                    ->get();

<a name="joins"></a>
## Joins
## Joins-조인

<a name="inner-join-clause"></a>
#### Inner Join Clause
#### Inner Join 절

The query builder may also be used to add join clauses to your queries. To perform a basic "inner join", you may use the `join` method on a query builder instance. The first argument passed to the `join` method is the name of the table you need to join to, while the remaining arguments specify the column constraints for the join. You may even join multiple tables in a single query:

쿼리 빌더를 사용해서 쿼리에 조인 절(join clauses)을 추가할 수 있습니다. 기본적인 "inner join" 을 수행하기 위해서는 쿼리 빌더 인스턴스의 `join` 메소드를 사용하면 됩니다. `join` 메소드에 전달된 첫 번째 인수는 조인해야 하는 테이블의 이름이고 나머지 인수는 조인에 대한 컬럼 제약 조건을 지정합니다. 단일 쿼리에서 여러 테이블을 조인할 수도 있습니다.

    use Illuminate\Support\Facades\DB;

    $users = DB::table('users')
                ->join('contacts', 'users.id', '=', 'contacts.user_id')
                ->join('orders', 'users.id', '=', 'orders.user_id')
                ->select('users.*', 'contacts.phone', 'orders.price')
                ->get();

<a name="left-join-right-join-clause"></a>
#### Left Join / Right Join Clause
#### Left Join / Right Join 절

If you would like to perform a "left join" or "right join" instead of an "inner join", use the `leftJoin` or `rightJoin` methods. These methods have the same signature as the `join` method:

"inner join" 대신 "left join" 또는 "right join" 을 수행하고자 한다면, `leftJoin` 또는 `rightJoin` 메소드를 사용하십시오. 이 메소드들은 `join` 메소드와 동일한 구성을 가집니다.

    $users = DB::table('users')
                ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
                ->get();

    $users = DB::table('users')
                ->rightJoin('posts', 'users.id', '=', 'posts.user_id')
                ->get();

<a name="cross-join-clause"></a>
#### Cross Join Clause
#### Cross Join 절

You may use the `crossJoin` method to perform a "cross join". Cross joins generate a cartesian product between the first table and the joined table:

`crossJoin` 메소드를 사용하여 "cross Join"을 수행할 수 있습니다. cross join은 첫 번째 테이블과 조인된 테이블 사이의 곱집합(cartesian product)을 생성합니다.

    $sizes = DB::table('sizes')
                ->crossJoin('colors')
                ->get();

<a name="advanced-join-clauses"></a>
#### Advanced Join Clauses
#### 보다 복잡한 Join 절

You may also specify more advanced join clauses. To get started, pass a closure as the second argument to the `join` method. The closure will receive a `Illuminate\Database\Query\JoinClause` instance which allows you to specify constraints on the "join" clause:

보다 복잡한 join 절도 지정할 수 있습니다. 시작하려면 `join` 메소드의 두 번째 인자로 클로저를 전달하십시오. 클로저는 `Illuminate\Database\Query\JoinClause`의 인스턴스를 전달받아 `join` 절에 제약사항을 지정할 것입니다.

    DB::table('users')
            ->join('contacts', function ($join) {
                $join->on('users.id', '=', 'contacts.user_id')->orOn(...);
            })
            ->get();

If you would like to use a "where" clause on your joins, you may use the `where` and `orWhere` methods provided by the `JoinClause` instance. Instead of comparing two columns, these methods will compare the column against a value:

join에 "where" 절을 사용 하려면 `JoinClause` 인스턴스가 제공하는 `where`와 `orWhere` 메소드를 사용할 수 있습니다. 두개의 컬럼을 비교하는 대신 두 메소드는 컬럼을 값과 비교합니다.


    DB::table('users')
            ->join('contacts', function ($join) {
                $join->on('users.id', '=', 'contacts.user_id')
                     ->where('contacts.user_id', '>', 5);
            })
            ->get();

<a name="subquery-joins"></a>
#### Subquery Joins
#### 서브쿼리 조인

You may use the `joinSub`, `leftJoinSub`, and `rightJoinSub` methods to join a query to a subquery. Each of these methods receives three arguments: the subquery, its table alias, and a closure that defines the related columns. In this example, we will retrieve a collection of users where each user record also contains the `created_at` timestamp of the user's most recently published blog post:

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
## Unions
## Unions-유니온

The query builder also provides a convenient method to "union" two or more queries together. For example, you may create an initial query and use the `union` method to union it with more queries:

쿼리 빌더는 두 개 또는 그 이상의 쿼리를 "union" 하는 간편한 방법을 제공합니다. 다음과 같이 초기화한 쿼리를 생성하고 추가 쿼리를 결합하기 위하여 `union` 메소드를 사용할 수 있습니다.

    use Illuminate\Support\Facades\DB;

    $first = DB::table('users')
                ->whereNull('first_name');

    $users = DB::table('users')
                ->whereNull('last_name')
                ->union($first)
                ->get();

In addition to the `union` method, the query builder provides a `unionAll` method. Queries that are combined using the `unionAll` method will not have their duplicate results removed. The `unionAll` method has the same method signature as the `union` method.

`union` 메소드 외에도 쿼리 빌더는 `unionAll` 메소드를 제공합니다. `unionAll` 메소드를 사용하여 결합된 쿼리는 중복 결과가 제거되지 않습니다. `unionAll` 메소드는 `union` 메소드와 동일하게 사용할수 있습니다.

<a name="basic-where-clauses"></a>
## Basic Where Clauses
## 기본 Where 절

<a name="where-clauses"></a>
### Where Clauses
### Where 절

You may use the query builder's `where` method to add "where" clauses to the query. The most basic call to the `where` method requires three arguments. The first argument is the name of the column. The second argument is an operator, which can be any of the database's supported operators. The third argument is the value to compare against the column's value.

쿼리 빌더의 `where` 메소드를 사용하여 쿼리에 `where` 절을 추가할 수 있습니다. 가장 기본적인 `where` 메소드 호출 방법에는 3가지 인자가 필요합니다. 첫번째 인자는 컬럼의 이름입니다. 두번째 인자는 데이터베이스가 지원하는 연산자입니다. 마지막으로, 세번째 인자는 컬럼값과 비교할 값입니다.

For example, the following query retrieves users where the value of the `votes` column is equal to `100` and the value of the `age` column is greater than `35`:

예를 들어 다음 쿼리는 `votes` 컬럼의 값이 `100` 이고 `age` 컬럼의 값이 `35` 보다 큰 유저를 검색합니다.

    $users = DB::table('users')
                    ->where('votes', '=', 100)
                    ->where('age', '>', 35)
                    ->get();

For convenience, if you want to verify that a column is `=` to a given value, you may pass the value as the second argument to the `where` method. Laravel will assume you would like to use the `=` operator:

보다 편리하게 사용하기 위해서, 컬럼이 주어진 값에 대해 `=` 인지 확인하려면, `where` 메소드에 두 번째 인수로 전달할 수 있습니다. 라라벨은 `=` 연산자를 사용할 것으로 가정합니다.


    $users = DB::table('users')->where('votes', 100)->get();

As previously mentioned, you may use any operator that is supported by your database system:

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

You may also pass an array of conditions to the `where` function. Each element of the array should be an array containing the three arguments typically passed to the `where` method:

`where` 함수에 조건 배열을 전달할 수도 있습니다. 배열의 각 요소는 일반적으로 `where` 메소드에 전달되는 세 개의 인수로 이루어진 배열이어야 합니다.

    $users = DB::table('users')->where([
        ['status', '=', '1'],
        ['subscribed', '<>', '1'],
    ])->get();

> {note} PDO does not support binding column names. Therefore, you should never allow user input to dictate the column names referenced by your queries, including "order by" columns.

> {note} PDO는 바인딩 컬럼 이름을 지원하지 않습니다. 따라서 "order by" 컬럼을 포함하여 쿼리에서 참조하는 컬럼 이름을 사용자 입력이 지시하도록 허용해서는 안 됩니다.

<a name="or-where-clauses"></a>
### Or Where Clauses
### Or Where 절

When chaining together calls to the query builder's `where` method, the "where" clauses will be joined together using the `and` operator. However, you may use the `orWhere` method to join a clause to the query using the `or` operator. The `orWhere` method accepts the same arguments as the `where` method:

쿼리 빌더의 `where` 메소드를 체이닝 할때 "where" 절은 `and` 연산자를 사용하여 결합됩니다. 하지만 `orWhere` 메소드를 사용하여 `or` 연산자로 쿼리에 절을 결합할 수 있습니다. `orWhere` 메소드는 `where` 메소드와 동일한 인수를 받습니다.

    $users = DB::table('users')
                        ->where('votes', '>', 100)
                        ->orWhere('name', 'John')
                        ->get();

If you need to group an "or" condition within parentheses, you may pass a closure as the first argument to the `orWhere` method:

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

> {note} You should always group `orWhere` calls in order to avoid unexpected behavior when global scopes are applied.

> {tip} 글로벌 스코프가 적용될 때 예상치 못한 동작이 실행되는 것을 방지하기 위해서 항상 `orWhere` 그룹을 호출해야합니다.

<a name="where-not-clauses"></a>
### Where Not Clauses
### Where Not 절

The `whereNot` and `orWhereNot` methods may be used to negate a given group of query constraints. For example, the following query excludes products that are on clearance or which have a price that is less than ten:

`whereNot` 과 `orWhereNot` 메소드는 주어진 쿼리 제약조건그룹에 해당하지 않는 결과를 얻는데 사용합니다. 예를 들어, 다음의 예제는 'clearance' 가 'true' 이거나 'price' 가 10 미만인 제품을 제외한 결과를 확인할 수 있습니다. 

    $products = DB::table('products')
                    ->whereNot(function ($query) {
                        $query->where('clearance', true)
                              ->orWhere('price', '<', 10);
                    })
                    ->get();

<a name="json-where-clauses"></a>
### JSON Where Clauses
### JSON Where 절

Laravel also supports querying JSON column types on databases that provide support for JSON column types. Currently, this includes MySQL 5.7+, PostgreSQL, SQL Server 2016, and SQLite 3.9.0 (with the [JSON1 extension](https://www.sqlite.org/json1.html)). To query a JSON column, use the `->` operator:

라라벨은 JSON 컬럼 타입을 지원하는 데이터베이스의 JSON 컬럼 타입 쿼리를 지원합니다. 현재는 MySQL 5.7 이상, PostgreSQL, SQL Server 2016, 그리고 SQLite 3.9.0 ([JSON1 extension](https://www.sqlite.org/json1.html)과 함께)에 포함되어 있습니다. JSON 컬럼 쿼리를 하기 위해서는 `->` 연산자를 사용하십시오:

    $users = DB::table('users')
                    ->where('preferences->dining->meal', 'salad')
                    ->get();

You may use `whereJsonContains` to query JSON arrays. This feature is not supported by the SQLite database:

JSON 배열을 쿼리하기 위해 `whereJsonContains` 를 사용할 수 있습니다. 이 기능은 SQLite 데이터베이스에서는 지원하지 않습니다.

    $users = DB::table('users')
                    ->whereJsonContains('options->languages', 'en')
                    ->get();

If your application uses the MySQL or PostgreSQL databases, you may pass an array of values to the `whereJsonContains` method:

어플리케이션이 MySQL 이나 PostgreSQL 데이터베이스를 사용한다면 `whereJsonContains` 메소드에 배열 값을 전달할 수 있습니다.

    $users = DB::table('users')
                    ->whereJsonContains('options->languages', ['en', 'de'])
                    ->get();

You may use `whereJsonLength` method to query JSON arrays by their length:

`whereJsonLength` 메소드 를 사용하여 JSON 배열을 길이에 따라 쿼리 할 수 있습니다.

    $users = DB::table('users')
                    ->whereJsonLength('options->languages', 0)
                    ->get();

    $users = DB::table('users')
                    ->whereJsonLength('options->languages', '>', 1)
                    ->get();

<a name="additional-where-clauses"></a>
### Additional Where Clauses
### 추가 Where 절

**whereBetween / orWhereBetween**
**whereBetween / orWhereBetween**

The `whereBetween` method verifies that a column's value is between two values:

`whereBetween` 메소드는 컬럼의 값이 두개의 값 사이의 값인지 확인합니다.

    $users = DB::table('users')
               ->whereBetween('votes', [1, 100])
               ->get();

**whereNotBetween / orWhereNotBetween**
**whereNotBetween / orWhereNotBetween**

The `whereNotBetween` method verifies that a column's value lies outside of two values:

`whereNotBetween` 메소드는 컬럼의 값이 두개의 값 사이가 아닌지 확인합니다.

    $users = DB::table('users')
                        ->whereNotBetween('votes', [1, 100])
                        ->get();

**whereIn / whereNotIn / orWhereIn / orWhereNotIn**
**whereIn / whereNotIn / orWhereIn / orWhereNotIn**

The `whereIn` method verifies that a given column's value is contained within the given array:

`whereIn` 메소드는 주어진 컬럼의 값이 주어진 배열안에 값에 속해 있는지 확인합니다.

    $users = DB::table('users')
                        ->whereIn('id', [1, 2, 3])
                        ->get();

The `whereNotIn` method verifies that the given column's value is not contained in the given array:

`whereNotIn` 메소드는 주어진 컬럼의 값이 주어진 배열안에 포함되어 있지 않은지 확인합니다.

    $users = DB::table('users')
                        ->whereNotIn('id', [1, 2, 3])
                        ->get();

> {note} If you are adding a large array of integer bindings to your query, the `whereIntegerInRaw` or `whereIntegerNotInRaw` methods may be used to greatly reduce your memory usage.

> {note} 쿼리에 큰 정수 배열을 추가하는 경우 `whereIntegerInRaw` 또는 `whereIntegerNotInRaw` 메서드를 사용하여 메모리 사용량을 크게 줄일 수 있습니다.

**whereNull / whereNotNull / orWhereNull / orWhereNotNull**
**whereNull / whereNotNull / orWhereNull / orWhereNotNull**

The `whereNull` method verifies that the value of the given column is `NULL`:

`whereNull` 메소드는 주어진 컬럼의 값이 `NULL` 인지 확인합니다.

    $users = DB::table('users')
                    ->whereNull('updated_at')
                    ->get();

The `whereNotNull` method verifies that the column's value is not `NULL`:

`whereNotNull` 메소드는 주어진 컬럼의 값이 `NULL`이 아닌지 확인합니다.

    $users = DB::table('users')
                    ->whereNotNull('updated_at')
                    ->get();

**whereDate / whereMonth / whereDay / whereYear / whereTime**
**whereDate / whereMonth / whereDay / whereYear / whereTime**

The `whereDate` method may be used to compare a column's value against a date:

`whereDate` 메소드는 컬럼의 값이 date 값인지 비교하는데 사용됩니다.

    $users = DB::table('users')
                    ->whereDate('created_at', '2016-12-31')
                    ->get();

The `whereMonth` method may be used to compare a column's value against a specific month:

`whereMonth` 메소드는 컬럼의 값이 지정된 달과 같은지 비교하는데 사용됩니다.

    $users = DB::table('users')
                    ->whereMonth('created_at', '12')
                    ->get();

The `whereDay` method may be used to compare a column's value against a specific day of the month:

`whereDay` 메소드는 컬럼의 값이 한달의 지정된 일과 같은지 비교하는데 사용됩니다.

    $users = DB::table('users')
                    ->whereDay('created_at', '31')
                    ->get();

The `whereYear` method may be used to compare a column's value against a specific year:

`whereYear` 메소드는 컬럼의 값이 지정된 년도와 같은지 비교하는데 사용됩니다.

    $users = DB::table('users')
                    ->whereYear('created_at', '2016')
                    ->get();

The `whereTime` method may be used to compare a column's value against a specific time:

`whereTime` 메소드는 컬럼의 값을 특정 시간과 비교하는데 사용할 수 있습니다.

    $users = DB::table('users')
                    ->whereTime('created_at', '=', '11:20:45')
                    ->get();

**whereColumn / orWhereColumn**
**whereColumn / orWhereColumn**

The `whereColumn` method may be used to verify that two columns are equal:

`whereColumn` 메소드는 두개의 컬럼이 동일하는 것을 확인하는데 사용할 수 있습니다.

    $users = DB::table('users')
                    ->whereColumn('first_name', 'last_name')
                    ->get();

You may also pass a comparison operator to the `whereColumn` method:

또한 비교 연산자를 `whereColumn` 메소드에 전달할 수도 있습니다.

    $users = DB::table('users')
                    ->whereColumn('updated_at', '>', 'created_at')
                    ->get();

You may also pass an array of column comparisons to the `whereColumn` method. These conditions will be joined using the `and` operator:

컬럼 비교식 배열을 `whereColumn` 메서드에 전달할 수도 있습니다. 이 조건들은 `and` 연산자를 사용하여 연결됩니다.

    $users = DB::table('users')
                    ->whereColumn([
                        ['first_name', '=', 'last_name'],
                        ['updated_at', '>', 'created_at'],
                    ])->get();

<a name="logical-grouping"></a>
### Logical Grouping
### 논리적 그룹화

Sometimes you may need to group several "where" clauses within parentheses in order to achieve your query's desired logical grouping. In fact, you should generally always group calls to the `orWhere` method in parentheses in order to avoid unexpected query behavior. To accomplish this, you may pass a closure to the `where` method:

쿼리의 원하는 논리적 그룹화를 달성하기 위해 괄호 안에 여러 "where" 절을 그룹화해야 하는 경우가 있습니다. 예상하지 않은 쿼리 동작을 방지하기 `orWhere` 메소드는 일반적으로 항상 괄호 안에 그룹화해야 합니다. 이를 위해 `where` 메소드에 클로저를 전달할 수 있습니다.

    $users = DB::table('users')
               ->where('name', '=', 'John')
               ->where(function ($query) {
                   $query->where('votes', '>', 100)
                         ->orWhere('title', '=', 'Admin');
               })
               ->get();

As you can see, passing a closure into the `where` method instructs the query builder to begin a constraint group. The closure will receive a query builder instance which you can use to set the constraints that should be contained within the parenthesis group. The example above will produce the following SQL:

위에서 보시다 시피, `where` 메소드에 전달된 하나의 클로저가 쿼리 빌더의 제약조건을 그룹으로 묶고 있습니다. 이 클로저는 괄호로 포함된 제약조건을 설정하는데 사용할 쿼리 빌더 인스턴스를 전달받습니다. 위 예제는 다음과 같은 SQL을 생성할 것입니다.

```sql
select * from users where name = 'John' and (votes > 100 or title = 'Admin')
```

> {note} You should always group `orWhere` calls in order to avoid unexpected behavior when global scopes are applied.

> {note} 글로벌 스코프가 적용될 때 예상치 못한 동작이 실행되는 것을 방지하기 위해서 항상 `orWhere` 그룹을 호출해야합니다.

<a name="advanced-where-clauses"></a>
### Advanced Where Clauses
### 보다 복잡한 Where 절

<a name="where-exists-clauses"></a>
### Where Exists Clauses
### 존재여부를 판단하는 Where 절

The `whereExists` method allows you to write "where exists" SQL clauses. The `whereExists` method accepts a closure which will receive a query builder instance, allowing you to define the query that should be placed inside of the "exists" clause:

`whereExists` 메소드를 사용하면 "where exists" SQL 절을 작성할 수 있습니다. `whereExists` 메소드는 쿼리 빌더 인스턴스를 인자로 받아들이는 클로저를 받으므로 "exists" 절 내부에 위치해야 하는 쿼리를 정의할 수 있습니다.

    $users = DB::table('users')
               ->whereExists(function ($query) {
                   $query->select(DB::raw(1))
                         ->from('orders')
                         ->whereColumn('orders.user_id', 'users.id');
               })
               ->get();

The query above will produce the following SQL:

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
### Subquery Where Clauses
### 서브쿼리 Where 절

Sometimes you may need to construct a "where" clause that compares the results of a subquery to a given value. You may accomplish this by passing a closure and a value to the `where` method. For example, the following query will retrieve all users who have a recent "membership" of a given type;

주어진 값에 대한 서브쿼리의 결과를 비교하는 "where" 절을 생성해야 할때, 클로저와 값을 `where` 메서드에 전달하면 됩니다. 예를 들어 다음 쿼리는 주어진 유형의 최근 "membership"을 가진 모든 유저를 검색합니다.

    use App\Models\User;

    $users = User::where(function ($query) {
        $query->select('type')
            ->from('membership')
            ->whereColumn('membership.user_id', 'users.id')
            ->orderByDesc('membership.start_date')
            ->limit(1);
    }, 'Pro')->get();

Or, you may need to construct a "where" clause that compares a column to the results of a subquery. You may accomplish this by passing a column, operator, and closure to the `where` method. For example, the following query will retrieve all income records where the amount is less than average;

또는 컬럼을 서브쿼리의 결과와 비교하는 "where" 절을 구성해야 하는 경우 컬럼명, 연산자, 클로저를 `where` 메소드에 전달하여 됩니다. 예를 들어 다음 쿼리는 amount가 평균 미만인 모든 income 레코드를 검색합니다.

    use App\Models\Income;

    $incomes = Income::where('amount', '<', function ($query) {
        $query->selectRaw('avg(i.amount)')->from('incomes as i');
    })->get();

<a name="full-text-where-clauses"></a>
### Full Text Where Clauses
### Full Text Where 절

> {note} Full text where clauses are currently supported by MySQL and PostgreSQL.

> {note} Full text where 절은 현재 MySQL과 PostgreSQL을 지원합니다.

The `whereFullText` and `orWhereFullText` methods may be used to add full text "where" clauses to a query for columns that have [full text indexes](/docs/{{version}}/migrations#available-index-types). These methods will be transformed into the appropriate SQL for the underlying database system by Laravel. For example, a `MATCH AGAINST` clause will be generated for applications utilizing MySQL:

`whereFullText` 및 `orWhereFullText` 메서드는 [Full Text 인덱스](/docs/{{version}}/migrations#available-index-types)가 있는 컬럼에 대한 쿼리에 Full text "where" 절을 추가하는 데 사용할 수 있습니다. 라라벨은 이 메소드를 기본 데이터베이스 시스템에 적합한 SQL로 변환됩니다. 예를 들어 MySQL의 경우 `MATCH AGAINST` 절을 생성합니다.

    $users = DB::table('users')
               ->whereFullText('bio', 'web developer')
               ->get();

<a name="ordering-grouping-limit-and-offset"></a>
## Ordering, Grouping, Limit & Offset
## Ordering, Grouping, Limit & Offset

<a name="ordering"></a>
### Ordering
### Ordering

<a name="orderby"></a>
#### The `orderBy` Method
#### `orderBy` 메소드

The `orderBy` method allows you to sort the results of the query by a given column. The first argument accepted by the `orderBy` method should be the column you wish to sort by, while the second argument determines the direction of the sort and may be either `asc` or `desc`:

`orderBy` 메소드는 주어진 컬럼에 대한 쿼리의 결과를 정렬합니다. `orderBy` 메소드의 첫번째 인자는 정렬하고자 하는 컬럼이며, 두번째 인자는 `asc` 또는 `desc`의 정렬 방식을 결정하는 인자가 됩니다.

    $users = DB::table('users')
                    ->orderBy('name', 'desc')
                    ->get();

To sort by multiple columns, you may simply invoke `orderBy` as many times as necessary:

여러 컬럼을 기준으로 정렬하려면 필요한 만큼 `orderBy`를 호출하면 됩니다.

    $users = DB::table('users')
                    ->orderBy('name', 'desc')
                    ->orderBy('email', 'asc')
                    ->get();

<a name="latest-oldest"></a>
#### The `latest` & `oldest` Methods
#### `latest` & `oldest` 메소드

The `latest` and `oldest` methods allow you to easily order results by date. By default, the result will be ordered by the table's `created_at` column. Or, you may pass the column name that you wish to sort by:

`latest` 와`oldest` 메소드는 손쉽게 날짜를 기반으로 결과를 정렬할 수 있게 해줍니다. 기본적으로 결과는 테이블의 `created_at` 컬럼을 기준으로 정렬됩니다. 또는 정렬에 기준이 되는 컬럼 이름을 전달할 수 있습니다.

    $user = DB::table('users')
                    ->latest()
                    ->first();

<a name="random-ordering"></a>
#### Random Ordering
#### 랜덤 정렬

The `inRandomOrder` method may be used to sort the query results randomly. For example, you may use this method to fetch a random user:

`inRandomOrder` 메소드는 쿼리 결과를 랜덤하게 정렬하고자 할때 사용할 수 있습니다. 예를 들어, 유저를 랜덤하게 가져올 때 사용할 수 있습니다.

    $randomUser = DB::table('users')
                    ->inRandomOrder()
                    ->first();

<a name="removing-existing-orderings"></a>
#### Removing Existing Orderings
#### 존재하는 정렬 제거

The `reorder` method removes all of the "order by" clauses that have previously been applied to the query:

`reorder` 메소드는 이전 쿼리에 적용된 모든 "order by" 절을 제거합니다.

`reorder`

The `reorder` method allows you to remove all the existing orders and optionally apply a new order. For example, you can remove all the existing orders:

`reorder` 메소드를 사용하면 기존의 모든 정렬을 제거하고 선택적으로 새 정렬을 적용 할 수 있습니다. 예를 들어 다음과 같이 기존 정렬을 모두 제거 할 수 있습니다.

    $query = DB::table('users')->orderBy('name');

    $unorderedUsers = $query->reorder()->get();

You may pass a column and direction when calling the `reorder` method in order to remove all existing "order by" clauses and apply an entirely new order to the query:

기존의 모든 "order by" 절을 제거하고 쿼리에 완전히 새로운 순서를 적용하기 위해 `reorder` 메소드를 호출할 때 컬럼과 정렬 순서을 지정할 수 있습니다.

    $query = DB::table('users')->orderBy('name');

    $usersOrderedByEmail = $query->reorder('email', 'desc')->get();

<a name="grouping"></a>
### Grouping
### Grouping

<a name="groupby-having"></a>
#### The `groupBy` & `having` Methods
#### `groupBy` & `having` 메소드

As you might expect, the `groupBy` and `having` methods may be used to group the query results. The `having` method's signature is similar to that of the `where` method:

예상할 수 있듯 `groupBy` 와 `having` 메소드는 쿼리 결과를 그룹화하는데 사용합니다. `having` 메소드는 `where` 메소드와 사용방법이 비슷합니다.

    $users = DB::table('users')
                    ->groupBy('account_id')
                    ->having('account_id', '>', 100)
                    ->get();

You can use the `havingBetween` method to filter the results within a given range:

`haveBetween` 메소드를 사용하여 주어진 범위 내에서 결과를 필터링할 수 있습니다.

    $report = DB::table('orders')
                    ->selectRaw('count(id) as number_of_orders, customer_id')
                    ->groupBy('customer_id')
                    ->havingBetween('number_of_orders', [5, 15])
                    ->get();

You may pass multiple arguments to the `groupBy` method to group by multiple columns:

멀티 컬럼 groupBy를 위해서 `groupBy` 메소드에 여러개의 인자를 전달 할 수 있습니다.

    $users = DB::table('users')
                    ->groupBy('first_name', 'status')
                    ->having('account_id', '>', 100)
                    ->get();

To build more advanced `having` statements, see the [`havingRaw`](#raw-methods) method.

보다 복잡한 `having` 구문에 대해서는 [`havingRaw`](#raw-methods) 메소드를 참고하십시오.

<a name="limit-and-offset"></a>
### Limit & Offset
### Limit & Offset

<a name="skip-take"></a>
#### The `skip` & `take` Methods
#### The `skip` & `take` 메소드

You may use the `skip` and `take` methods to limit the number of results returned from the query or to skip a given number of results in the query:

`skip` 과 `take` 메소드를 사용하여 쿼리에서 반환되는 결과 수를 제한하거나 쿼리에서 지정된 수의 결과를 건너뛸 수 있습니다.

    $users = DB::table('users')->skip(10)->take(5)->get();

Alternatively, you may use the `limit` and `offset` methods. These methods are functionally equivalent to the `take` and `skip` methods, respectively:

대안으로 `limit` 과 `offset` 메소드를 사용할 수도 있습니다. 이 메소드는 각각 `take` 및 `skip` 메소드와 기능적으로 동일합니다.

    $users = DB::table('users')
                    ->offset(10)
                    ->limit(5)
                    ->get();

<a name="conditional-clauses"></a>
## Conditional Clauses
## Conditional-조건적 where 절

Sometimes you may want certain query clauses to apply to a query based on another condition. For instance, you may only want to apply a `where` statement if a given input value is present on the incoming HTTP request. You may accomplish this using the `when` method:

때로는 특정 쿼리 절이 다른 조건에 따라 쿼리에 적용되기를 원할 수 있습니다. 예를 들어, 현재의 HTTP 요청에서 주어진 입력값이 존재할 때에만 `where` 구문을 적용하고 싶을 수도 있습니다. 이 경우 `when` 메소드를 사용할 수 있습니다.

    $role = $request->input('role');

    $users = DB::table('users')
                    ->when($role, function ($query, $role) {
                        $query->where('role_id', $role);
                    })
                    ->get();

The `when` method only executes the given closure when the first argument is `true`. If the first argument is `false`, the closure will not be executed. So, in the example above, the closure given to the `when` method will only be invoked if the `role` field is present on the incoming request and evaluates to `true`.

`when` 메소드는 첫번째 파라미터가 `true` 일때 주어진 클로저를 실행합니다. 첫번째 파라미터가 `false` 라면 클로저는 실행되지 않을 것입니다. 따라서 위의 예제에서는 `when` 메소드에 지정된 클로저는 들어오는 요청에 `role` 필드가 있고 `true`로 평가되는 경우에만 호출됩니다.

You may pass another closure as the third argument to the `when` method. This closure will only execute if the first argument evaluates as `false`. To illustrate how this feature may be used, we will use it to configure the default ordering of a query:

`when` 메소드의 세번째 인수로 또다른 클로저를 전달할 수 있습니다. 이 클로저는 첫번째 파라미터가 `false` 일때만 실행됩니다. 다음은 이 기능을 어떻게 사용하는지 보여주기 위한 예로, 쿼리의 기본 정렬을 구성한 것입니다.

    $sortByVotes = $request->input('sort_by_votes');

    $users = DB::table('users')
                    ->when($sortByVotes, function ($query, $sortByVotes) {
                        $query->orderBy('votes');
                    }, function ($query) {
                        $query->orderBy('name');
                    })
                    ->get();

<a name="insert-statements"></a>
## Insert Statements
## Insert-삽입 구문

The query builder also provides an `insert` method that may be used to insert records into the database table. The `insert` method accepts an array of column names and values:
The query builder also provides an `insert` method for inserting records into the database table.              The `insert` method accepts an array of column names and values:

쿼리 빌더는 데이터베이스 테이블에 레코드를 삽입하는 데 사용하는 `insert` 메서드도 제공합니다. `insert` 메소드는 컬럼의 이름과 값으로 이루어진 배열을 인자로 전달받습니다.

    DB::table('users')->insert([
        'email' => 'kayla@example.com',
        'votes' => 0
    ]);

You may insert several records at once by passing an array of arrays. Each array represents a record that should be inserted into the table:

배열들의 배열(여러 개의 배열) 전달하여 한 번에 여러 레코드를 삽입할 수 있습니다. 각 배열은 테이블에 삽입되어야 하는 레코드를 나타냅니다.

    DB::table('users')->insert([
        ['email' => 'picard@example.com', 'votes' => 0],
        ['email' => 'janeway@example.com', 'votes' => 0],
    ]);

The `insertOrIgnore` method will ignore errors while inserting records into the database:

`insertOrIgnore` 메소드는 데이터베이스에 레코드를 삽입하는 동안 오류를 무시합니다.

    DB::table('users')->insertOrIgnore([
        ['id' => 1, 'email' => 'sisko@example.com'],
        ['id' => 2, 'email' => 'archer@example.com'],
    ]);

> {note} `insertOrIgnore` will ignore duplicate records and also may ignore other types of errors depending on the database engine. For example, `insertOrIgnore` will [bypass MySQL's strict mode](https://dev.mysql.com/doc/refman/en/sql-mode.html#ignore-effect-on-execution).

> {note} `insertOrIgnore` 중복 레코드를 무시하고 데이터베이스 엔진에 따라 다른 유형의 오류도 무시할 수 있습니다. 예를들어 `insertOrIgnore`는 [MySQL strict mode 우회](https://dev.mysql.com/doc/refman/en/sql-mode.html#ignore-effect-on-execution)합니다.


<a name="auto-incrementing-ids"></a>
#### Auto-Incrementing IDs
#### Auto-Incrementing IDs

If the table has an auto-incrementing id, use the `insertGetId` method to insert a record and then retrieve the ID:

테이블이 자동 증가(auto-incrementing) id를 가지고 있다면 `insertGetId` 메소드를 사용하여 레코드를 추가하고, 추가된 ID를 획득할 수 있습니다.

    $id = DB::table('users')->insertGetId(
        ['email' => 'john@example.com', 'votes' => 0]
    );

> {note} When using PostgreSQL the `insertGetId` method expects the auto-incrementing column to be named `id`. If you would like to retrieve the ID from a different "sequence", you may pass the column name as the second parameter to the `insertGetId` method.

> {note} PostgreSQL에서 `insertGetId` 메소드를 사용하는 경우 auto-incrementing 컬럼의 이름은 `id` 이어야 합니다. 다른 이름의 ID를 원한다면는 `insertGetId` 메소드의 두 번째 인자로 컬럼 이름을 전달 하십시오.

<a name="upserts"></a>
### Upserts
### Upserts

The `upsert` method will insert records that do not exist and update the records that already exist with new values that you may specify. The method's first argument consists of the values to insert or update, while the second argument lists the column(s) that uniquely identify records within the associated table. The method's third and final argument is an array of columns that should be updated if a matching record already exists in the database:

`upsert` 메소드는 존재하지 않는 레코드는 삽입하고 이미 존재하는 레코드는 지정한 값으로 업데이트합니다. 메소드의 첫 번째 인수는 삽입하거나 업데이트할 값으로 구성됩니다. 두 번째 인수는 연결된 테이블 내에서 레코드를 고유하게 식별할 수 있는 컬럼을 나열합니다. 메서드의 세 번째이자 마지막 인수는 일치하는 레코드가 데이터베이스에 이미 있는 경우 업데이트해야 하는 컬럼의 배열입니다.

    DB::table('flights')->upsert([
        ['departure' => 'Oakland', 'destination' => 'San Diego', 'price' => 99],
        ['departure' => 'Chicago', 'destination' => 'New York', 'price' => 150]
    ], ['departure', 'destination'], ['price']);

In the example above, Laravel will attempt to insert two records. If a record already exists with the same `departure` and `destination` column values, Laravel will update that record's `price` column.

위의 예제에서 라라벨은 두 개의 레코드를 삽입하려고 시도합니다. 동일한 `departure` 및 `destination` 컬럼 값을 가진 레코드가 이미 존재하는 경우 라라벨은 해당 레코드의 `price` 컬럼을 업데이트합니다.

> {note} All databases except SQL Server require the columns in the second argument of the `upsert` method to have a "primary" or "unique" index. In addition, the MySQL database driver ignores the second argument of the `upsert` method and always uses the "primary" and "unique" indexes of the table to detect existing records.

> {note} SQL Server를 제외한 모든 데이터베이스는 `upsert` 메소드의 두 번째 인수의 컬럼은 "primary" 또는 "unique" 인덱스가 있어야 합니다. 또한 MySQL 데이터베이스 드라이버는 `upsert` 메소드의 두 번째 인수를 무시하고 항상 테이블의 "primary" 및 "unique" 인덱스를 사용하여 기존 레코드를 감지합니다.

<a name="update-statements"></a>
## Update Statements
## Update-수정 구문

In addition to inserting records into the database, the query builder can also update existing records using the `update` method. The `update` method, like the `insert` method, accepts an array of column and value pairs indicating the columns to be updated. The `update` method returns the number of affected rows. You may constrain the `update` query using `where` clauses:

데이터베이스에 레코드를 삽입하는 것 외에도 쿼리 빌더는 `update` 메소드를 사용하여 기존 레코드를 업데이트할 수도 있습니다. `update` 메소드는 `insert` 메소드와 마찬가지로 업데이트할 컬럼을 나타내는 컬럼과 값 쌍의 배열을 받습니다. `update` 메소드는 영향을 받는 행의 수를 반환합니다. `where` 절을 사용하여 `update` 쿼리를 제한할 수 있습니다.

    $affected = DB::table('users')
                  ->where('id', 1)
                  ->update(['votes' => 1]);

<a name="update-or-insert"></a>
#### Update Or Insert
#### Update-수정 또는 Insert-삽입

Sometimes you may want to update an existing record in the database or create it if no matching record exists. In this scenario, the `updateOrInsert` method may be used. The `updateOrInsert` method accepts two arguments: an array of conditions by which to find the record, and an array of column and value pairs indicating the columns to be updated.

경우에 따라 데이터베이스의 기존 레코드는 업데이트 하고 일치하는 레코드가 없는 경우 레코드를 생성해야 할 수도 있습니다. 이런 시나리오에서는 `updateOrInsert` 메소드를 사용할 수 있습니다. `updateOrInsert` 메소드는 레코드를 찾는 데 사용되는 조건 배열과 업데이트할 컬럼을 나타내는 컬럼 및 값 쌍의 배열의 두 가지 인수를 받습니다.

The `updateOrInsert` method will attempt to locate a matching database record using the first argument's column and value pairs. If the record exists, it will be updated with the values in the second argument. If the record can not be found, a new record will be inserted with the merged attributes of both arguments:

`updateOrInsert` 메소드는 먼저 첫 번째 인자의 컬럼과 값의 쌍을 사용하여 일치하는 데이터베이스 레코드를 찾습니다. 레코드가 있으면 두 번째 인수의 값으로 업데이트 합니다. 레코드를 찾을 수 없으면 두 인수를 병합한 속성(attribute)으로 새 레코드가 삽입됩니다.

    DB::table('users')
        ->updateOrInsert(
            ['email' => 'john@example.com', 'name' => 'John'],
            ['votes' => '2']
        );

<a name="updating-json-columns"></a>
### Updating JSON Columns
### JSON 컬럼 업데이트

When updating a JSON column, you should use `->` syntax to update the appropriate key in the JSON object. This operation is supported on MySQL 5.7+ and PostgreSQL 9.5+:

JSON 컬럼을 업데이트 할때에는 JSON 객체의 해당 키에 엑세스하기 위해서 `->` 문법을 사용해야 합니다. 이 작업은 MySQL 5.7 이상, PostgreSQL 9.5 이상 에서만 지원합니다.

    $affected = DB::table('users')
                  ->where('id', 1)
                  ->update(['options->enabled' => true]);

<a name="increment-and-decrement"></a>
### Increment & Decrement
### Increment-증가 & Decrement-감소

The query builder also provides convenient methods for incrementing or decrementing the value of a given column. Both of these methods accept at least one argument: the column to modify. A second argument may be provided to specify the amount by which the column should be incremented or decremented:

쿼리 빌더는 주어진 컬럼의 값을 증가감하는 편리한 메소드를 제공합니다. 두 메소드 모두 수정할 컬럼을 하나 이상의 인수로 받습니다. 컬럼이 증가하거나 감소해야 하는 양을 지정하기 위해 두 번째 인수를 지정할 수 있습니다.

    DB::table('users')->increment('votes');

    DB::table('users')->increment('votes', 5);

    DB::table('users')->decrement('votes');

    DB::table('users')->decrement('votes', 5);

You may also specify additional columns to update during the operation:

또한 이 작업을 수행하는 동안 업데이트 되어야할 컬럼을 추가적으로 지정할 수도 있습니다.

    DB::table('users')->increment('votes', 1, ['name' => 'John']);

<a name="delete-statements"></a>
## Delete Statements
## Delete-삭제 구문

The query builder's `delete` method may be used to delete records from the table. The `delete` method returns the number of affected rows. You may constrain `delete` statements by adding "where" clauses before calling the `delete` method:

쿼리 빌더의 `delete` 메소드를 사용하여 테이블에서 레코드를 삭제할 수 있습니다. `delete` 메소드는 영향을 받는 행의 수를 반환합니다. `delete` 메소드를 호출하기 전에 "where" 절을 추가하여 `delete` 문을 제한할 수 있습니다.

    $deleted = DB::table('users')->delete();

    $deleted = DB::table('users')->where('votes', '>', 100)->delete();

If you wish to truncate an entire table, which will remove all records from the table and reset the auto-incrementing ID to zero, you may use the `truncate` method:

모든 데이터를 삭제하고 자동 증가(auto-incrementing) ID를 0으로 초기화 하는 것과 같이, 테이블 전체를 비우고자 한다면 `truncate` 메소드를 사용할 수 있습니다.

    DB::table('users')->truncate();

<a name="table-truncation-and-postgresql"></a>
#### Table Truncation & PostgreSQL
#### Table Truncation & PostgreSQL

When truncating a PostgreSQL database, the `CASCADE` behavior will be applied. This means that all foreign key related records in other tables will be deleted as well.

PostgreSQL 데이터베이스에서 초기화(truncate) 할때 `CASCADE` 동작이 적용됩니다. 즉, 다른 테이블의 모든 외래 키 관련 레코드도 삭제됩니다.

<a name="pessimistic-locking"></a>
## Pessimistic Locking

The query builder also includes a few functions to help you achieve "pessimistic locking" when executing your `select` statements. To execute a statement with a "shared lock", you may call the `sharedLock` method. A shared lock prevents the selected rows from being modified until your transaction is committed:

쿼리 빌더는 `select` 구문에 "Pessimistic Locking-배타적 잠금"을 설정을 할 수 있도록 도와주는 몇가지 기능을 포함하고 있습니다. "공유 lock"과 함께 구문을 실행하기 위해서, `sharedLock` 메소드를 사용할 수 있습니다. 공유 lock 은 선택된 row 가 트랜젝션이 커밋되기 전까지 수정되는 것을 방지합니다.

    DB::table('users')
            ->where('votes', '>', 100)
            ->sharedLock()
            ->get();

Alternatively, you may use the `lockForUpdate` method. A "for update" lock prevents the selected records from being modified or from being selected with another shared lock:

대안으로, `lockForUpdate` 메소드를 사용할 수 있습니다. "수정을 위한" lock 선택한 레코드가 수정되거나 다른 공유 lock으로 선택되는 것을 방지합니다.

    DB::table('users')
            ->where('votes', '>', 100)
            ->lockForUpdate()
            ->get();

<a name="debugging"></a>
## Debugging
## Debugging

You may use the `dd` and `dump` methods while building a query to dump the current query bindings and SQL. The `dd` method will display the debug information and then stop executing the request. The `dump` method will display the debug information but allow the request to continue executing:

쿼리를 작성하는 동안 현재 쿼리 바인딩과 SQL을 출력하기 위해 `dd` 또는 `dump` 메소드를 사용 할 수 있습니다. `dd` 메소드는 디버그 정보를 표시하고 요청의 실행을 중단합니다. `dump` 메소드는 디버그 정보를 보여 주지만 요청이 계속 실행되도록합니다.

    DB::table('users')->where('votes', '>', 100)->dd();

    DB::table('users')->where('votes', '>', 100)->dump();
