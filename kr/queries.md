# Database: Query Builder
# 데이터베이스: 쿼리 빌더

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Retrieving Results](#retrieving-results)
- [결과 조회하기](#retrieving-results)
    - [Chunking Results](#chunking-results)
    - [결과 분할하기](#chunking-results)
    - [Aggregates](#aggregates)
    - [Aggregates-집계문](#aggregates)
- [Selects](#selects)
- [Selects-선택](#selects)
- [Raw Expressions](#raw-expressions)
- [Raw 표현식](#raw-expressions)
- [Joins](#joins)
- [Joins-조인](#joins)
- [Unions](#unions)
- [Unions-유니온](#unions)
- [Where Clauses](#where-clauses)
- [Where 구문](#where-clauses)
    - [Parameter Grouping](#parameter-grouping)
    - [파라미터 그룹](#parameter-grouping)
    - [Where Exists Clauses](#where-exists-clauses)
    - [존재여부를 판단하는 Where 절](#where-exists-clauses)
    - [JSON Where Clauses](#json-where-clauses)
    - [JSON Where 절](#json-where-clauses)
- [Ordering, Grouping, Limit, & Offset](#ordering-grouping-limit-and-offset)
- [Ordering, Grouping, Limit, & Offset](#ordering-grouping-limit-and-offset)
- [Conditional Clauses](#conditional-clauses)
- [Conditional-조건적 구문](#conditional-clauses)
- [Inserts](#inserts)
- [Inserts-삽입](#inserts)
- [Updates](#updates)
- [Updates-수정](#updates)
    - [Updating JSON Columns](#updating-json-columns)
    - [JSON 컬럼 업데이트](#updating-json-columns)
    - [Increment & Decrement](#increment-and-decrement)
    - [Increment-증가 & Decrement-감소](#increment-and-decrement)
- [Deletes](#deletes)
- [Deletes-삭제](#deletes)
- [Pessimistic Locking](#pessimistic-locking)
- [Pessimistic Locking](#pessimistic-locking)
- [Debugging](#debugging)
- [디버깅](#debugging)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel's database query builder provides a convenient, fluent interface to creating and running database queries. It can be used to perform most database operations in your application and works on all supported database systems.

라라벨의 데이터베이스 쿼리 빌더는 데이터베이스 쿼리들을 만들고 운영하는데 다양한 인터페이스의 편의기능를 제공합니다. 여러분들의 애플리케이션에서 운영하는 대부분의 데이터베이스 시스템에서 잘 작동합니다.

The Laravel query builder uses PDO parameter binding to protect your application against SQL injection attacks. There is no need to clean strings being passed as bindings.

라라벨의 쿼리 빌더는 PDO 파라미터 바인딩을 사용하여 SQL injection 공격을 방지합니다. 따라서 쿼리에 바인딩할 문자열들을 따로 정리하고 전달할 필요가 없습니다.

> {note} PDO does not support binding column names. Therefore, you should never allow user input to dictate the column names referenced by your queries, including "order by" columns, etc. If you must allow the user to select certain columns to query against, always validate the column names against a white-list of allowed columns.

> {note} PDO는 컬럼명 바인딩을 지원하지 않습니다. 따라서 "order by" 컬럼을 포함하여 사용자 입력이 쿼리에서 참조하는 컬럼 이름을 지시하도록 허용해서는 안됩니다. 사용자가 쿼리 할 특정 컬럼을 선택하도록 허용해야 하는 경우 항상 허용 된 컬럼의 화이트리스트를 통해 컬럼 이름의 유효성을 검사하십시오.

<a name="retrieving-results"></a>
## Retrieving Results
## 결과 조회하기

#### Retrieving All Rows From A Table
#### 한 테이블에서 모든 행들을 가져오기

You may use the `table` method on the `DB` facade to begin a query. The `table` method returns a fluent query builder instance for the given table, allowing you to chain more constraints onto the query and then finally get the results using the `get` method:

`DB` 파사드의 `table` 메소드를 사용하여 쿼리를 시작할 수 있습니다. `table` 메소드는 주어진 테이블에 대한 쿼리빌더 인스턴스를 반환하고, 다양한 제약을 메소드 체이닝 형태로 구성할 수 있으며, 마지막으로 `get` 메소드를 사용하여 결과를 가져올 수 있습니다:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Support\Facades\DB;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Show a list of all of the application's users.
         *
         * @return Response
         */
        public function index()
        {
            $users = DB::table('users')->get();

            return view('user.index', ['users' => $users]);
        }
    }

The `get` method returns an `Illuminate\Support\Collection` containing the results where each result is an instance of the PHP `stdClass` object. You may access each column's value by accessing the column as a property of the object:

`get` 메소드는 각각의 결과가 PHP `stdClass` 객체 인스턴스로 구성된 `Illuminate\Support\Collection` 을 반환합니다. 객체의 속성이 컬럼인것 처럼, 각각의 컬럼의 값에 엑세스 할 수 있습니다.

    foreach ($users as $user) {
        echo $user->name;
    }

#### Retrieving A Single Row / Column From A Table
#### 테이블에서 하나의 결과 / 컬럼 가져오기

If you just need to retrieve a single row from the database table, you may use the `first` method. This method will return a single `stdClass` object:

데이터베이스 테이블에서 하나의 row을 가져오고자 한다면, `first` 메소드를 사용하면 됩니다. 이 메소드는 하나의 `stdClass` 객체를 반환할 것입니다:

    $user = DB::table('users')->where('name', 'John')->first();

    echo $user->name;

If you don't even need an entire row, you may extract a single value from a record using the `value` method. This method will return the value of the column directly:

전체 row 가 필요하지 않다면, `value` 메소드를 사용하여 레코드에서 하나의 값만 추출할 수 있습니다. 메소드는 컬럼의 직접적인 값을 반환할 것입니다:

    $email = DB::table('users')->where('name', 'John')->value('email');

To retrieve a single row by its `id` column value, use the `find` method:

`id` 컬럼 값으로 단일 행을 검색하려면 `find` 메소드를 사용하십시오.

    $user = DB::table('users')->find(3);

#### Retrieving A List Of Column Values
#### 컬럼 값의 목록 조회하기

If you would like to retrieve a Collection containing the values of a single column, you may use the `pluck` method. In this example, we'll retrieve a Collection of role titles:

한개의 컬럼의 값들을 포함하고 있는 컬렉션을 조회하고자 한다면, `pluck` 메소드를 사용할 수 있습니다. 이 예제에서는 사용자의 역할의 이름의 컬렉션을 조회할 것입니다:

    $titles = DB::table('roles')->pluck('title');

    foreach ($titles as $title) {
        echo $title;
    }

 You may also specify a custom key column for the returned Collection:

 반환될 컬렉션에서 사용될 특정한 컬럼 키를 지정할 수도 있습니다:

    $roles = DB::table('roles')->pluck('title', 'name');

    foreach ($roles as $name => $title) {
        echo $title;
    }

<a name="chunking-results"></a>
### Chunking Results
### 결과 분할하기

If you need to work with thousands of database records, consider using the `chunk` method. This method retrieves a small chunk of the results at a time and feeds each chunk into a `Closure` for processing. This method is very useful for writing [Artisan commands](/docs/{{version}}/artisan) that process thousands of records. For example, let's work with the entire `users` table in chunks of 100 records at a time:

데이터베이스 레코드가 많은 작업을 수행해야 한다면, `chunk` 메소드를 사용하는 것을 고려하십시오. 이 메소드는 한번에 결과에 대한 하나의 작은 chunk 를 획득하고, 각각의 chunk를 `Closure` 를 통해서 처리합니다. 이 메소드는 다수의 레코드를 처리하는 [아티즌 명령어](/docs/{{version}}/artisan)를 작성하는데 유용합니다. 예를 들어, 전체 `users` 테이블에서 한번에 100개의 레코드를 가져온다고 해보겠습니다:

    DB::table('users')->orderBy('id')->chunk(100, function ($users) {
        foreach ($users as $user) {
            //
        }
    });

You may stop further chunks from being processed by returning `false` from the `Closure`:

`Closure` 에서 `false`를 반환하여, 더이상의 chunk를 처리하지 않도록 중단할 수 있습니다:

    DB::table('users')->orderBy('id')->chunk(100, function ($users) {
        // Process the records...

        return false;
    });

If you are updating database records while chunking results, your chunk results could change in unexpected ways. So, when updating records while chunking, it is always best to use the `chunkById` method instead. This method will automatically paginate the results based on the record's primary key:

결과를 청킹하는 동안 데이터베이스 레코드를 업데이트하는 경우 청크 결과가 예상치 못한 방식으로 변경 될 수 있습니다. 따라서 청킹 중 레코드를 업데이트 할 때는 항상 `chunkById` 메소드를 사용하는 것이 좋습니다. 이 메서드는 레코드의 기본 키를 기반으로 결과에 자동으로 페이징합니다.

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

<a name="aggregates"></a>
### Aggregates
### Aggregates-집계문

The query builder also provides a variety of aggregate methods such as `count`, `max`, `min`, `avg`, and `sum`. You may call any of these methods after constructing your query:

쿼리빌더는 또한 `count`, `max`, `min`, `avg` 그리고 `sum` 과 같은 다양한 집계 관련 메소드를 제공합니다. 이 메소드들은 쿼리를 생성한 뒤에 호출하면 됩니다:

    $users = DB::table('users')->count();

    $price = DB::table('orders')->max('price');

You may combine these methods with other clauses:

이 메소드들과 다른 구문을 조합하여 사용할 수 있습니다:

    $price = DB::table('orders')
                    ->where('finalized', 1)
                    ->avg('price');

#### Determining If Records Exist
#### 레코드가 존재하는지 확인하기

Instead of using the `count` method to determine if any records exist that match your query's constraints, you may use the `exists` and `doesntExist` methods:

쿼리 조건에 맞는 레코드가 존재하는지 확인하기 위해서 `count` 메소드를 사용하는 대신에, `exists` 와 `doesntExist` 메소드를 사용할 수 있습니다:

    return DB::table('orders')->where('finalized', 1)->exists();

    return DB::table('orders')->where('finalized', 1)->doesntExist();

<a name="selects"></a>
## Selects
## Selects-선택

#### Specifying A Select Clause
#### Select문 지정하기

You may not always want to select all columns from a database table. Using the `select` method, you can specify a custom `select` clause for the query:

항상 데이터베이스의 테이블에서 모든 컬럼을 조회하고자 하지는 않을 것입니다. `select` 메소드를 사용하여, 쿼리에서 `select` 문을 위한 지정을 할 수 있습니다:

    $users = DB::table('users')->select('name', 'email as user_email')->get();

The `distinct` method allows you to force the query to return distinct results:

`distinct` 메소드는 쿼리가 고유한 결과를 반환하도록 강제할 수 있습니다:

    $users = DB::table('users')->distinct()->get();

If you already have a query builder instance and you wish to add a column to its existing select clause, you may use the `addSelect` method:

이미 쿼리빌더 인스턴스를 가지고 있고, 존재하는 select 구문에 선택할 컬럼을 추가하고자 한다면, `addSelect` 메소드를 사용할 수 있습니다:

    $query = DB::table('users')->select('name');

    $users = $query->addSelect('age')->get();

<a name="raw-expressions"></a>
## Raw Expressions
## Raw 표현식

Sometimes you may need to use a raw expression in a query. To create a raw expression, you may use the `DB::raw` method:

때로는, 쿼리에서 Raw Expressions를 사용하고자 할 수도 있습니다. raw expression 을 생성하기 위해서는 `DB::raw` 메소드를 사용할 수 있습니다:

    $users = DB::table('users')
                         ->select(DB::raw('count(*) as user_count, status'))
                         ->where('status', '<>', 1)
                         ->groupBy('status')
                         ->get();

> {note} Raw statements will be injected into the query as strings, so you should be extremely careful to not create SQL injection vulnerabilities.

> {note} 이러한 구문들은 쿼리를 문자열 형태로 주입하기 때문에, SQL 인젝션에 취약하지 않도록 특별히 주의해야 합니다!

<a name="raw-methods"></a>
### Raw Methods
### Raw 메소드

Instead of using `DB::raw`, you may also use the following methods to insert a raw expression into various parts of your query.

`DB::raw` 를 사용하는 대신에, 쿼리의 다양한 부분을 raw expression으로 대체하기 위해서 다음의 메소드를 사용할 수 있습니다.

#### `selectRaw`

The `selectRaw` method can be used in place of `select(DB::raw(...))`. This method accepts an optional array of bindings as its second argument:

`selectRaw` 메소드는 `select(DB::raw(...))` 대신 사용할 수 있습니다. 이 메소드는 옵션 배열을 두번째 인자로 받습니다:

    $orders = DB::table('orders')
                    ->selectRaw('price * ? as price_with_tax', [1.0825])
                    ->get();

#### `whereRaw / orWhereRaw`

The `whereRaw` and `orWhereRaw` methods can be used to inject a raw `where` clause into your query. These methods accept an optional array of bindings as their second argument:

`whereRaw` 와 `orWhereRaw` 메소드는 쿼리의 `where` 절에 raw 한 구문을 삽입하는데 사용할 수 있습니다. 이 메소드도 옵션 배열을 두번째 인자로 받습니다:

    $orders = DB::table('orders')
                    ->whereRaw('price > IF(state = "TX", ?, 100)', [200])
                    ->get();

#### `havingRaw / orHavingRaw`

The `havingRaw` and `orHavingRaw` methods may be used to set a raw string as the value of the `having` clause. These methods accept an optional array of bindings as their second argument:

`havingRaw` 와 `orHavingRaw` 메소드는 `having` 절의 값으로 raw 문자열을 설정하는데 사용됩니다. 이 메소드는 두번째 인자로 (옵션값) 바인딩 배열을 받을 수 있습니다:

    $orders = DB::table('orders')
                    ->select('department', DB::raw('SUM(price) as total_sales'))
                    ->groupBy('department')
                    ->havingRaw('SUM(price) > ?', [2500])
                    ->get();

#### `orderByRaw`

The `orderByRaw` method may be used to set a raw string as the value of the `order by` clause:

`orderByRaw` 메소드는 `order by` 절의 값을 raw 한 문자열로 설정하는데 사용합니다:

    $orders = DB::table('orders')
                    ->orderByRaw('updated_at - created_at DESC')
                    ->get();



<a name="joins"></a>
## Joins
## Joins-조인

#### Inner Join Clause
#### Inner Join

The query builder may also be used to write join statements. To perform a basic "inner join", you may use the `join` method on a query builder instance. The first argument passed to the `join` method is the name of the table you need to join to, while the remaining arguments specify the column constraints for the join. You can even join to multiple tables in a single query:

쿼리 빌더를 사용해서 조인문(join statement)을 만들수 있습니다. 기본적인 "inner join" 을 수행하기 위해서는 쿼리 빌더 인스턴스에 `join` 메소드를 사용하면 됩니다. `join` 메소드에 전달되는 첫번째 인자는 join을 수행할 테이블의 이름이며, 구 이후는 join 을 실행할 때 컬럼의 제약 조건입니다. 다음과 같이 하나의 쿼리에서 여러 테이블을 join 할 수도 있습니다:

    $users = DB::table('users')
                ->join('contacts', 'users.id', '=', 'contacts.user_id')
                ->join('orders', 'users.id', '=', 'orders.user_id')
                ->select('users.*', 'contacts.phone', 'orders.price')
                ->get();

#### Left Join / Right Join Clause
#### Left 조인 / Right 조인

If you would like to perform a "left join" or "right join" instead of an "inner join", use the `leftJoin` or `rightJoin` methods. These methods have the same signature as the `join` method:

"inner join" 대신 "left join" 또는 "right join" 을 수행하고자 한다면, `leftJoin` 또는 `rightJoin` 메소드를 사용하십시오. 이 메소드들은 `join` 메소드와 동일한 구성을 가집니다:

    $users = DB::table('users')
                ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
                ->get();

    $users = DB::table('users')
                ->rightJoin('posts', 'users.id', '=', 'posts.user_id')
                ->get();

#### Cross Join Clause
#### Cross Join

To perform a "cross join" use the `crossJoin` method with the name of the table you wish to cross join to. Cross joins generate a cartesian product between the first table and the joined table:

"Cross Join"을 수행하고자 한다면 조인 하고자 하는 테이블 이름과 함께 `crossJoin` 메소드를 사용하면 됩니다. 크로스 조인은 첫 번째 테이블과 조인된 테이블 사이의 cartesian product 를 생성합니다:

    $users = DB::table('sizes')
                ->crossJoin('colours')
                ->get();

#### Advanced Join Clauses
#### 보다 복잡한 Join

You may also specify more advanced join clauses. To get started, pass a `Closure` as the second argument into the `join` method. The `Closure` will receive a `JoinClause` object which allows you to specify constraints on the `join` clause:

보다 복잡한 join 구문도 지정할 수 있습니다. 이렇게 하기 위해서, `join` 메소드의 두번째 인자로 `Closure` 를 전달하십시오. `Closure` 는 `JoinCaluse` 객체를 전달 받아, `join` 구문에 제약사항을 지정할 것입니다.

    DB::table('users')
            ->join('contacts', function ($join) {
                $join->on('users.id', '=', 'contacts.user_id')->orOn(...);
            })
            ->get();

If you would like to use a "where" style clause on your joins, you may use the `where` and `orWhere` methods on a join. Instead of comparing two columns, these methods will compare the column against a value:

join 구문에 "where" 을 사용하고자 한다면, join 에 `where`와 `orWhere` 메소드를 사용하면 됩니다. 두개의 컬럼을 비교하는 대신 이러한 메소드들은 값과 컬럼을 비교합니다:

    DB::table('users')
            ->join('contacts', function ($join) {
                $join->on('users.id', '=', 'contacts.user_id')
                     ->where('contacts.user_id', '>', 5);
            })
            ->get();

#### Sub-Query Joins
#### 서브쿼리 조인

You may use the `joinSub`, `leftJoinSub`, and `rightJoinSub` methods to join a query to a sub-query. Each of these methods receive three arguments: the sub-query, its table alias, and a Closure that defines the related columns:

쿼리와 서브쿼리의 조인을 위해서 `joinSub`, `leftJoinSub`, `rightJoinSub` 메소드를 사용할 수 있습니다. 각각의 메소드는 세개의 인자: 서브쿼리, 테이블의 별칭(alias), 연관된 컬럼을 정의하는 클로저를 전달받습니다:

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

The query builder also provides a quick way to "union" two queries together. For example, you may create an initial query and use the `union` method to union it with a second query:

쿼리 빌더는 두 개의 쿼리를 "union" 하는 간편한 방법을 제공합니다. 다음과 같이 초기화한 쿼리를 생성하고 두 번째 쿼리를 결합하기 위하여 `union` 메소드를 사용할 수 있습니다:

    $first = DB::table('users')
                ->whereNull('first_name');

    $users = DB::table('users')
                ->whereNull('last_name')
                ->union($first)
                ->get();

> {tip} The `unionAll` method is also available and has the same method signature as `union`.

> {tip} `unionAll` 메소드도 `union` 메소드와 같은 방법으로 사용할 수 있습니다.

<a name="where-clauses"></a>
## Where Clauses
## Where 절

#### Simple Where Clauses
#### 간단한 Where 절

You may use the `where` method on a query builder instance to add `where` clauses to the query. The most basic call to `where` requires three arguments. The first argument is the name of the column. The second argument is an operator, which can be any of the database's supported operators. Finally, the third argument is the value to evaluate against the column.

쿼리 빌더 인스턴스에서 `where` 절을 쿼리에 추가하기 위해서는 `where` 메소드를 사용할 수 있습니다. 가장 기본적인 `where` 메소드 호출 방법에는 3가지 인자가 필요합니다. 첫번째 인자는 컬럼의 이름입니다. 두번째 인자는 데이터베이스가 지원하는 연산자입니다. 마지막으로, 세번째 인자는 컬럼에 대해 비교하는 값입니다.

For example, here is a query that verifies the value of the "votes" column is equal to 100:

다음과 같이 "votes" 컬럼이 100과 일치하는 값을 가진 쿼리를 수행합니다:

    $users = DB::table('users')->where('votes', '=', 100)->get();

For convenience, if you want to verify that a column is equal to a given value, you may pass the value directly as the second argument to the `where` method:

보다 편리하게 사용하기 위해서, 컬럼이 주어진 값과 동일한가를 확인하고자 한다면, `where` 메소드의 두번째 인자로 바로 값을 전달할 수도 있습니다:

    $users = DB::table('users')->where('votes', 100)->get();

You may use a variety of other operators when writing a `where` clause:

`where`절을 작성할 때 다양한 다른 연산자를 사용할 수 있습니다:

    $users = DB::table('users')
                    ->where('votes', '>=', 100)
                    ->get();

    $users = DB::table('users')
                    ->where('votes', '<>', 100)
                    ->get();

    $users = DB::table('users')
                    ->where('name', 'like', 'T%')
                    ->get();

You may also pass an array of conditions to the `where` function:

또한 `where` 함수에 조건에 대한 배열을 전달 할 수도 있습니다:

    $users = DB::table('users')->where([
        ['status', '=', '1'],
        ['subscribed', '<>', '1'],
    ])->get();

#### Or Statements
#### Or 구문

You may chain where constraints together as well as add `or` clauses to the query. The `orWhere` method accepts the same arguments as the `where` method:

쿼리에 `or` 절을 추가하는 것과 같이 where 제약에 더해서 체이닝 형태로 사용할 수 있습니다. `orWhere` 메소드는 `where` 메소드와 동일한 인자를 전달 받습니다:

    $users = DB::table('users')
                        ->where('votes', '>', 100)
                        ->orWhere('name', 'John')
                        ->get();

#### Additional Where Clauses
#### 추가적인 Where 구문

**whereBetween / orWhereBetween**
**whereBetween / orWhereBetween**

The `whereBetween` method verifies that a column's value is between two values:

`whereBetween` 메소드는 컬럼의 값이 두개의 값 사이의 값인지 확인합니다:

    $users = DB::table('users')
                        ->whereBetween('votes', [1, 100])->get();

**whereNotBetween / orWhereNotBetween**
**whereNotBetween / orWhereNotBetween**

The `whereNotBetween` method verifies that a column's value lies outside of two values:

`whereNotBetween` 메소드는 컬럼의 값이 두개의 값 사이가 아닌지 확인합니다:

    $users = DB::table('users')
                        ->whereNotBetween('votes', [1, 100])
                        ->get();

**whereIn / whereNotIn / orWhereIn / orWhereNotIn**
**whereIn / whereNotIn / orWhereIn / orWhereNotIn**

The `whereIn` method verifies that a given column's value is contained within the given array:

`whereIn`메소드는 주어진 컬럼의 값이 주어진 배열안에 값에 속해 있는지 확인합니다:

    $users = DB::table('users')
                        ->whereIn('id', [1, 2, 3])
                        ->get();

The `whereNotIn` method verifies that the given column's value is **not** contained in the given array:

`whereNotIn` 메소드는 주어진 컬럼의 값이 주어진 배열안에 포함되어 있지 **않은지** 확인합니다:

    $users = DB::table('users')
                        ->whereNotIn('id', [1, 2, 3])
                        ->get();

**whereNull / whereNotNull / orWhereNull / orWhereNotNull**
**whereNull / whereNotNull / orWhereNull / orWhereNotNull**

The `whereNull` method verifies that the value of the given column is `NULL`:

`whereNull`메소드는 주어진 컬럼의 값이 `NULL` 인지 확인합니다:

    $users = DB::table('users')
                        ->whereNull('updated_at')
                        ->get();

The `whereNotNull` method verifies that the column's value is not `NULL`:

`whereNotNull`메소드는 주어진 컬럼의 값이 `NULL`이 아닌지 확인합니다:

    $users = DB::table('users')
                        ->whereNotNull('updated_at')
                        ->get();

**whereDate / whereMonth / whereDay / whereYear / whereTime**
**whereDate / whereMonth / whereDay / whereYear / whereTime**

The `whereDate` method may be used to compare a column's value against a date:

`whereDate` 메소드는 컬럼의 값이 date 값인지 비교하는데 사용됩니다:

    $users = DB::table('users')
                    ->whereDate('created_at', '2016-12-31')
                    ->get();

The `whereMonth` method may be used to compare a column's value against a specific month of a year:

`whereMonth` 메소드는 컬럼의 값이 한해의 지정된 달과 같은지 비교하는데 사용됩니다:

    $users = DB::table('users')
                    ->whereMonth('created_at', '12')
                    ->get();

The `whereDay` method may be used to compare a column's value against a specific day of a month:

`whereDay` 메소드는 컬럼의 값이 한달의 지정된 일과 같은지 비교하는데 사용됩니다:

    $users = DB::table('users')
                    ->whereDay('created_at', '31')
                    ->get();

The `whereYear` method may be used to compare a column's value against a specific year:

`whereYear` 메소드는 컬럼의 값이 지정된 년도와 같은지 비교하는데 사용됩니다:

    $users = DB::table('users')
                    ->whereYear('created_at', '2016')
                    ->get();

The `whereTime` method may be used to compare a column's value against a specific time:

`whereTime` 메소드는 컬럼의 값을 특정 시간과 비교하는데 사용할 수 있습니다:

    $users = DB::table('users')
                    ->whereTime('created_at', '=', '11:20:45')
                    ->get();

**whereColumn / orWhereColumn**
**whereColumn / orWhereColumn**

The `whereColumn` method may be used to verify that two columns are equal:

`whereColumn` 메소드는 두개의 컬럼이 동일하는 것을 확인하는데 사용할 수 있습니다:

    $users = DB::table('users')
                    ->whereColumn('first_name', 'last_name')
                    ->get();

You may also pass a comparison operator to the method:

또한 비교 연산자를 메소드에 전달할 수도 있습니다:

    $users = DB::table('users')
                    ->whereColumn('updated_at', '>', 'created_at')
                    ->get();

The `whereColumn` method can also be passed an array of multiple conditions. These conditions will be joined using the `and` operator:

`whereColumn`메소드는 또한 다수의 조건 배열을 전달 받을 수도 있습니다. 이 조건들은 `and` 연산자를 사용하여 연결됩니다:

    $users = DB::table('users')
                    ->whereColumn([
                        ['first_name', '=', 'last_name'],
                        ['updated_at', '>', 'created_at']
                    ])->get();

<a name="parameter-grouping"></a>
### Parameter Grouping
### 파라미터 그룹

Sometimes you may need to create more advanced where clauses such as "where exists" clauses or nested parameter groupings. The Laravel query builder can handle these as well. To get started, let's look at an example of grouping constraints within parenthesis:

가끔은 "where exists" 절이나 중첩된 파라미터를 그룹으로 묶어서 수행하는것과 같이, 복잡한 조건문을 사용해야 할 필요가 있습니다. 라라벨의 쿼리 빌더는 이 경우 다음과 같이 사용할 수 있습니다. 다음의 괄호 안에 제약조건을 그룹으로 묶는 예제를 살펴보겠습니다:

    DB::table('users')
                ->where('name', '=', 'John')
                ->where(function ($query) {
                    $query->where('votes', '>', 100)
                          ->orWhere('title', '=', 'Admin');
                })
                ->get();

As you can see, passing a `Closure` into the `where` method instructs the query builder to begin a constraint group. The `Closure` will receive a query builder instance which you can use to set the constraints that should be contained within the parenthesis group. The example above will produce the following SQL:

위에서 보시다 시피, `where` 메소드에 전달된 하나의 `Closure`가 쿼리빌더의 제약조건을 그룹으로 묶고 있습니다. 이 `Closure`는 괄호로 포함된 제약조건을 설정하는데 사용할 쿼리빌더 인스턴스를 전달받습니다. 이 예제는 다음과 같은 SQL을 생성할 것입니다:

    select * from users where name = 'John' and (votes > 100 or title = 'Admin')

> {tip} You should always group `orWhere` calls in order to avoid unexpected behavior when global scopes are applied.

> {tip} 글로벌 스코프가 적용될 때 예상치 못한 동작이 실행되는 것을 방지하기 위해서 항상 `orWhere` 그룹을 호출해야합니다.

<a name="where-exists-clauses"></a>
### Where Exists Clauses
### 존재여부를 판단하는(Exists) where 절

The `whereExists` method allows you to write `where exists` SQL clauses. The `whereExists` method accepts a `Closure` argument, which will receive a query builder instance allowing you to define the query that should be placed inside of the "exists" clause:

`whereExists` 메소드는 SQL 쿼리에 `where exists` 를 작성할 수 있도록 합니다. `whereExists` 메소드는 쿼리에 "exist" 구문을 정의할 수 있도록 쿼리 빌더를 인자로 받아들이는 `Closure`를 인자로 받아들입니다:

    DB::table('users')
                ->whereExists(function ($query) {
                    $query->select(DB::raw(1))
                          ->from('orders')
                          ->whereRaw('orders.user_id = users.id');
                })
                ->get();

The query above will produce the following SQL:

위의 쿼리는 다음과 같은 SQL을 생성합니다:

    select * from users
    where exists (
        select 1 from orders where orders.user_id = users.id
    )

<a name="json-where-clauses"></a>
### JSON Where Clauses
### JSON Where 절

Laravel also supports querying JSON column types on databases that provide support for JSON column types. Currently, this includes MySQL 5.7, PostgreSQL, SQL Server 2016, and SQLite 3.9.0 (with the [JSON1 extension](https://www.sqlite.org/json1.html)). To query a JSON column, use the `->` operator:

라라벨은 또한 JSON 컬럼 타입을 지원하는 데이터베이스의 JSON 컬럼 타입에 대한 쿼리를 지원합니다. 현재는 MySQL 5.7, PostgreSQL, SQL Server 2016, 그리고 SQLite 3.9.0 ([JSON1 extension](https://www.sqlite.org/json1.html)과 함께)에 포함되어 있습니다. JSON 컬럼 질의를 하기 위해서는 `->` 연산자를 사용하십시오:

    $users = DB::table('users')
                    ->where('options->language', 'en')
                    ->get();

    $users = DB::table('users')
                    ->where('preferences->dining->meal', 'salad')
                    ->get();

You may use `whereJsonContains` to query JSON arrays (not supported on SQLite):

JSON 배열 (SQLite에서는 지원되지 않음)을 쿼리하기 위해 `whereJsonContains` 를 사용할 수 있습니다 :

    $users = DB::table('users')
                    ->whereJsonContains('options->languages', 'en')
                    ->get();

MySQL and PostgreSQL support `whereJsonContains` with multiple values:

MySQL과 PostgreSQL은 다중 값으로 `whereJsonContains` 를 지원합니다 :

    $users = DB::table('users')
                    ->whereJsonContains('options->languages', ['en', 'de'])
                    ->get();

You may use `whereJsonLength` to query JSON arrays by their length:

`whereJsonLength` 를 사용하여 JSON 배열을 길이에 따라 쿼리 할 수 있습니다 :

    $users = DB::table('users')
                    ->whereJsonLength('options->languages', 0)
                    ->get();

    $users = DB::table('users')
                    ->whereJsonLength('options->languages', '>', 1)
                    ->get();

<a name="ordering-grouping-limit-and-offset"></a>
## Ordering, Grouping, Limit, & Offset
## Ordering, Grouping, Limit, & Offset

#### orderBy
#### orderBy 정렬

The `orderBy` method allows you to sort the result of the query by a given column. The first argument to the `orderBy` method should be the column you wish to sort by, while the second argument controls the direction of the sort and may be either `asc` or `desc`:

`orderBy` 메소드는 주어진 컬럼에 대한 쿼리의 결과를 정렬합니다. `orderBy` 메소드의 첫번째 인자는 정렬하고자 하는 컬럼이며, 두번째 인자는 `asc` 또는 `desc`의 정렬 방식을 제어하는 인자가 됩니다:

    $users = DB::table('users')
                    ->orderBy('name', 'desc')
                    ->get();

#### latest / oldest
#### latest / oldest

The `latest` and `oldest` methods allow you to easily order results by date. By default, result will be ordered by the `created_at` column. Or, you may pass the column name that you wish to sort by:

`latest` 와`oldest` 메소드는 여러준이 손쉽게 날짜를 기반으로 결과를 정렬할 수 있게 해줍니다. 기본적으로 결과는 `created_at` 컬럼을 기준으로 정렬됩니다. 또는 정렬에 기준이 되는 컬럼이름을 전달할 수 있습니다:

    $user = DB::table('users')
                    ->latest()
                    ->first();

#### inRandomOrder
#### inRandomOrder 랜덤 정렬

The `inRandomOrder` method may be used to sort the query results randomly. For example, you may use this method to fetch a random user:

`inRandomOrder` 메소드는 쿼리 결과를 랜덤하게 정렬하고자 할때 사용할 수 있습니다. 예를 들어, 사용자를 랜덤하게 가져올 때 사용할 수 있습니다:

    $randomUser = DB::table('users')
                    ->inRandomOrder()
                    ->first();

#### groupBy / having
#### groupBy / having

The `groupBy` and `having` methods may be used to group the query results. The `having` method's signature is similar to that of the `where` method:

`groupBy` 와 `hanving` 메소드는 쿼리 결과를 그룹화하는데 사용합니다. `having` 메소드는 `where` 메소드와 사용장법이 비슷합니다:

    $users = DB::table('users')
                    ->groupBy('account_id')
                    ->having('account_id', '>', 100)
                    ->get();

You may pass multiple arguments to the `groupBy` method to group by multiple columns:

멀티 컬럼 groupBy를 위해서 `groupBy` 메소드에 여러개의 인자를 전달 할 수 있습니다:

    $users = DB::table('users')
                    ->groupBy('first_name', 'status')
                    ->having('account_id', '>', 100)
                    ->get();

For more advanced `having` statements, see the [`havingRaw`](#raw-methods) method.

보다 복잡한 `having` 구문에 대해서는 [`havingRaw`](#raw-methods) 메소드를 참조하십시오.

#### skip / take

To limit the number of results returned from the query, or to skip a given number of results in the query, you may use the `skip` and `take` methods:

쿼리에서 반환되는 결과의 갯수를 제한하거나, 주어진 갯수만큼 결과를 건너뛰기 위해서는, `skip` 과 `take` 메소드를 사용하면 됩니다:

    $users = DB::table('users')->skip(10)->take(5)->get();

Alternatively, you may use the `limit` and `offset` methods:

대신에, `limit` 과 `offset` 메소드를 사용할 수도 있습니다:

    $users = DB::table('users')
                    ->offset(10)
                    ->limit(5)
                    ->get();

<a name="conditional-clauses"></a>
## Conditional Clauses
## Conditional-조건적 where 절

Sometimes you may want clauses to apply to a query only when something else is true. For instance you may only want to apply a `where` statement if a given input value is present on the incoming request. You may accomplish this using the `when` method:

때로는 어떠한 조건이 참일 때만 해당 where 절이 쿼리에 적용되는 것을 원할 수도 있습니다. 예를 들어, 현재의 요청에서 주어진 입력값이 존재할 때에만 `where` 구문을 적용하고 싶을 수도 있습니다. 이 경우 `when` 메소드를 사용할 수 있습니다:

    $role = $request->input('role');

    $users = DB::table('users')
                    ->when($role, function ($query, $role) {
                        return $query->where('role_id', $role);
                    })
                    ->get();


The `when` method only executes the given Closure when the first parameter is `true`. If the first parameter is `false`, the Closure will not be executed.

`when` 메소드는 첫번째 파라미터가 `true` 일때 주어진 클로저를 실행합니다. 첫번째 파라미터가 `false` 라면 클로저는 실행되지 않을 것입니다.

You may pass another Closure as the third parameter to the `when` method. This Closure will execute if the first parameter evaluates as `false`. To illustrate how this feature may be used, we will use it to configure the default sorting of a query:

`when` 메소드의 세번째 파라미터로 또다른 클로저를 전달할 수 있습니다. 이 클로저는 첫번째 파라미터가 `false` 일때 실행됩니다. 다음은 이 기능을 어떻게 사용하는지 보여주기 위한 예로, 쿼리의 기본 정렬을 구성한 것입니다:
    $sortBy = null;

    $users = DB::table('users')
                    ->when($sortBy, function ($query, $sortBy) {
                        return $query->orderBy($sortBy);
                    }, function ($query) {
                        return $query->orderBy('name');
                    })
                    ->get();

<a name="inserts"></a>
## Inserts
## Inserts-삽입

The query builder also provides an `insert` method for inserting records into the database table. The `insert` method accepts an array of column names and values:

쿼리 빌더는 데이터베이스 테이블에 레코드를 추가하는 `insert` 메소드를 제공합니다. `insert` 메소드는 컬럼의 이름과 값으로 이루어진 배열을 인자로 전달받습니다:

    DB::table('users')->insert(
        ['email' => 'john@example.com', 'votes' => 0]
    );

You may even insert several records into the table with a single call to `insert` by passing an array of arrays. Each array represents a row to be inserted into the table:

또한 한번의 `insert` 메소드 호출로 테이블에 여러 레코드를 추가하기 위해서 배열의 배열을 전달 할 수도 있습니다. 각각의 배열은 테이블에 추가되고자 하는 row 를 나타냅니다:

    DB::table('users')->insert([
        ['email' => 'taylor@example.com', 'votes' => 0],
        ['email' => 'dayle@example.com', 'votes' => 0]
    ]);

#### Auto-Incrementing IDs
#### Auto-Incrementing IDs

If the table has an auto-incrementing id, use the `insertGetId` method to insert a record and then retrieve the ID:

테이블이 auto-incrementing id를 가지고 있다면 `insertGetId` 메소드를 사용하여 레코드를 추가하고, 추가된 ID를 획득할 수 있습니다:

    $id = DB::table('users')->insertGetId(
        ['email' => 'john@example.com', 'votes' => 0]
    );

> {note} When using PostgreSQL the `insertGetId` method expects the auto-incrementing column to be named `id`. If you would like to retrieve the ID from a different "sequence", you may pass the column name as the second parameter to the `insertGetId` method.

> {note} PostgreSQL에서 `insertGetId` 메소드를 사용하는 경우 auto-incrementing 컬럼의 이름은 `id` 이어야 합니다. 다른 이름의 ID를 원한다면는 `insertGetId` 메서드의 두 번째 인자로 컬름 이름을 전달 하십시오.

<a name="updates"></a>
## Updates
## Updates-수정

In addition to inserting records into the database, the query builder can also update existing records using the `update` method. The `update` method, like the `insert` method, accepts an array of column and value pairs containing the columns to be updated. You may constrain the `update` query using `where` clauses:

데이터베이스에 레코드를 삽입하는 것에 더해서 쿼리 빌더는 당연히 이미 존재하는 레코드를 `update` 메소드를 사용하여 변경할 수 있습니다. `update` 메소드는 `insert` 메소드와 마찬가지로, 업데이트 하기 위한 컬럼과 컬럼에 대한 값의 쌍으로 이루어진 배열을 인자로 전달받습니다. `update` 쿼리에 `where` 구문을 사용하여 범위를 제한할 수도 있습니다:

    DB::table('users')
                ->where('id', 1)
                ->update(['votes' => 1]);

#### Update Or Insert
####  Updates-수정 또는 Inserts-삽입

Sometimes you may want to update an existing record in the database or create it if no matching record exists. In this scenario, the `updateOrInsert` method may be used. The `updateOrInsert` method accepts two arguments: an array of conditions by which to find the record, and an array of column and value pairs containing the columns to be updated.

경우에 따라 데이터베이스의 기존 레코드를 업데이트하거나 일치하는 레코드가 없는 경우 레코드를 만들 수도 있습니다. 이 시나리오에서는 `updateOrInsert` 메소드를 사용할 수 있습니다. `updateOrInsert` 메소드는 두 개의 인수, 즉 레코드를 찾는 조건 배열과 업데이트 될 컬럼을 포함하는 컬럼과 값 쌍의 배열을 받아들입니다.

The `updateOrInsert` method will first attempt to locate a matching database record using the first argument's column and value pairs. If the record exists, it will be updated with the values in the second argument. If the record can not be found, a new record will be inserted with the merged attributes of both arguments:

`updateOrInsert` 메소드는 첫 번째 인자의 컬럼과 값의 쌍을 사용하여 일치하는 데이터베이스 레코드를 찾습니다. 레코드가 있으면 두 번째 인수의 값으로 업데이트됩니다. 레코드를 찾을 수 없으면 두 레코드의 병합 된 특성과 함께 새 레코드가 삽입됩니다.

    DB::table('users')
        ->updateOrInsert(
            ['email' => 'john@example.com', 'name' => 'John'],
            ['votes' => '2']
        );

<a name="updating-json-columns"></a>
### Updating JSON Columns
### JSON 컬럼 업데이트

When updating a JSON column, you should use `->` syntax to access the appropriate key in the JSON object. This operation is supported on MySQL 5.7+ and PostgreSQL 9.5+:

JSON 컬럼을 업데이트 할때에는 JSON 객체의 해당 키에 엑세스하기 위해서 `->` 문법을 사용해야 합니다. 이 작업은 MySQL 5.7 이상, PostgreSQL 9.5 이상 에서만 지원합니다:

    DB::table('users')
                ->where('id', 1)
                ->update(['options->enabled' => true]);

<a name="increment-and-decrement"></a>
### Increment & Decrement
### 컬럼값의 증가 & 감소

The query builder also provides convenient methods for incrementing or decrementing the value of a given column. This is a shortcut, providing a more expressive and terse interface compared to manually writing the `update` statement.

쿼리빌더는 주어진 컬럼의 값을 증가 또는 감소하는 편리한 메소드를 제공합니다. 이는 단축키와 같으며, 수종으로 `update` 구문을 작성하는 것과 비교하면 보다 간결한 인터페이스를 제공합니다.

Both of these methods accept at least one argument: the column to modify. A second argument may optionally be passed to control the amount by which the column should be incremented or decremented:

이 두개의 메소드는 최소한 하나의 인자를 전달 받습니다: 수정하고자 하는 컬럼. 두번째 인자는 선택적으로 전달되며, 컬럼이 증가되어야할 또는 감소되어야할 양을 나타냅니다.

    DB::table('users')->increment('votes');

    DB::table('users')->increment('votes', 5);

    DB::table('users')->decrement('votes');

    DB::table('users')->decrement('votes', 5);

You may also specify additional columns to update during the operation:

또한 이 작업을 수행동안 업데이트 되어야할 컬럼을 추가적으로 지정할 수도 있습니다:

    DB::table('users')->increment('votes', 1, ['name' => 'John']);

<a name="deletes"></a>
## Deletes
## Deletes-삭제

The query builder may also be used to delete records from the table via the `delete` method. You may constrain `delete` statements by adding `where` clauses before calling the `delete` method:

쿼리빌더는 테이블에서 레코드를 삭제하는데 `delete` 메소드를 사용할 수 있습니다. `delete` 구문에서는 `delete` 메소드를 호출하기 전에 `where` 절을 추가하여 제약사항을 설정할 수 있습니다:

    DB::table('users')->delete();

    DB::table('users')->where('votes', '>', 100)->delete();

If you wish to truncate the entire table, which will remove all rows and reset the auto-incrementing ID to zero, you may use the `truncate` method:

모든 데이터를 삭제하고 auto-incrementing ID를 0으로 초기화 하는 것과 같이, 테이블 전체를 비우고자 한다면 `truncate` 메소드를 사용할 수 있습니다:

    DB::table('users')->truncate();

<a name="pessimistic-locking"></a>
## Pessimistic Locking
## Pessimistic Locking

The query builder also includes a few functions to help you do "pessimistic locking" on your `select` statements. To run the statement with a "shared lock", you may use the `sharedLock` method on a query. A shared lock prevents the selected rows from being modified until your transaction commits:

쿼리빌더는 또한 여러분이 `select` 구문에 “Pessimistic Locking-배타적 잠금”을 설정을 할 수 있도록 도와주는 몇가지 기능을 포함하고 있습니다. "공유 lock"과 함께 구문을 실행하기 위해서, 쿼리에 `sharedLock` 메소드를 사용할 수 있습니다. 공유 lock 은 선택된 row 가 트랜젝션이 커밋되기 전까지 수정되는 것을 방지합니다:

    DB::table('users')->where('votes', '>', 100)->sharedLock()->get();

Alternatively, you may use the `lockForUpdate` method. A "for update" lock prevents the rows from being modified or from being selected with another shared lock:

이대신에, `lockForUpdate` 메소드를 사용할 수 있습니다. "수정을 위한" lock 은 row 가 수정되는 것 또는 다른 공유 lock 에 의해서 선택되는 것을 방지합니다:

    DB::table('users')->where('votes', '>', 100)->lockForUpdate()->get();

<a name="debugging"></a>
## Debugging
## 디버깅

You may use the `dd` or `dump` methods while building a query to dump the query bindings and SQL. The `dd` method will display the debug information and then stop executing the request. The `dump` method will display the debug information but allow the request to keep executing:

쿼리를 작성하는 동안 쿼리 바인딩과 SQL을 출력하기 위해 `dd` 또는 `dump` 메소드를 사용 할 수 있습니다. `dd` 메소드는 디버그 정보를 표시하고 요청 실행을 중단합니다. `dump` 메소드는 디버그 정보를 보여 주지만 요청이 계속 실행되도록합니다.

    DB::table('users')->where('votes', '>', 100)->dd();

    DB::table('users')->where('votes', '>', 100)->dump();
