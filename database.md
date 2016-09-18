# 기본적인 데이터베이스 사용법(Basic Database Usage)

- [설정하기](#configuration)
- [읽기용 / 쓰기용 커넥션](#read-write-connections)
- [쿼리 실행](#running-queries)
- [데이터베이스 트랜잭션](#database-transactions)
- [커넥션 엑세스](#accessing-connections)
- [쿼리 로깅](#query-logging)

<a name="configuration"></a>
## 설정하기

라라벨은 데이터베이스 연결과 쿼리 사용을 매우 쉽게 만들어줍니다. 데이터베이스 설정 파일은 `config/database.php`입니다. 이 파일에서 모든 데이터베이스 커넥션에 대한 설정을 정의하고 기본적으로 사용할 커넥션을 지정할 수 있습니다. 이 파일에서는 모든 지원하는 데이터베이스 예제가 들어 있습니다.

현재 라라벨이 지원하는 데이터베이스 시스템은 : MySQL, Postgres, SQLite 그리고 SQL Server입니다.

<!--chak-comment-기본적인-데이터베이스-사용법(Basic-Database-Usage)-설정하기-->

<a name="read-write-connections"></a>
## 읽기용 / 쓰기용 커넥션

SELECT문에서 사용하는 데이터베이스와 INSERT, UPDATE 그리고 DELETE문을 사용하는 데이터베이스에 다른 연결을 사용할고 싶은 경우도 있습니다. Raw 쿼리를 사용하든, 쿼리 빌더 또는 Eloquent ORM을 사용하든 적절한 연결들을 사용할 수 있습니다. 

다음은 어떻게 read / write 커넥션을 설정하는지에 대한 예제입니다:

	'mysql' => [
		'read' => [
			'host' => '192.168.1.1',
		],
		'write' => [
			'host' => '196.168.1.2'
		],
		'driver'    => 'mysql',
		'database'  => 'database',
		'username'  => 'root',
		'password'  => '',
		'charset'   => 'utf8',
		'collation' => 'utf8_unicode_ci',
		'prefix'    => '',
	],

설정 배열에 `read`와 `write` 두 개의 키가 추가 된것을 참고하십시오. 이 키들은 `host`라는 싱글 키를 포함하는 배열 값을 갖는다: `read`와 `write` 연결에대한 나머지 데이터베이스 옵션들은 기본 `mysql` 배열에서 합쳐(merge)집니다. 따라서 메인 배열값들 중에서 재정의하고자하는 것들만 `read`와 `write` 배열에서 입력하면 됩니다. 위의 경우에서는 `192.168.1.1` 호스트는 “read” 커넥션에서 사용되고, `192.168.1.2` 호스트는 “write” 커넥션에서 사용되어 집니다. 메인 `mysql`설정 배열에 포함된 데이터베이스 연결정보, 프리픽스, 캐릭터 셋 등 다른 모든 옵션들은 양쪽연결에서 모두 공유합니다 .

<!--chak-comment-기본적인-데이터베이스-사용법(Basic-Database-Usage)-읽기용---쓰기용-커넥션-->

<a name="running-queries"></a>
## 쿼리 실행

일단 데이터베이스 연결을 설정하면 `DB` 파사드를 사용해서 쿼리를 실행 할 수 있습니다.

#### Select 쿼리 실행하기

	$results = DB::select('select * from users where id = ?', [1]);

`select` 메소드는 항상 결과를 `배열`로 반환합니다.

또한, named 바인딩을 사용한 쿼리를 실행할 수도 있습니다:

	$results = DB::select('select * from users where id = :id', ['id' => 1]);

#### Insert문 실행

	DB::insert('insert into users (id, name) values (?, ?)', [1, 'Dayle']);

#### Update문 실행

	DB::update('update users set votes = 100 where name = ?', ['John']);

#### Delete문 실행

	DB::delete('delete from users');

> **참고:** `update`와 `delete`문은 해당 작업이 영향을 끼친 행(row)의 수를 반환합니다.

#### 일반적인 SQL문 실행

	DB::statement('drop table users');

#### 쿼리 이벤트 받기

`DB::listen` 메소드를 사용하여 쿼리 이벤트를 받을 수 있습니다:

	DB::listen(function($sql, $bindings, $time)
	{
		//
	});

<!--chak-comment-기본적인-데이터베이스-사용법(Basic-Database-Usage)-쿼리-실행-->

<a name="database-transactions"></a>
## 데이터베이스 트랜잭션

일련의 작업들을 하나의 데이터베이스 트랜잭션으로 실행할때 `transaction`메소드를 사용하면 됩니다:

	DB::transaction(function()
	{
		DB::table('users')->update(['votes' => 1]);

		DB::table('posts')->delete();
	});

> **참고:** `transaction` 메소드에 넘겨진 클로저에 어떤 예외가 발생한 경우 자동으로 트랜잭션을 롤백시킵니다.

때로는 트랜잭션을 직접 시작해야하는 경우도 있습니다:

	DB::beginTransaction();

`rollback`메소드로 트랜잭션을 롤백 할 수 있습니다:

	DB::rollback();

마찬가지로, `commit`메소드로 트랜잭션을 commit할 수 있습니다.

	DB::commit();

<!--chak-comment-기본적인-데이터베이스-사용법(Basic-Database-Usage)-데이터베이스-트랜잭션-->

<a name="accessing-connections"></a>
## 커넥션 엑세스

여러 연결들을 사용할때 `DB::connection`메소드를 통해 특정 커넥션에 액세스 할 수 있습니다:

	$users = DB::connection('foo')->select(...);

또한, PDO 인스턴스로 액세스 할 수 있습니다:

	$pdo = DB::connection()->getPdo();

때로는 주어진 데이터데이스에 다시 연결해야 할 수도 있습니다:

	DB::reconnect('foo');

PDO인스턴스의 `max_connections`한계를 넘어서서 데이터베이스 연결을 끊어야하는경우 `disconnect`메소드를 사용하면 됩니다:

	DB::disconnect('foo');

<!--chak-comment-기본적인-데이터베이스-사용법(Basic-Database-Usage)-커넥션-엑세스-->

<a name="query-logging"></a>
## 쿼리 로깅

라라벨은 선택적으로 현재 요청에 대해서 실행되는 모든 쿼리를 메모리에 로깅할 수 있습니다. 다수의 행을 삽입 하는 경우와 어플리케이션이 그 과도한 메모리를 사용할 수 있다는 점을 유의해야 합니다. 로깅을 활성화 하기 위해서는 `enableQueryLog` 메소드를 사용하면 됩니다:

	DB::connection()->enableQueryLog();

실행된 쿼리들의 배열을 얻으려면 `getQueryLog`메소드를 사용하면 됩니다:

	$queries = DB::getQueryLog();

<!--chak-comment-기본적인-데이터베이스-사용법(Basic-Database-Usage)-쿼리-로깅-->
