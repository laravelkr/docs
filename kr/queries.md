# Database: Query Builder
# 데이터베이스: 쿼리 빌더

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Retrieving Results](#retrieving-results)
- [결과 질의](#retrieving-results)
    - [Aggregates](#aggregates)
    - [Aggregates-집계문](#aggregates)
- [Selects](#selects)
- [Selects-선택](#selects)
- [Joins](#joins)
- [Joins-조인](#joins)
- [Unions](#unions)
- [Unions-유니온](#unions)
- [Where Clauses](#where-clauses)
- [Where-구문](#where-clauses)
    - [Advanced Where Clauses](#advanced-where-clauses)
    - [Advanced Where Clauses](#advanced-where-clauses)
- [Ordering, Grouping, Limit, & Offset](#ordering-grouping-limit-and-offset)
- [Ordering, Grouping, Limit, & Offset](#ordering-grouping-limit-and-offset)
- [Inserts](#inserts)
- [Inserts-삽입](#inserts)
- [Updates](#updates)
- [Updates-수정](#updates)
- [Deletes](#deletes)
- [Deletes-삭제](#deletes)
- [Pessimistic Locking](#pessimistic-locking)
- [Pessimistic Locking](#pessimistic-locking)

<a name="introduction"></a>
## Introduction
## 시작하기

The database query builder provides a convenient, fluent interface to creating and running database queries. It can be used to perform most database operations in your application, and works on all supported database systems.

데이터베이스 쿼리 빌더는 데이터베이스 쿼리들을 만들고 운영하는데 다양한 인터페이스의 편의기능를 제공합니다. 여러분들의 애플리케이션에서 운영하는 대부분의 데이터베이스 시스템에서 잘 작동합니다.

> **Note:** The Laravel query builder uses PDO parameter binding to protect your application against SQL injection attacks. There is no need to clean strings being passed as bindings.

> **참고:** 라라벨의 쿼리 빌더는 PDO 파라미터 바인딩을 사용하여 SQL injection 공격을 방지합니다. 따라서 쿼리에 바인딩할 문자열들을 따로 정리하고 전달할 필요가 없습니다.

<a name="retrieving-results"></a>
## Retrieving Results
## 결과 질의

#### Retrieving All Rows From A Table
#### 한 테이블에서 모든 행들을 가져오기

To begin a fluent query, use the `table` method on the `DB` facade. The `table` method returns a fluent query builder instance for the given table, allowing you to chain more constraints onto the query and then finally get the results. In this example, let's just `get` all records from a table:

쿼리 구성을 시작하기 위해서 `DB` 파사드의 `table` 메소드를 사용합니다. `table` 메소드는 주어진 테이블에 대한 쿼리빌더 인스턴스를 반환하고, 결과를 가져올 수 있도록 다양한 제약을 메소드 체이닝 형태로 구성할 수 있습니다. 다음과 같이, 테이블의 모든 레코드를 `get` 을 통해서 가져올 수 있습니다. 

    <?php

    namespace App\Http\Controllers;

    use DB;
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

Like [raw queries](/docs/{{version}}/database), the `get` method returns an `array` of results where each result is an instance of the PHP `StdClass` object. You may access each column's value by accessing the column as a property of the object:

[raw 쿼리](/docs/{{version}}/database)와 같이 `get` 메소드는 각각의 결과에 대응하는 PHP `StdClass` 객체 인스턴스의 배열을 결과로 반환합니다. 컬럼을 나타내는 객체의 속성에 엑세스 하여 컬럼의 값을 확인할 수 있습니다: 

    foreach ($users as $user) {
        echo $user->name;
    }

#### Retrieving A Single Row / Column From A Table
#### 테이블에서 하나의 결과 / 컬럼 가져오기

If you just need to retrieve a single row from the database table, you may use the `first` method. This method will return a single `StdClass` object:

데이터베이스 테이블에서 하나의 row을 가져오고자 한다면, `first` 메소드를 사용하면 됩니다. 이 메소드는 하나의 `StdClass` 객체를 반환할 것입니다: 

    $user = DB::table('users')->where('name', 'John')->first();

    echo $user->name;

If you don't even need an entire row, you may extract a single value from a record using the `value` method. This method will return the value of the column directly:

전체 row 가 필요하지 않다면, `value` 메소드를 사용하여 레코드에서 하나의 값만 추출할 수 있습니다. 메소드는 컬럼의 직접적인 값을 반환할 것입니다: 

    $email = DB::table('users')->where('name', 'John')->value('email');

#### Chunking Results From A Table
#### 테이블에서 결과데이터 분할하기

If you need to work with thousands of database records, consider using the `chunk` method. This method retrieves a small "chunk" of the results at a time, and feeds each chunk into a `Closure` for processing. This method is very useful for writing [Artisan commands](/docs/{{version}}/artisan) that process thousands of records. For example, let's work with the entire `users` table in chunks of 100 records at a time:

데이터베이스 레코드가 많은 작업을 수행해야 한다면, `chunk` 메소드를 사용하는 것을 고려하십시오. 이 메소드는 한번에 결과에 대한 하나의 작은 "chunk" 를 획득하고, 각각의 chunk를 `Closure` 를 통해서 처리합니다. 이 메소드는 다수의 레코드를 처리하는 [아티즌 명령어](/docs/{{version}}/artisan)를 작성하는데 유용합니다. 예를 들어, 전체 `users` 테이블에서 한번에 100개의 레코드를 가져온다고 해보겠습니다. 

    DB::table('users')->chunk(100, function($users) {
        foreach ($users as $user) {
            //
        }
    });

You may stop further chunks from being processed by returning `false` from the `Closure`:

`Closure` 에서 `false`를 반환하여, 더이상의 chunk를 처리하지 않도록 중단할 수 있습니다:

    DB::table('users')->chunk(100, function($users) {
        // Process the records...

        return false;
    });

#### Retrieving A List Of Column Values
#### 컬럼 값의 목록 조회하기

If you would like to retrieve an array containing the values of a single column, you may use the `lists` method. In this example, we'll retrieve an array of role titles:

한개의 컬럼의 값들을 포함하고 있는 배열을 조회하고자 한다면, `lists` 메소드를 사용할 수 있습니다. 이 예제에서는 사용자의 역할의 이름의 배열을 조회할 것입니다: 

    $titles = DB::table('roles')->lists('title');

    foreach ($titles as $title) {
        echo $title;
    }

 You may also specify a custom key column for the returned array:

 반환될 배열에서 사용될 특정한 컬럼 키를 지정할 수도 있습니다. 

    $roles = DB::table('roles')->lists('title', 'name');

    foreach ($roles as $name => $title) {
        echo $title;
    }

<a name="aggregates"></a>
### Aggregates
### Aggregates

The query builder also provides a variety of aggregate methods, such as `count`, `max`, `min`, `avg`, and `sum`. You may call any of these methods after constructing your query:

쿼리빌더는 또한 `count`, `max`, `min`, `avg` 그리고 `sum` 과 같은 다양한 집계 관련 메소드를 제공합니다. 이 메소드들은 쿼리를 생성한 뒤에 호출하면 됩니다: 

    $users = DB::table('users')->count();

    $price = DB::table('orders')->max('price');

Of course, you may combine these methods with other clauses to build your query:

물론, 쿼리를 생성하는데 이 메소드들과 다른 조건문을 합쳐서 사용할 수도 있습니다: 

    $price = DB::table('orders')
                    ->where('finalized', 1)
                    ->avg('price');

<a name="selects"></a>
## Selects
## Selects

#### Specifying A Select Clause
#### Select문 지정하기

Of course, you may not always want to select all columns from a database table. Using the `select` method, you can specify a custom `select` clause for the query:

당연하게도, 항상 데이터베이스의 테이블에서 모든 컬럼을 조회하고자 하지는 않을 것입니다. `select` 메소드를 사용하여, 쿼리에서 `select` 문을 위한 지정을 할 수 있습니다. 

    $users = DB::table('users')->select('name', 'email as user_email')->get();

The `distinct` method allows you to force the query to return distinct results:

`distinct` 메소드는 쿼리가 고유한 결과를 반환하도록 강제할 수 있습니다: 

    $users = DB::table('users')->distinct()->get();

If you already have a query builder instance and you wish to add a column to its existing select clause, you may use the `addSelect` method:

이미 쿼리빌더 인스턴스를 가지고 있고, 존재하는 select 구문에 선택할 컬럼을 추가하고자 한다면, `addSelect` 메소드를 사용할 수 있습니다: 

    $query = DB::table('users')->select('name');

    $users = $query->addSelect('age')->get();

#### Raw Expressions
#### 직접 질의구문

Sometimes you may need to use a raw expression in a query. These expressions will be injected into the query as strings, so be careful not to create any SQL injection points! To create a raw expression, you may use the `DB::raw` method:

때로는, 쿼리에서 Raw Expressions를 사용하고자 할 수도 있습니다. 이러한 구문들은 쿼리에 문자열 형태로 주입될 것이기 때문에, SQL 인젝션 공격을 유발하지 않도록 주의하시기 바랍니다! raw expression 을 생성하기 위해서는 `DB::raw` 메소드를 사용할 수 있습니다: 

    $users = DB::table('users')
                         ->select(DB::raw('count(*) as user_count, status'))
                         ->where('status', '<>', 1)
                         ->groupBy('status')
                         ->get();

<a name="joins"></a>
## Joins
## Joins

#### Inner Join Statement
#### Inner Join 구문

The query builder may also be used to write join statements. To perform a basic SQL "inner join", you may use the `join` method on a query builder instance. The first argument passed to the `join` method is the name of the table you need to join to, while the remaining arguments specify the column constraints for the join. Of course, as you can see, you can join to multiple tables in a single query:

쿼리 빌더를 사용해서 조인문(join statement)을 만들수 있습니다. 기본적인 SQL "inner join" 을 수행하기 위해서는 쿼리 빌더 인스턴스에 `join` 메소드를 사용하면 됩니다. `join` 메소드에 전달되는 첫번째 인자는 join을 수행할 테이블의 이름이며, 구 이후는 join 을 실행할 때 컬럼의 제약 조건입니다. 물론, 다음과 같이 하나의 쿼리에서 여러 테이블을 join 할 수도 있습니다: 

    $users = DB::table('users')
                ->join('contacts', 'users.id', '=', 'contacts.user_id')
                ->join('orders', 'users.id', '=', 'orders.user_id')
                ->select('users.*', 'contacts.phone', 'orders.price')
                ->get();

#### Left Join Statement
#### Left Join 구문

If you would like to perform a "left join" instead of an "inner join", use the `leftJoin` method. The `leftJoin` method has the same signature as the `join` method:

"inner join" 대신 "left join" 을 수행하고자 한다면, `leftJoin` 메소드를 사용하십시오. `leftJoin` 메소드는 `join` 메소드와 동일한 구성을 가집니다: 

    $users = DB::table('users')
                ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
                ->get();

#### Advanced Join Statements
#### Advanced Join 구문

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

<a name="unions"></a>
## Unions
## Unions

The query builder also provides a quick way to "union" two queries together. For example, you may create an initial query, and then use the `union` method to union it with a second query:

쿼리 빌더는 두 개의 쿼리를 "union" 하는 간편한 방법을 제공합니다. 다음과 같이 처음에 쿼리를 생성한 다음, 두 번째 쿼리를 결합하기 위하여 `union` 메소드를 사용할 수 있습니다:

    $first = DB::table('users')
                ->whereNull('first_name');

    $users = DB::table('users')
                ->whereNull('last_name')
                ->union($first)
                ->get();

The `unionAll` method is also available and has the same method signature as `union`.

`unionAll` 메소드도 `union` 메소드와 같은 방법으로 사용할 수 있습니다. 

<a name="where-clauses"></a>
## Where Clauses
## Where 절

#### Simple Where Clauses
#### 간단한 Where 절

To add `where` clauses to the query, use the `where` method on a query builder instance. The most basic call to `where` requires three arguments. The first argument is the name of the column. The second argument is an operator, which can be any of the database's supported operators. The third argument is the value to evaluate against the column.

쿼리에 `where` 절을 추가하기 위해서는, 쿼리빌더 인스턴스에 `where` 메소드를 사용하면 됩니다. 가장 기본적인 `where` 메소드 호출 방법에는 3가지 인자가 필요합니다. 첫번째 인자는 컬럼의 이름입니다. 두번째 인자는 데이터베이스가 지원하는 연산자입니다. 세번째 인자는 컬람에 대해 비교하는 값입니다. 

For example, here is a query that verifies the value of the "votes" column is equal to 100:

다음과 같이 "votes" 컬럼이 100과 일치하는 값을 가진 쿼리를 수행합니다: 

    $users = DB::table('users')->where('votes', '=', 100)->get();

For convenience, if you simply want to verify that a column is equal to a given value, you may pass the value directly as the second argument to the `where` method:

보다 편리하게 사용하기 위해서, 컬럼이 주어진 값과 동일한가를 확인하고자 한다면, `where` 메소드의 두번째 인자로 바로 값을 전달할 수도 있습니다: 

    $users = DB::table('users')->where('votes', 100)->get();

Of course, you may use a variety of other operators when writing a `where` clause:

물론, `where`절을 작성할 때 다양한 다른 연산자를 사용할 수 있습니다: 

    $users = DB::table('users')
                    ->where('votes', '>=', 100)
                    ->get();

    $users = DB::table('users')
                    ->where('votes', '<>', 100)
                    ->get();

    $users = DB::table('users')
                    ->where('name', 'like', 'T%')
                    ->get();

#### Or Statements
#### Or 구문

You may chain where constraints together, as well as add `or` clauses to the query. The `orWhere` method accepts the same arguments as the `where` method:

쿼리에 `or` 절을 추가하는 것과 같이 where 제약에 더해서 체이닝 형태로 사용할 수 있습니다. `orWhere` 메소드는 `where` 메소드와 동일한 인자를 전달 받습니다: 

    $users = DB::table('users')
                        ->where('votes', '>', 100)
                        ->orWhere('name', 'John')
                        ->get();

#### Additional Where Clauses
#### 추가적인 Where 구문

**whereBetween**
**whereBetween**

The `whereBetween` method verifies that a column's value is between two values:

`whereBetween` 메소드는 컬럼의 값이 두개의 값 사이의 값인지 확인합니다:

    $users = DB::table('users')
                        ->whereBetween('votes', [1, 100])->get();

**whereNotBetween**
**whereNotBetween**

The `whereNotBetween` method verifies that a column's value lies outside of two values:

`whereNotBetween` 메소드는 컬럼의 값이 두개의 값 사이가 아닌지 확인합니다: 

    $users = DB::table('users')
                        ->whereNotBetween('votes', [1, 100])
                        ->get();

**whereIn / whereNotIn**
**whereIn / whereNotIn**

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

**whereNull / whereNotNull**
**whereNull / whereNotNull**

The `whereNull` method verifies that the value of the given column is `NULL`:

`whereNull`메소드는 주어진 컬럼의 값이 `NULL` 인지 확인합니다: 

    $users = DB::table('users')
                        ->whereNull('updated_at')
                        ->get();

The `whereNotNull` method verifies that the column's value is **not** `NULL`:

`whereNotNull`메소드는 주어진 컬럼의 값이 `NULL`이 **아닌지** 확인합니다: 

    $users = DB::table('users')
                        ->whereNotNull('updated_at')
                        ->get();

<a name="advanced-where-clauses"></a>
## Advanced Where Clauses
## 복잡한 Where 절

#### Parameter Grouping
#### Parameter Grouping

Sometimes you may need to create more advanced where clauses such as "where exists" or nested parameter groupings. The Laravel query builder can handle these as well. To get started, let's look at an example of grouping constraints within parenthesis:

가끔은 "where exists"나 중첩된 파라미터를 그룹으로 묶어서 수행하는것과 같이, 복잡한 조건문을 사용해야 할 필요가 있습니다. 라라벨의 쿼리 빌더는 이 경우 다음과 같이 사용할 수 있습니다. 다음의 괄호 안에 제약조건을 그룹으로 묶는 예제를 살펴보겠습니다. 

    DB::table('users')
                ->where('name', '=', 'John')
                ->orWhere(function ($query) {
                    $query->where('votes', '>', 100)
                          ->where('title', '<>', 'Admin');
                })
                ->get();

As you can see, passing `Closure` into the `orWhere` method instructs the query builder to begin a constraint group. The `Closure` will receive a query builder instance which you can use to set the constraints that should be contained within the parenthesis group. The example above will produce the following SQL:

위에서 보시다 시피, `orWhere` 메소드에 전달된 `Closure`가 쿼리빌더의 제약조건을 그룹으로 묶고 있습니다. 이 `Closure`는 괄호로 포함된 제약조건을 설정하는데 사용할 쿼리빌더 인스턴스를 전달받습니다. 이 예제는 다음과 같은 SQL을 생성할 것입니다: 

    select * from users where name = 'John' or (votes > 100 and title <> 'Admin')

#### Exists Statements
#### Exists 구문

The `whereExists` method allows you to write `where exist` SQL clauses. The `whereExists` method accepts a `Closure` argument, which will receive a query builder instance allowing you to define the query that should be placed inside of the "exists" clause:

`whereExists` 메소드는 SQL 쿼리에 `where exist` 를 작성할 수 있도록 합니다. `whereExists` 메소드는 쿼리에 "exist" 구문을 정의할 수 있도록 쿼리 빌더를 인자로 받아들이는 `Closure`를 인자로 받아들입니다.

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

<a name="ordering-grouping-limit-and-offset"></a>
## Ordering, Grouping, Limit, & Offset
## Ordering, Grouping, Limit, & Offset

#### orderBy
#### orderBy

The `orderBy` method allows you to sort the result of the query by a given column. The first argument to the `orderBy` method should be the column you wish to sort by, while the second argument controls the direction of the sort and may be either `asc` or `desc`:

`orderBy` 메소드는 주어진 컬럼에 대한 쿼리의 결과를 정렬합니다. `orderBy` 메소드의 첫번째 인자는 정렬하고자 하는 컬럼이며, 두번째 인자는 `asc` 또는 `desc`의 정렬 방식을 제어하는 인자가 됩니다. 

    $users = DB::table('users')
                    ->orderBy('name', 'desc')
                    ->get();

#### groupBy / having / havingRaw
#### groupBy / having / havingRaw

The `groupBy` and `having` methods may be used to group the query results. The `having` method's signature is similar to that of the `where` method:

`groupBy` 와 `hanving` 메소드는 쿼리 결과를 그룹화하는데 사용합니다. `having` 메소드는 `where` 메소드와 사용장법이 비슷합니다:

    $users = DB::table('users')
                    ->groupBy('account_id')
                    ->having('account_id', '>', 100)
                    ->get();

The `havingRaw` method may be used to set a raw string as the value of the `having` clause. For example, we can find all of the departments with sales greater than $2,500:

`havingRaw` 메소드는 `having` 절의 값으로 raw 문자열을 설정하는데 사용됩니다. 다음과 같이, $2,500보다 많은 매출을 올린 부서를 찾을 수도 있습니다. 

    $users = DB::table('orders')
                    ->select('department', DB::raw('SUM(price) as total_sales'))
                    ->groupBy('department')
                    ->havingRaw('SUM(price) > 2500')
                    ->get();

#### skip / take
#### skip / take

To limit the number of results returned from the query, or to skip a given number of results in the query (`OFFSET`), you may use the `skip` and `take` methods:

쿼리에서 반환되는 결과의 갯수를 제한하거나, 주어진 갯수만큼 결과를 건너뛰기(`OFFSET`) 위해서는, `skip` 과 `take` 메소드를 사용하면 됩니다:

    $users = DB::table('users')->skip(10)->take(5)->get();

<a name="inserts"></a>
## Inserts
## 삽입

The query builder also provides an `insert` method for inserting records into the database table. The `insert` method accepts an array of column names and values to insert:

쿼리 빌더는 데이터베이스 테이블에 레코드를 추가하는 `insert` 메소드를 제공합니다. `insert` 메소드는 컬럼의 이름과 값으로 이루어진 배열을 인자로 전달받습니다. 

    DB::table('users')->insert(
        ['email' => 'john@example.com', 'votes' => 0]
    );

You may even insert several records into the table with a single call to `insert` by passing an array of arrays. Each array represents a row to be inserted into the table:

또한 한번의 `insert` 메소드 호출로 테이블에 여러 레코드를 추가하기 위해서 배열의 배열을 전달 할 수도 있습니다. 각각의 배열은 테이블에 추가되고자 하는 row 를 나타냅니다. 

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

> **Note:** When using PostgreSQL the insertGetId method expects the auto-incrementing column to be named `id`. If you would like to retrieve the ID from a different "sequence", you may pass the sequence name as the second parameter to the `insertGetId` method.

> **주의:** PostgreSQL에서 insertGetId 메소드를 사용하는 경우 auto-incrementing 컬럼의 이름은 `id` 이어야 합니다. 다른 이름의 ID를 원한다면는 `insertGetId` 메서드의 두 번째 인자로 시퀀스 이름을 전달 하십시오.

<a name="updates"></a>
## Updates
## Updates-수정

Of course, in addition to inserting records into the database, the query builder can also update existing records using the `update` method. The `update` method, like the `insert` method, accepts an array of column and value pairs containing the columns to be updated. You may constrain the `update` query using `where` clauses:

데이터베이스에 레코드를 삽입하는 것에 더해서 쿼리 빌더는 당연히 이미 존재하는 레코드를 `update` 메소드를 사용하여 변경할 수 있습니다. `update` 메소드는 `insert` 메소드와 마찬가지로, 업데이트 하기 위한 컬럼과 컬럼에 대한 값의 쌍으로 이루어진 배열을 인자로 전달받습니다. `update` 쿼리에 `where` 구문을 사용하여 범위를 제한할 수도 있습니다: 

    DB::table('users')
                ->where('id', 1)
                ->update(['votes' => 1]);

#### Increment / Decrement
#### 컬럼값의 증가 / 감소

The query builder also provides convenient methods for incrementing or decrementing the value of a given column. This is simply a short-cut, providing a more expressive and terse interface compared to manually writing the `update` statement.

쿼리빌더는 주어진 컬럼의 값을 증가 또는 감소하는 편리한 메소드를 제공합니다. 이는 간단한 단축키와 같으며, 수종으로 `update` 구문을 작성하는 것과 비교하면 보다 간결한 인터페이스를 제공합니다. 

Both of these methods accept at least one argument: the column to modify. A second argument may optionally be passed to control the amount by which the column should be incremented / decremented.

이 두개의 메소드는 최소한 하나의 인자를 전달 받습니다: 수정하고자 하는 컬럼. 두번째 인자는 선택적으로 전달되며, 컬럼이 증가되어야할 / 감소되어야할 양을 나타냅니다. 

    DB::table('users')->increment('votes');

    DB::table('users')->increment('votes', 5);

    DB::table('users')->decrement('votes');

    DB::table('users')->decrement('votes', 5);

You may also specify additional columns to update during the operation:

또한 이 작업을 수행동안 업데이트 되어야할 컬럼을 추가적으로 지정할 수도 있습니다. 

    DB::table('users')->increment('votes', 1, ['name' => 'John']);

<a name="deletes"></a>
## Deletes
## Deletes-삭제

Of course, the query builder may also be used to delete records from the table via the `delete` method:

당연하게도, 쿼리빌더는 테이블에서 레코드를 삭제하는데 `delete` 메소드를 사용할 수 있습니다. 

    DB::table('users')->delete();

You may constrain `delete` statements by adding `where` clauses before calling the `delete` method:

`delete` 구문에서는 `delete` 메소드를 호출하기 전에 `where` 절을 추가하여 제약사항을 설정할 수 있습니다. 

    DB::table('users')->where('votes', '<', 100)->delete();

If you wish to truncate the entire table, which will remove all rows and reset the auto-incrementing ID to zero, you may use the `truncate` method:

모든 데이터를 삭제하고 auto-incrementing ID를 0으로 초기화 하는 것과 같이, 테이블 전체를 비우고자 한다면 `truncate` 메소드를 사용할 수 있습니다:

    DB::table('users')->truncate();

<a name="pessimistic-locking"></a>
## Pessimistic Locking
## Pessimistic Locking

The query builder also includes a few functions to help you do "pessimistic locking" on your `select` statements. To run the statement with a "shared lock", you may use the `sharedLock` method on a query. A shared lock prevents the selected rows from being modified until your transaction commits:

쿼리빌더는 또한 여러분이 `select` 구문에 “Pessimistic Locking-배타적 잠금”을 설정을 할 수 있도록 도와주는 몇가지 기능을 포함하고 있습니다. "공유 lock"과 함께 구문을 실행하기 위해서, 쿼리에 `sharedLock` 메소드를 사용할 수 있습니다. 공유 lock 은 선택된 row 가 트랜젝션이 커밋되기 전까지 수정되는 것을 방지합니다. 

    DB::table('users')->where('votes', '>', 100)->sharedLock()->get();

Alternatively, you may use the `lockForUpdate` method. A "for update" lock prevents the rows from being modified or from being selected with another shared lock:

이대신에, `lockForUpdate` 메소드를 사용할 수 있습니다. "수정을 위한" lock 은 row 가 수정되는 것 또는 다른 공유 lock 에 의해서 선택되는 것을 방지합니다. 

    DB::table('users')->where('votes', '>', 100)->lockForUpdate()->get();
