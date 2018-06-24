# Query Builder 쿼리빌더

- [Introduction 소개](#introduction)
- [Selects-선택](#selects)
- [Joins-조인](#joins)
- [Advanced Wheres-복잡한 조건문](#advanced-wheres)
- [Aggregates-집계문](#aggregates)
- [Raw Expressions-직접질의](#raw-expressions)
- [Inserts-삽입](#inserts)
- [Updates-수정](#updates)
- [Deletes-삭제](#deletes)
- [Unions-쿼리 결합](#unions)
- [Pessimistic Locking](#pessimistic-locking)

<a name="introduction"></a>
## Introduction 소개

The database query builder provides a convenient, fluent interface to creating and running database queries. 데이터베이스 쿼리 빌더는 데이터베이스 쿼리들을 만들고 운영하는데 다양한 인터페이스의 편의기능를 제공합니다. It can be used to perform most database operations in your application, and works on all supported database systems. 여러분들의 애플리케이션에서 운영하는 대부분의 데이터베이스 시스템에서 잘 작동합니다.

> **Note 참고:** The Laravel query builder uses PDO parameter binding throughout to protect your application against SQL injection attacks. 라라벨의 쿼리 빌더는 PDO 파라미터 바인딩을 사용하여 SQL injection 공격을 방지합니다.  There is no need to clean strings being passed as bindings. 따라서 쿼리에 바인딩할 문자열들을 따로 정리하고 전달할 필요가 없습니다.

<a name="selects"></a>
## Selects 

#### Retrieving All Rows From A Table 한 테이블에서 모든 행들을 가져오기

	$users = DB::table('users')->get();

	foreach ($users as $user)
	{
		var_dump($user->name);
	}

#### Chunking Results From A Table 테이블 결과데이터 분할

	DB::table('users')->chunk(100, function($users)
	{
		foreach ($users as $user)
		{
			//
		}
	});

You may stop further chunks from being processed by returning `false` from the `Closure`: `Closure`에서 `false` 를 반환하여 결과 분할을 중단 할 수 있습니다.

	DB::table('users')->chunk(100, function($users)
	{
		//

		return false;
	});

#### Retrieving A Single Row From A Table 테이블에서 하나의 결과 가져오기

	$user = DB::table('users')->where('name', 'John')->first();

	var_dump($user->name);

#### Retrieving A Single Column From A Row 한개의 행에서 하나의 컬럼만을 가져오기

	$name = DB::table('users')->where('name', 'John')->pluck('name');

#### Retrieving A List Of Column Values 컬럼 값들의 리스트를 가져오기

	$roles = DB::table('roles')->lists('title');

This method will return an array of role titles. 이 메소드는 role 테이블의 title들의 배열을 반환할 것입니다. You may also specify a custom key column for the returned array: 반환되는 배열에 특정 키의 컬럼을 지정할 수도 있습니다. 

	$roles = DB::table('roles')->lists('title', 'name');

#### Specifying A Select Clause Select문 지정하기

	$users = DB::table('users')->select('name', 'email')->get();

	$users = DB::table('users')->distinct()->get();

	$users = DB::table('users')->select('name as user_name')->get();

#### Adding A Select Clause To An Existing Query 이미 있는 쿼리에 select 문 추가하기

	$query = DB::table('users')->select('name');

	$users = $query->addSelect('age')->get();

#### Using Where Operators 
#### where 구문 사용

	$users = DB::table('users')->where('votes', '>', 100)->get();

#### Or Statements 
#### orWhere

	$users = DB::table('users')
	                    ->where('votes', '>', 100)
	                    ->orWhere('name', 'John')
	                    ->get();

#### Using Where Between
#### whereBetween 사용

	$users = DB::table('users')
	                    ->whereBetween('votes', [1, 100])->get();

#### Using Where Not Between
#### whereNotBetween 사용하기

	$users = DB::table('users')
	                    ->whereNotBetween('votes', [1, 100])->get();

#### Using Where In With An Array
#### 배열로 whereIn 사용하기
	$users = DB::table('users')
	                    ->whereIn('id', [1, 2, 3])->get();

	$users = DB::table('users')
	                    ->whereNotIn('id', [1, 2, 3])->get();

#### Using Where Null To Find Records With Unset Values
#### 값이 설정되지 않은 레코드를 찾는데 whereNull 사용하기

	$users = DB::table('users')
	                    ->whereNull('updated_at')->get();

#### Dynamic Where Clauses
#### 동적 where 구문

You may even use "dynamic" where statements to fluently build where statements using magic methods: 매직 매소드를 사용하여 where 구문을 동적으로 구성할 수도 있습니다. 

	$admin = DB::table('users')->whereId(1)->first();

	$john = DB::table('users')
	                    ->whereIdAndEmail(2, 'john@doe.com')
	                    ->first();

	$jane = DB::table('users')
	                    ->whereNameOrAge('Jane', 22)
	                    ->first();

#### Order By, Group By, And Having

	$users = DB::table('users')
	                    ->orderBy('name', 'desc')
	                    ->groupBy('count')
	                    ->having('count', '>', 100)
	                    ->get();

#### Offset & Limit

	$users = DB::table('users')->skip(10)->take(5)->get();

<a name="joins"></a>
## Joins

The query builder may also be used to write join statements. 쿼리 빌더를 사용해서 조인문(join statement)을 만들수 있습니다. Take a look at the following examples: 다음 예들을 살펴봅시다.

#### Basic Join Statement
#### 기본적인 Join문

	DB::table('users')
	            ->join('contacts', 'users.id', '=', 'contacts.user_id')
	            ->join('orders', 'users.id', '=', 'orders.user_id')
	            ->select('users.id', 'contacts.phone', 'orders.price')
	            ->get();

#### Left Join Statement
#### Left Join 
	DB::table('users')
		    ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
		    ->get();

You may also specify more advanced join clauses:
더 복잡한 join도 지정할 수 있습니다. 

	DB::table('users')
	        ->join('contacts', function($join)
	        {
	        	$join->on('users.id', '=', 'contacts.user_id')->orOn(...);
	        })
	        ->get();

If you would like to use a "where" style clause on your joins, you may use the `where` and `orWhere` methods on a join. 조인문에서 "where”구문을 사용하고 싶으면 `where`과 `orwhere` method를 사용하십시오. Instead of comparing two columns, these methods will compare the column against a value: 이러한 메소드는 두개의 컬럼을 비교 대신에 주어진 값과 컬럼을 비교합니다.

	DB::table('users')
	        ->join('contacts', function($join)
	        {
	        	$join->on('users.id', '=', 'contacts.user_id')
	        	     ->where('contacts.user_id', '>', 5);
	        })
	        ->get();

<a name="advanced-wheres"></a>
## Advanced Wheres 더 복잡한 조건문

#### Parameter Grouping 

Sometimes you may need to create more advanced where clauses such as "where exists" or nested parameter groupings. 가끔은 “where exists”나 중첩된 파라미터 그루핑과 같이 복잡한 조건문을 사용해야 할 필요가 있습니다. The Laravel query builder can handle these as well: 라라벨의 쿼리 빌더는 다음과 같이 사용할 수 있습니다. 

	DB::table('users')
	            ->where('name', '=', 'John')
	            ->orWhere(function($query)
	            {
	            	$query->where('votes', '>', 100)
	            	      ->where('title', '<>', 'Admin');
	            })
	            ->get();

The query above will produce the following SQL: 위의 쿼리는 다음과 같은 SQL을 생성합니다. 

	select * from users where name = 'John' or (votes > 100 and title <> 'Admin')

#### Exists Statements 
#### Exist 구문

	DB::table('users')
	            ->whereExists(function($query)
	            {
	            	$query->select(DB::raw(1))
	            	      ->from('orders')
	            	      ->whereRaw('orders.user_id = users.id');
	            })
	            ->get();

The query above will produce the following SQL: 위의 쿼리는 다음과 같은 SQL을 생성합니다. 

	select * from users
	where exists (
		select 1 from orders where orders.user_id = users.id
	)

<a name="aggregates"></a>
## Aggregates 집계

The query builder also provides a variety of aggregate methods, such as `count`, `max`, `min`, `avg`, and `sum`. 쿼리 빌더는 또한 `count`, `max`, `min`, `avg`, 그리고`sum`과 같은 집계를 위한 메소드들을 제공하고 있습니다. 

#### Using Aggregate Methods
#### 집계를 위한 메소드 사용하기

	$users = DB::table('users')->count();

	$price = DB::table('orders')->max('price');

	$price = DB::table('orders')->min('price');

	$price = DB::table('orders')->avg('price');

	$total = DB::table('users')->sum('votes');

<a name="raw-expressions"></a>
## Raw Expressions
## 직접 질의구문

Sometimes you may need to use a raw expression in a query. 때때로 직적쿼리를 생성하여 사용할 필요가 있을수도 있습니다. These expressions will be injected into the query as strings, so be careful not to create any SQL injection points! 이러한 구문은 문자열이 쿼리에 그대로 삽입되기 때문에 SQL 인젝션 공격에 주의해야합니다. To create a raw expression, you may use the `DB::raw` method: 직접 질의 구문을 생성하기 위해서는 `DB::raw` 메소드를 사용하면 됩니다. 

#### Using A Raw Expression
#### 직접 질의 구문 사용하기 

	$users = DB::table('users')
	                     ->select(DB::raw('count(*) as user_count, status'))
	                     ->where('status', '<>', 1)
	                     ->groupBy('status')
	                     ->get();

<a name="inserts"></a>
## Inserts

#### Inserting Records Into A Table 
#### 테이블에 새로운 레코드 추가하기

	DB::table('users')->insert(
		['email' => 'john@example.com', 'votes' => 0]
	);

#### Inserting Records Into A Table With An Auto-Incrementing ID
#### Auto-Incrementing ID 방식 테이블에 레코드 추가하기

If the table has an auto-incrementing id, use `insertGetId` to insert a record and retrieve the id: 테이블에 auto-incrementing id 가 있다면 `insertGetId` 를 사용하여 레코드를 추가하고 ID를 얻을 수 있습니다. 

	$id = DB::table('users')->insertGetId(
		['email' => 'john@example.com', 'votes' => 0]
	);

> **Note 주의:** When using PostgreSQL the insertGetId method expects the auto-incrementing column to be named "id". PostgreSQL에 insertGetId 메소드를 사용하는 경우에는 auto-incrementing 컬럼의 이름을 "id"로 지정하십시오.

#### Inserting Multiple Records Into A Table
#### 한번에 여러 레코드들을 추가하기

	DB::table('users')->insert([
		['email' => 'taylor@example.com', 'votes' => 0],
		['email' => 'dayle@example.com', 'votes' => 0]
	]);

<a name="updates"></a>
## Updates

#### Updating Records In A Table 한 테이블에 레코드들 업데이트하기

	DB::table('users')
	            ->where('id', 1)
	            ->update(['votes' => 1]);

#### Incrementing or decrementing a value of a column
#### 컬럼값의 중가 또는 감소

	DB::table('users')->increment('votes');

	DB::table('users')->increment('votes', 5);

	DB::table('users')->decrement('votes');

	DB::table('users')->decrement('votes', 5);

You may also specify additional columns to update:
또한 업데이트할 추가적인 컬럼을 지정할 수도 있습니다. 

	DB::table('users')->increment('votes', 1, ['name' => 'John']);

<a name="deletes"></a>
## Deletes

#### Deleting Records In A Table 테이블에서 레코드 하나 삭제하기

	DB::table('users')->where('votes', '<', 100)->delete();

#### Deleting All Records From A Table 테이블에서 전체 레코드 삭제하기 

	DB::table('users')->delete();

#### Truncating A Table 테이블 비우기

	DB::table('users')->truncate();

<a name="unions"></a>
## Unions

The query builder also provides a quick way to "union" two queries together:
쿼리 빌더는 두 개의 쿼리를 결합 “union”하는 간편한 방법을 제공합니다.

	$first = DB::table('users')->whereNull('first_name');

	$users = DB::table('users')->whereNull('last_name')->union($first)->get();

The `unionAll` method is also available, and has the same method signature as `union`. `unionAll` 메소드도 사용가능하며 `union`과 동일하게 사용합니다.

<a name="pessimistic-locking"></a>
## Pessimistic Locking

The query builder includes a few functions to help you do "pessimistic locking" on your SELECT statements. 쿼리빌더는 여러분이 SELECT 구문에 “Pessimistic Locking-배타적 잠금” 설정을 할 수 있도록 도와주는몇가지 기능을 포함하고 있습니다. 

To run the SELECT statement with a "shared lock", you may use the `sharedLock` method on a query: “shared lock” 에서 SELECT 문을 실행하려면 `sharedLock` 메소드를 쿼리에 지정하십시오.

	DB::table('users')->where('votes', '>', 100)->sharedLock()->get();

To "lock for update" on a SELECT statement, you may use the `lockForUpdate` method on a query: SELECT문을 "lock for update" 하려면 `lockForUpdate` 메소드를 쿼리에 지정하십시오. 

	DB::table('users')->where('votes', '>', 100)->lockForUpdate()->get();
