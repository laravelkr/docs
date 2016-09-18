# Eloquent ORM

- [소개](#introduction)
- [기본 사용법](#basic-usage)
- [대량 할당](#mass-assignment)
- [인서트, 업데이트, 삭제](#insert-update-delete)
- [소프트 삭제](#soft-deleting)
- [타임스탬프](#timestamps)
- [쿼리 스코프](#query-scopes)
- [글로벌 스코프](#global-scopes)
- [관계](#relationships)
- [관계 쿼리](#querying-relations)
- [Eager 로딩](#eager-loading)
- [관련 모델 삽입](#inserting-related-models)
- [부모의 타임스탬프 업데이트](#touching-parent-timestamps)
- [피벗 테이블 작업](#working-with-pivot-tables)
- [컬렉션](#collections)
- [Accessors & Mutators](#accessors-and-mutators)
- [날짜 변경](#date-mutators)
- [속성 캐스팅](#attribute-casting)
- [모델 이벤트](#model-events)
- [모델 옵저버](#model-observers)
- [모델 URL 생성](#model-url-generation)
- [배열 / JSON으로 변환](#converting-to-arrays-or-json)

<a name="introduction"></a>
## 소개

라라벨에 포함된 Eloquent ORM은 여러분의 데이터베이스와 동작하는 아름답고 심플한 액티브 레코드를 제공합니다. 각각의 데이터베이스 테이블은 이에 해당하는 "모델”을 가지고 있습니다.

시작하기에 앞서 `config/database.php` 에 데이터베이스 커넥션이 설정되어 있는지 확인하십시오.

<!--chak-comment-Eloquent-ORM-소개-->

<a name="basic-usage"></a>
## 기본 사용법

시작하기 위해서 Eloquent 모델 하나를 생성합니다. 일반적으로 모델은 `app`디렉토리에 존재하지만, `composer.json`파일에 의해서 오토로드 되는 곳이라면 어느곳에든 위치해도 상관없습니다. 모든 Eloquent 모델은 `Illuminate\Database\Eloquent\Model`을 상속받습니다.

#### Eloquent 모델 정의하기

	class User extends Model {}

`make:model` 명령어를 사용하여 Eloquent 모델을 생성할 수 있습니다:

	php artisan make:model User

생성한 `User` 모델은 어떠한 테이블을 사용할지 엘로퀀트에게 알려주지 않는 다는 점을 주의하십시오. 관련된 테이블이 별도로 지정되지 않는다면 클래스의 “스네이크 케이스” 로 표시된 복수 형태의 이름이 사용되어 집니다. 따라서 이 예제에서는 Eloquent는 `User` 모델은 `users`테이블에 레코드를 저장한다고 추정할 것입니다. 여러분은 모델의 `table`속성을 통해서 고유한 테이블을 지정할 수 있습니다:

	class User extends Model {

		protected $table = 'my_users';

	}

> **주의:** Eloquent는 테이블의 primary key 컬럼의 이름을 `id`로 추정합니다. `primaryKey` 속성을 통해서 이 컬럼명을 재지정할 수 있습니다. 마찬가지로 `connection` 속성을 통해서 모델에서 사용해야 하는 데이터베이스 커넥션을 지정할 수 있습니다.

모델이 정의되면, 테이블에서 레코드를 검색하거나 생성할 준비가 된 것입니다. 기본적으로 테이블에 `updated_at` 과 `created_at`컬럼을 필요로 한다는 점에 유의하십시오. 자동으로 이 컬럼값이 채워지기를 원하지 않는다면 `timestamps` 속성을 `false` 로 지정하십시오.

#### 모든 레코드 가져오기

	$users = User::all();

#### Primary Key를 통해서 하나의 레코드 가져오기

	$user = User::find(1);

	var_dump($user->name);

> **주의:** [쿼리 빌더](/docs/queries)에서 사용가능한 모든 메소드들은 Eloquent 모델에서 동일하게 사용할 수 있습니다.

#### Primary Key를 통해서 찾거나 Exception 던지기

때로는 모델을 찾지 못했을 때 Exception을 던지고 싶을 수도 있습니다. 이렇게 하려면 `firstOrFail` 메소드를 사용하면 됩니다.

	$model = User::findOrFail(1);

	$model = User::where('votes', '>', 100)->firstOrFail();

이렇게 하면 여러분이 별도로 exception을 처리할 수 있게 합니다. 따라서 로그를 남기거나, 필요한 경우 에러 페이지를 보여줄 수 있습니다. `ModelNotFoundException`을 처리하기 위해서는 `app/Exceptions/Handler.php`파일에 로직을 추가하면 됩니다.

	use Illuminate\Database\Eloquent\ModelNotFoundException;

	class Handler extends ExceptionHandler {

		public function render($request, Exception $e)
		{
			if ($e instanceof ModelNotFoundException)
			{
				// Custom logic for model not found...
			}

			return parent::render($request, $e);
		}

	}

#### Eloquent 모델에서 쿼리 사용하기

	$users = User::where('votes', '>', 100)->take(10)->get();

	foreach ($users as $user)
	{
		var_dump($user->name);
	}

#### Eloquent 합계

물론 쿼리 빌더 합계 함수도 사용할 수 있습니다.

	$count = User::where('votes', '>', 100)->count();

쿼리빌더 기능을 사용할 수 없다면 `whereRaw`를 사용할 수 있습니다.

	$users = User::whereRaw('age > ? and votes = 100', [25])->get();

#### 결과 분할

만약 처리해야할 Eloquent 레코드가 너무 많다면(몇천의), `chunk` 커맨드를 사용하여 램 사용량을 줄일 수 있습니다:

	User::chunk(200, function($users)
	{
		foreach ($users as $user)
		{
			//
		}
	});

메소드의 첫번째 인자는 “chunk” 메소드에서 받아 들일 레코드의 갯수 입니다. 두번째 인자는 클로저로 데이터베이스로 부터 분할된 데이터들을 전달 받습니다.

#### 쿼리 커넥션 지정하기

Eloquent 쿼리를 실행할 때 사용할 데이터베이스 커넥션을 지정할 수도 있습니다. 간단하게 `on` 메소드를 사용하면 됩니다.

	$user = User::on('connection-name')->find(1);

여러분이 [읽기 / 쓰기용 커넥션](/docs/{{version}}/database#read-write-connections)을 사용하고 있다면, 다음과 같은 메소들를 통해서 “쓰기용” 커넥션을 강제로 지정할 수 있습니다.

	$user = User::onWriteConnection()->find(1);

<!--chak-comment-Eloquent-ORM-기본-사용법-->

<a name="mass-assignment"></a>
## 대량 할당

새로운 모델을 생성할 때, 여러분은 모델의 생성자에 속성을 나타내는 배열을 전달하게 됩니다. 이 속성들은 모델의 대량-할당을 통해서 모델이 지정됩니다. 이러한 특징은 편리하긴 하지만 사용자의 입력값을 그대로 전달하게 되면 **심각한** 보안 위험을 초래할 수 있습니다. 사용자 입력을 바로 모델에 전달하게 된다면, 사용자는 모든 모델의 속성을 변경할 수 있게됩니다. 따라서 모든 Eloquent 모델은 기본적으로 대량-할당이 되지 않도록 보호되고 있습니다.

대량-할당을 사용하려면 `fillable` 또는 `guarded` 속성을 모델에 설정하십시오.

#### 모델에 할당할 수 있는 속성 정의하기

`fillable` 속성은 모델에서 할당 가능한 속성들을 지정합니다. 이 설정은 클래스나 인스턴스를 통해서 지정이 가능합니다.

	class User extends Model {

		protected $fillable = ['first_name', 'last_name', 'email'];

	}

이 예제에서는 단지 3개의 속성을 할당 가능한 형태로 지정하였습니다.

#### 모델에 보호해야할 속성 정의하기

`fillable` 과는 반대로 `guarded` 속성은 “화이트-리스트” 대신 “블랙-리스트”를 정의합니다.

	class User extends Model {

		protected $guarded = ['id', 'password'];

	}

> **주의** `guarded`를 사용하고 있을 때에는, 보호되지 않는 모든 컬럼이 업데이트 되기 때문에, `Input::get` 또는 사용자가 입력한 배열을 바로 전달하면 안됩니다.

#### 모든 속성을 대량 할당으로 부터 보호하기

위의 예제에서는 `id` 와 `password` 속성이 대량-할당 대상에서 제외 됩니다. 다른 속성들은 대량-할당이 가능합니다. 모든 속성을 대량-할당으로부터 보호하려면 guard 등록에 **모든** 속성을 지정하도록 할 수 있습니다.

	protected $guarded = ['*'];

<!--chak-comment-Eloquent-ORM-대량-할당-->

<a name="insert-update-delete"></a>
## 인서트, 업데이트, 삭제

모델에서 데이터베이스에 새로운 레코드를 만들려면 새로운 모델 인스턴스를 생성하고 `save` 메소드를 호출하면 됩니다.

#### 새로운 모델 저장하기

	$user = new User;

	$user->name = 'John';

	$user->save();

> **참고** 일반적으로 Eloquent 모델은 자동으로 auto-incrementing 키를 가집니다. 하지만 만약 고유한 key를 지정하고자 한다면 모델의 `incrementing` 속성을 `false`로 지정하십시오.

또한 `create` 메소드를 사용하여 한번에 새로운 모델을 생성할 수도 있습니다. 이 메소드는 생성된 모델의 인스턴스를 반환할 것입니다. 그러나 이렇게 하기 전에 모든 Eloquent 모델들이 대량-할당을 방지 할 수 있도록 모델의 `fillable` 또는 `guarded` 속성값을 지정해야 합니다.

auto-incrementing ID를 사용하는 새로운 모델을 저장하거나 생성한 다음에, 객체의 `id` 속성에 해당하는 ID를 조회할 수 있습니다.

	$insertedId = $user->id;

#### 모델에 보호되어야할 속성 설정하기

	class User extends Model {

		protected $guarded = ['id', 'account_id'];

	}

#### 모델의 생성 메소드 사용하기

	// Create a new user in the database...
	$user = User::create(['name' => 'John']);

	// Retrieve the user by the attributes, or create it if it doesn't exist...
	$user = User::firstOrCreate(['name' => 'John']);

	// Retrieve the user by the attributes, or instantiate a new instance...
	$user = User::firstOrNew(['name' => 'John']);

#### 조회한 모델 업데이트 하기

모델을 업데이트 하기 위해서는, 우선 해당 모델을 조회한다음, 속성값들을 변경하고 `save` 메소드를 사용하면 됩니다:

	$user = User::find(1);

	$user->email = 'john@foo.com';

	$user->save();

#### 모델과 관계된 모델들 저장하기

떄로는 해당 모델 뿐만 아니라 관계된 모든 모델 또한 저장해야 할 수도 있습니다. 이렇게 하려면, `push` 메서드를 사용하면 됩니다:

	$user->push();

또한 모델들에 대한 쿼리를 통해서 업데이트를 실행할 수도 있습니다.

	$affectedRows = User::where('votes', '>', 100)->update(['status' => 2]);

> **주의** Eloquent 쿼리 빌더를 사용하여 모델들에 대한 업데이트를 실행할 때에는 모델 이벤트가 발생하지 않습니다.

#### 기존 모델 삭제하기

모델을 삭제하기 위해서는  간단하게 모델 인스턴스에 대해서 `delete` 메소드를 호출하면 됩니다.

	$user = User::find(1);

	$user->delete();

#### 키를 통해서 모델 삭제하기

	User::destroy(1);

	User::destroy([1, 2, 3]);

	User::destroy(1, 2, 3);

또한 모델들에 대해서 삭제 쿼리 실행시킬 수도 있습니다.

	$affectedRows = User::where('votes', '>', 100)->delete();

#### 모델의 타임스탬프 값만 업데이트 하기

단순히 모델의 타임스탬프 값만을 업데이트 하려면 `touch` 메소드를 사용하면 됩니다:

	$user->touch();

<!--chak-comment-Eloquent-ORM-인서트--업데이트--삭제-->

<a name="soft-deleting"></a>
## 소프트 삭제

모델에서 소프트 삭제를 사용하는 경우, 데이터베이스에서 실제로 삭제되지 않습니다. 대신에, `delete_at` 타임스탬프 값이 설정됩니다. 모델에서 소프트 삭제를 사용하려면 모델에서 `SoftDeletes` trait을 사용하도록 설정하십시오.

	use Illuminate\Database\Eloquent\SoftDeletes;

	class User extends Model {

		use SoftDeletes;

		protected $dates = ['deleted_at'];

	}

테이블에 `delete_at` 컬럼을 추가하기 위해서는 마이그레이션에서 `softDeletes` 메소드를 사용하면 됩니다.

	$table->softDeletes();

이제 모델에서 `delete` 메소드를 호출하면, `delete_at` 컬럼이 현재의 타임스탬프 값으로 설정됩니다. 소프트 삭제를 사용하는 모델에 대해서 쿼리가 실행되면 “삭제된” 모델들은 쿼리 결과에 포함되지 않습니다.

#### 강제로 소프트 삭제된 모델들을 결과에 포함하기

강제로 소프트 삭제된 모델들을 결과에 나타나게 하기 위해서는, 쿼리에서 `withTrashed` 메소드를 사용하면 됩니다:

	$users = User::withTrashed()->where('account_id', 1)->get();

`withTrashed` 메소드는 정의된 관계 모델에서도 사용할 수 있습니다.

	$user->posts()->withTrashed()->get();

결과에서 **삭제된 모델들만** 확인하고자 한다면 `onlyTrashed` 메소드를 사용하면 됩니다:

	$users = User::onlyTrashed()->where('account_id', 1)->get();

소프트 삭제처리된 모델을 원래 상태로 복원하고자 한다면 `restore` 메소드를 사용하면 됩니다:

	$user->restore();

또한 쿼리에서 `restore` 메소드를 사용할 수도 있습니다.

	User::withTrashed()->where('account_id', 1)->restore();

`withTrashed` 와 같이, `restore` 메소드는 관계 질의에서도 사용할 수 있습니다:

	$user->posts()->restore();

데이터베이스에서 모델을 완전히 삭제하고자 한다면 `forceDelete` 메소드를 사용하면 됩니다:

	$user->forceDelete();

`forceDelete` 메소드는 관계 질의에서도 동작합니다:

	$user->posts()->forceDelete();

모델 인스턴스가 소프트 삭제된 상태인지 확인하고자 한다면 `trashed` 메소드를 사용하면 됩니다:

	if ($user->trashed())
	{
		//
	}

<!--chak-comment-Eloquent-ORM-소프트-삭제-->

<a name="timestamps"></a>
## 타임스탬프

기본적으로 Eloquent는 데이터베이스 테이블에 자동으로 `created_at` 과 `updated_at` 컬럼을 가지도록 되어 있습니다.

#### Disabling Auto Timestamps
#### 타임스탬프 자동으로 설정되지 않게 하기

	class User extends Model {

		protected $table = 'users';

		public $timestamps = false;

	}

#### 타임 스탬프의 사용자 정의 형식 지정

고유한 타임스탬프 형식을 지정하고자 한다면 모델의 `getDateFormat` 메소드를 재지정하면 됩니다.

	class User extends Model {

		protected function getDateFormat()
		{
			return 'U';
		}

	}

<!--chak-comment-Eloquent-ORM-타임스탬프-->

<a name="query-scopes"></a>
## 쿼리 스코프

#### 쿼리 스코프 정의하기

스코프는 여러분들이 모델안에서 쿼리 로직을 쉽게 재사용할 수 있도록 해줍니다. 스코프를 정의하기 위해서는 간단하게 메소드의 이름에 `scope` 를 접두어로 붙이면 됩니다.

	class User extends Model {

		public function scopePopular($query)
		{
			return $query->where('votes', '>', 100);
		}

		public function scopeWomen($query)
		{
			return $query->whereGender('W');
		}

	}

#### 쿼리 스코프 사용하기

	$users = User::popular()->women()->orderBy('created_at')->get();

#### 동적-다이나믹 스코프

때때로 파라미터를 전달받는 스코프를 정의하고자 할 수도 있습니다. 이 경우 간단하게 필요한 파라미터를 스코프 함수에 추가할 수 있습니다.

	class User extends Model {

		public function scopeOfType($query, $type)
		{
			return $query->whereType($type);
		}

	}

그 뒤에 스코프를 호출할때 필요한 파라미터를 전달하면 됩니다.

	$users = User::ofType('member')->get();

<!--chak-comment-Eloquent-ORM-쿼리-스코프-->

<a name="global-scopes"></a>
## 글로벌 스코프

때때로 모델에서 실행되는 모든 쿼리에 대해서 스코프를 정의하고자 할 수도 있을 것입니다. 실제로 이러한 방식은 Eloquent가 “소프트 삭제”를 구동시키는 방식입니다. 글로벌 스코프는 PHP의 trait과 `Illuminate\Database\Eloquent\ScopeInterface`의 구현 클래스를 결합하여 정의합니다.

먼저 trait을 정의합니다. 예를 들어 라라벨에 포함된 `SoftDeletes` 을 사용할 수 있습니다.

	trait SoftDeletes {

		/**
		 * Boot the soft deleting trait for a model.
		 *
		 * @return void
		 */
		public static function bootSoftDeletes()
		{
			static::addGlobalScope(new SoftDeletingScope);
		}

	}

만약 Eloquent 모델이 `bootNameOfTrait`으로 이름이 붙여진 메소드를 가지고 있는 Trait을 사용한다면, 글로벌 스코프를 등록하거나 혹은 여러분이 하고자 하는 다른 작업들을 할 수 있는 기회를 제공하기 위해서 Eloquent 모델이 부팅될 때 해당 trait 메소드가 호출됩니다. 스코프는 반드시 `apply` 와 `remove` 메소드를 가진 `ScopeInterface`를 구현해야 합니다.

`apply` 메소드는 `Illuminate\Database\Eloquent\Builder` 쿼리 빌더 객체와 적용할 `Model` 을 받아 스코프에 추가하고자 하는 `where`절을 추가하는 역활을 담당합니다. `remove` 메소드 또한 `Builder` 객체와 `Model`을 받고 `apply` 메소드가 수행한 액션을 되돌리는 역활을 담당합니다. 다시 말해, `remove` 는 추가된 `where` 구문(또는 다른 구문)을 제거 해야합니다. 따라서 우리가 고려하는 `SoftDeletingScope`는 다음처럼 구성할 수 있습니다.

	/**
	 * Apply the scope to a given Eloquent query builder.
	 *
	 * @param  \Illuminate\Database\Eloquent\Builder  $builder
	 * @param  \Illuminate\Database\Eloquent\Model  $model
	 * @return void
	 */
	public function apply(Builder $builder, Model $model)
	{
		$builder->whereNull($model->getQualifiedDeletedAtColumn());

		$this->extend($builder);
	}

	/**
	 * Remove the scope from the given Eloquent query builder.
	 *
	 * @param  \Illuminate\Database\Eloquent\Builder  $builder
	 * @param  \Illuminate\Database\Eloquent\Model  $model
	 * @return void
	 */
	public function remove(Builder $builder, Model $model)
	{
		$column = $model->getQualifiedDeletedAtColumn();

		$query = $builder->getQuery();

		foreach ((array) $query->wheres as $key => $where)
		{
			// If the where clause is a soft delete date constraint, we will remove it from
			// the query and reset the keys on the wheres. This allows this developer to
			// include deleted model in a relationship result set that is lazy loaded.
			if ($this->isSoftDeleteConstraint($where, $column))
			{
				unset($query->wheres[$key]);

				$query->wheres = array_values($query->wheres);
			}
		}
	}

<!--chak-comment-Eloquent-ORM-글로벌-스코프-->

<a name="relationships"></a>
## 관계

당연하게도 데이터베이스 테이블은 다른 테이블과 관계를 형성합니다. 예를 들어, 하나의 블로그 포스트는 다수의 코멘트를 가질 수 있고, 하나의 주문정보는 주문을 신청한 사용자와 관계되어 있습니다. Eloquent는 이러한 관계를 손쉽게 관리하고, 작동 할 수 있도록합니다. 라라벨은 다양한 종류의 관계형성을 제공합니다:

- [일대일](#one-to-one)
- [일대다](#one-to-many)
- [다대다](#many-to-many)
- [연결을 통한 다수를 가지는 관계](#has-many-through)
- [다형성 관계](#polymorphic-relations)
- [다대다 다형성 관계](#many-to-many-polymorphic-relations)

<a name="one-to-one"></a>
### 일 대 일 관계

#### 일대일 관계 정의하기

일대일 관계는 매우 기본적인 관계입니다. 예를 들어 `User` 모델은 하나의 `Phone` 을 가집니다. 이러한 관계를 Eloquent 에서 정의할 수 있습니다.

	class User extends Model {

		public function phone()
		{
			return $this->hasOne('App\Phone');
		}

	}

`hasOne` 메소드의 첫번째 인자는 관계된 모델의 이름입니다. 관계가 정의되고 나면 Eloquent의 [동적 속성](#dynamic-properties)을 통해서 조회할 수 있습니다:

	$phone = User::find(1)->phone;

위의 구문의 다음과 같은 SQL로 동작합니다.

	select * from users where id = 1

	select * from phones where user_id = 1

 Eloquent는 모델의 이름을 기반으로 관계된 외래 키를 추정한다는 것에 주의하십시오. 이 경우, `Phone` 모델은 `user_id` 외래 키를 사용 한다고 가정합니다. 만약 이러한 규칙을 재정의 하고자 한다면, `hasOne` 메소드의 두번째 인자로 외래 키로 사용하고자 하는 컬럼명을 전달하면 됩니다. 더불어, 세번째 인자로 어떠한 로컬 컬럼이 연결에 사용될지 명시 할 수도 있습니다:

	return $this->hasOne('App\Phone', 'foreign_key');

	return $this->hasOne('App\Phone', 'foreign_key', 'local_key');

#### 역관계 정의하기

`Phone` 모델에서 관계 설정의 반대, 즉 역관계를 정의하기 위해서는 `belongsTo` 메소드를 사용하면 됩니다.

	class Phone extends Model {

		public function user()
		{
			return $this->belongsTo('App\User');
		}

	}

위의 예제에서 Eloquent는 `phones` 테이블에서 `user_id` 컬럼을 찾을 것입니다. 만약 여러분이 따로 외래키를 정의하였다면 `belongsTo` 메소드에 두번째 인자로 이 키를 전달하면 됩니다.

	class Phone extends Model {

		public function user()
		{
			return $this->belongsTo('App\User', 'local_key');
		}

	}

추가적으로, 새번째 인자로 부모 테이블과 연결된 컬럼명을 전달 할 수도 있습니다:

	class Phone extends Model {

		public function user()
		{
			return $this->belongsTo('App\User', 'local_key', 'parent_key');
		}

	}

<a name="one-to-many"></a>
### 일대다 관계

일대다 관계의 예는 하나의 블로그 포스트가 “다수의” 코멘트를 가지는 것입니다. 다음처럼 모델을 설정할 수 있습니다.

	class Post extends Model {

		public function comments()
		{
			return $this->hasMany('App\Comment');
		}

	}

이제 [동적 속성](#dynamic-properties)을 통해서 포스트의 코멘트들을 엑세스 할 수 있습니다.

	$comments = Post::find(1)->comments;

만약 여러분이 어떤 코멘트들을 조회할 것인지에 대한 추가적인 제약이 필요하다면, `comments` 메소드와 연결된 체이닝 조건을 호출할 수도 있습니다.

	$comments = Post::find(1)->comments()->where('title', '=', 'foo')->first();

다시한번, `hasMany` 메서드에 두번째 인수를 전달하여, 외래 키 컬럼을 재정의 할 수 있습니다. 그리고, `hasOne` 관계와 같이, 로컬 컬럼 키 역시 명시 할 수 있습니다:

	return $this->hasMany('App\Comment', 'foreign_key');

	return $this->hasMany('App\Comment', 'foreign_key', 'local_key');

#### 역관계 정의하기

`Comment` 모델에 관계 설정의 반대, 즉 역관계를 설정하기 위해서는 `belongsTo` 메소드를 사용하면 됩니다.

	class Comment extends Model {

		public function post()
		{
			return $this->belongsTo('App\Post');
		}

	}

<a name="many-to-many"></a>
### 다대다 관계

다대다 관계는 좀더 복잡한 관계타입입니다. 이러한 예를 들자면 하나의 사용자는 다수의 역활을 가지고, 이 역활은 또한 각각의 사용자와 공유되는 경우입니다. 예를 들어 다수의 사용자가 “관리자” 역활을 가질 수 있습니다. 이 관계를 위해서는 3개의 데이터베이스 테이블이 필요합니다: `users`, `roles`, 그리고 `role_user`입니다. `role_user` 테이블은 관계가 설정된 모델의 이름을 알파벳 순서로 정렬한 순서에서 이름이 유래되었으며, `user_id`와 `role_id` 컬럼을 포함하고 있어야만 합니다.

`belongsToMany` 메소드를 사용하여 다음처럼 다대다 관계를 정의할 수 있습니다:

	class User extends Model {

		public function roles()
		{
			return $this->belongsToMany('App\Role');
		}

	}

이제 `User` 모델의 역활(roles)들을 조회할 수 있습니다.

	$roles = User::find(1)->roles;

만약 피벗 테이블 이름을 위에서 설명한 형식과 다르게 사용하려면 `belongsToMany` 메소드의 두 번째 인자로 전달하면 됩니다.

	return $this->belongsToMany('App\Role', 'user_roles');

또한 관련된 키 이름 역시 재정의할 수 있습니다 :

	return $this->belongsToMany('App\Role', 'user_roles', 'user_id', 'foo_id');

그리고 당연하게도 `Role` 모델에서 역관계를 정의할 수도 있습니다.

	class Role extends Model {

		public function users()
		{
			return $this->belongsToMany('App\User');
		}

	}

<a name="has-many-through"></a>
### 연결을 통한 다수를 가지는 관계

"연결을 통한 다수를 가지는" 관계는 중간 관계를 통해서 떨어진 관계에 엑세스 할 수 있는 편리한 방법을 제공합니다. 예를 들자면, 하나의 `Country` 모델은 `Users` 모델을 통해서 다수의 `Posts` 를 가지고 있을 수 있습니다. 테이블은 다음과 같이 구성될 것입니다.

	countries
		id - integer
		name - string

	users
		id - integer
		country_id - integer
		name - string

	posts
		id - integer
		user_id - integer
		title - string

`posts` 테이블은 `country_id` 컬럼을 가지고 있지 않지만, `hasManyThrough` 관계를 사용하면 `$country->posts` 를 통해서 해당 country의 posts에 엑세스 할 수 있습니다.  관계를 정의하는 것을 살펴보겠습니다.

	class Country extends Model {

		public function posts()
		{
			return $this->hasManyThrough('App\Post', 'App\User');
		}

	}

만약 수동으로 관계에 필요한 키를 지정하고자 한다면 메소드의 세번째와 네번째 인자로 전달하면 됩니다.

	class Country extends Model {

		public function posts()
		{
			return $this->hasManyThrough('App\Post', 'App\User', 'country_id', 'user_id');
		}

	}

<a name="polymorphic-relations"></a>
### 다형성 관계

다형성 관계는 모델을 하나의 관계뿐만 여러 모델에 속할 수 있도록 합니다. 예를 들어 사진 모델이 직원 모델이나 주문 모델 모두에 소속되어 있을 수 있습니다. 이러한 관계는 다음과 같이 정의 할 수 있습니다:

	class Photo extends Model {

		public function imageable()
		{
			return $this->morphTo();
		}

	}

	class Staff extends Model {

		public function photos()
		{
			return $this->morphMany('App\Photo', 'imageable');
		}

	}

	class Order extends Model {

		public function photos()
		{
			return $this->morphMany('App\Photo', 'imageable');
		}

	}

#### 하나의 다형성 관계 조회하기

이제 직원 맴버 또는 주문을 통해서 사진을 조회 할 수 있습니다:

	$staff = Staff::find(1);

	foreach ($staff->photos as $photo)
	{
		//
	}

#### 다형성 관계의 소유자 조회하기

그러나, 실제로 “다형성”의 마법은 `Photo` 모델에서 직원 또는 주문을 엑세스할 때 나타납니다.

	$photo = Photo::find(1);

	$imageable = $photo->imageable;

`Photo` 모델의 `imageable` 관계는 사진을 소유하고 있는 모델의 유형에 따라서 `Staff` 또는 `Order` 인스턴스 중 하나를 반환합니다.

#### 다형성 관계의 테이블 구조

이 관계에 대한 이해를 돕기 위해서 다형성 관계에서의 데이터베이스 구조를 살펴보겠습니다:

	staff
		id - integer
		name - string

	orders
		id - integer
		price - integer

	photos
		id - integer
		path - string
		imageable_id - integer
		imageable_type - string

주목해야할 핵심적인 필드는 `photos` 테이블의 `imageable_id` 와 `imageable_type` 입니다.  이 예제에서 ID는 직원 또는 주문 의 ID 를 나타내고, type은 소유하고 있는 모델 클래스의 이름을 나타냅니다. 이것은 `imageable` 관계를 엑세스 할 때 ORM 이 어떤 타입의 소유 모델을 반환해야 하는지 결정해줍니다.

<a name="many-to-many-polymorphic-relations"></a>
### 다대다 다형성 관계

#### 다대다 다형성 관게 테이블 구조

추가적으로 일반적인 다형성 관계뿐만 아니라, 다대다 다형성 관계도 지정할 수 있습니다. 예를 들어 하나의 블로그 `Post`와 `Video` 모델은 하나의 `Tag` 모델에 다형성 관계를 공유할 수 있습니다. 먼저 테이블 구조를 살펴보겠습니다:

	posts
		id - integer
		name - string

	videos
		id - integer
		name - string

	tags
		id - integer
		name - string

	taggables
		tag_id - integer
		taggable_id - integer
		taggable_type - string

이제, 모델에 관계를 설정할 준비가 되었습니다. `Post` 와 `Video` 모델은 둘다 `tags` 메소드를 통해서 `morphToMany` 관계를 가집니다:

	class Post extends Model {

		public function tags()
		{
			return $this->morphToMany('App\Tag', 'taggable');
		}

	}

`Tag` 모델에서는 각각의 관계에 대한 메소드를 정의할 수 있습니다:

	class Tag extends Model {

		public function posts()
		{
			return $this->morphedByMany('App\Post', 'taggable');
		}

		public function videos()
		{
			return $this->morphedByMany('App\Video', 'taggable');
		}

	}

<!--chak-comment-Eloquent-ORM-관계-->

<a name="querying-relations"></a>
## 관계 쿼리

#### SELECT 경우 관계 쿼리

모델의 레코드에 엑세스할 때, 여러분은 여러분의 결과를 관계의 존재에 따라서 제한하기를 원할 수도 있습니다. 예를 들어 최소한 하나의 코멘트를 가지고 있는 모든 블로그 포스트들을 조회하고자 할 수 있습니다. 이런 경우 `has` 메소드를 사용하면 됩니다.

	$posts = Post::has('comments')->get();

또한 메소드에서 사용할 수 있는 연산자와 카운트 갯수를 지정할 수도 있습니다:

	$posts = Post::has('comments', '>=', 3)->get();

중첩된 `has` 문법은 “점” 표기를 통해서 구성할 수 있습니다.

	$posts = Post::has('comments.votes')->get();

더 강력한 기능을 원한다면 `has` 쿼리에 "where"조건을 더하기 위해서 `whereHas` 과 `orWhereHas`을 이용할 수 있습니다.

	$posts = Post::whereHas('comments', function($q)
	{
		$q->where('content', 'like', 'foo%');

	})->get();

<a name="dynamic-properties"></a>
### 동적 속성

Eloquent는 동적 속성을 사용하여 지정된 관계들에 엑세스하는 방법을 제공합니다. Eloquent는 자동으로 지정된 관계를 로드하고, 똑똑하게도 `get` (일대다 관계의 경우) 또는 `first` (일대일 관계의 경우) 중 어떤 메소드를 호출해야 할지 구분합니다. 그다음에 관계와 동일한 이름의 동적 속성을 통해 엑세스 할 수 있습니다. 예를 들어, 다음의 `$phone` 모델을 확인해 봅시다:

	class Phone extends Model {

		public function user()
		{
			return $this->belongsTo('App\User');
		}

	}

	$phone = Phone::find(1);

사용자의 이메일을 다음처럼 표시하기 보다:

	echo $phone->user()->first()->email;

보다 짧고 간단하게 표시할 수 있습니다:

	echo $phone->user->email;

> **참고:** 여러 결과를 반환하는 관계는 `Illuminate\Database\Eloquent\Collection` 클래스의 인스턴스를 반환할 것입니다.

<!--chak-comment-Eloquent-ORM-관계-쿼리-->

<a name="eager-loading"></a>
## Eager 로딩

Eager 로딩은 N + 1 쿼리 문제를 위해서 존재합니다. 예를 들어 `Book` 모델과 관계된 `Author` 모델이 있다고 가정합니다. 관계에 대한 정의는 다음과 같을 것입니다.

	class Book extends Model {

		public function author()
		{
			return $this->belongsTo('App\Author');
		}

	}

이제 다음 코드를 살펴보겠습니다:

	foreach (Book::all() as $book)
	{
		echo $book->author->name;
	}

이 반복문은 테이블에서 모든 책들을 조회하는 1 개의 쿼리를 실행하고, 각각의 책마다 저자를 조회하는 별개의 쿼리를 실행할 것입니다. 따라서, 만약 25개의 책이 있다면, 이 반복문은 26개의 쿼리를 실행하게 됩니다.

다행스럽게도, 우리는 쿼리 수를 현저하게 줄일 수 있는 eager 로딩을 사용할 수 있습니다. `with`를 통해서 eager 로딩을 사용해야 하는 관계를 지정할 수 있습니다:

	foreach (Book::with('author')->get() as $book)
	{
		echo $book->author->name;
	}

위의 루프는 2 개의 쿼리만 실행될것입니다:

	select * from books

	select * from authors where id in (1, 2, 3, 4, 5, ...)

Eager 로딩의 적절한 사용은 여러분의 어플리케이션의 성능을 크게 향상 시킬 수 있습니다.

당연하게도 한번에 여러개의 관계를 Eager 로딩할 수도 있습니다:

	$books = Book::with('author', 'publisher')->get();

중첩된 관계에 대해서도 eager 로딩할 수 있습니다:

	$books = Book::with('author.contacts')->get();

이 예제에서 `author` 관계는 eager 로딩될 것이고, 저자의 `contacts` 관계도 로딩됩니다.

### Eager 로드의 제약조건

때때로, 관계를 Eager 로딩 할 때 조건을 지정하고 싶은 경우도 있습니다. 다음의 예를 보겠습니다:

	$users = User::with(['posts' => function($query)
	{
		$query->where('title', 'like', '%first%');

	}])->get();

이 예제에서 사용자의 포스트들을 eager 로딩하게 되지만, 포스트의 제목이 "first"라는 단어로 구성되어있는 것만이 가져오게 됩니다.

물론, Eager 로딩 클로저는 “제약 조건“에만 한정되어 있지 않습니다. 여러분은 또한 정렬 순서를 지정할 수도 있습니다:

	$users = User::with(['posts' => function($query)
	{
		$query->orderBy('created_at', 'desc');

	}])->get();

### 지연된 Eager 로딩

이미 존재하는 모델 컬렉션에서 직접 연관된 모델들을 Eager 로딩하는 것 또한 가능합니다. 이것은 관련된 모델을 로드할지 말지 여부를 동적으로 결정하거나, 캐시와 연동할 때 유용합니다.

	$books = Book::all();

	$books->load('author', 'publisher');

여러분은 또한 쿼리에 제약 조건을 설정하는 클로저를 전달 할 수도 있습니다:

	$books->load(['author' => function($query)
	{
		$query->orderBy('published_date', 'asc');
	}]);

<!--chak-comment-Eloquent-ORM-Eager-로딩-->

<a name="inserting-related-models"></a>
## 관련된 모델 삽입하기

#### 관계된 모델 추가하기

여러분은 종종 새로운 관계 모델을 삽입해야할 필요가 있을 수 있습니다. 예를 들어, 여러분은 포스트와 연결된 새로운 코멘트를 추가하려고 할 수 있습니다. 수동으로 모델에 `post_id` 외래키를 설정하는 대신에, 새로운 코멘트를 `Post` 모델을 통해서 바로 추가할 수 있습니다.

	$comment = new Comment(['message' => 'A new comment.']);

	$post = Post::find(1);

	$comment = $post->comments()->save($comment);

이 예제에서, `post_id` 필드는 삽입된 코멘트 레코드에 자동으로 설정됩니다.

만약 여러개의 관련된 모델을 저장해야 한다면 다음과 같이 할 수 있습니다:

	$comments = [
		new Comment(['message' => 'A new comment.']),
		new Comment(['message' => 'Another comment.']),
		new Comment(['message' => 'The latest comment.'])
	];

	$post = Post::find(1);

	$post->comments()->saveMany($comments);

### 연관 모델들 (Belongs To)

`belongsTo` 관계를 업데이트 할때, 여러분은 `associate` 메소드를 사용할 수 있습니다. 이 메소드는 자식 모델에 외래키를 설정할 것입니다.

	$account = Account::find(10);

	$user->account()->associate($account);

	$user->save();

### 관계된 모델 삽입(다대다)

여러분은 또한 다대대 관계를 작업해야 하는 경우에도 관계된 모델을 삽입할 수 있습니다. 예제처럼 `User` 와 `Role` 모델을 예제로 사용하겠습니다. `attach` 메소드를 사용하여 새로운 역활을 사용자에게 추가할 수 있습니다.

#### 다대다 모델 추가하기

	$user = User::find(1);

	$user->roles()->attach(1);

여러분은 또한 해당 관계에 대한 피벗 테이블에 저장되는 속성을 배열로 전달 할 수도 있습니다:

	$user->roles()->attach(1, ['expires' => $expires]);

물론 `attach`의 반대는 `detach` 입니다.

	$user->roles()->detach(1);

`attach`와 `detach` 두가지 모두 ID들의 배열을 받을 수도 있습니다:

	$user = User::find(1);

	$user->roles()->detach([1, 2, 3]);

	$user->roles()->attach([1 => ['attribute1' => 'value1'], 2, 3]);

#### 다대다 모델들을 추가하기 위해서 Sync 사용하기

여러분은 또한 관계된 모델들을 추가하기 위해서 `sync` 메소드를 사용할 수 있습니다. `sync` 메소드는 피벗 테이블에 배치할 ID들의 배열을 받습니다. 이 연산이 완료된 후, 배열 안의 ID들만 해당 모델의 피벗 테이블에 있게 될것입니다.

	$user->roles()->sync([1, 2, 3]);

#### 동기화중에 피벗 데이타 추가하기

여러분은 또한 주어진 ID들의 피벗 테이블 값들를 연결 할 수도 있습니다:

	$user->roles()->sync([1 => ['expires' => true]]);

때떄로 여러분은 하나의 명령어로 관련 모델을 생성하고 추가하기를 원할 수도 있습니다. 이 작업을 위해서는 `save` 메소드를 사용할 수 있습니다:

	$role = new Role(['name' => 'Editor']);

	User::find(1)->roles()->save($role);

이 예제에서 새로운 `Role` 모델은 데이터베이스에 저장되고 사용자 모델에 추가될 것입니다. 또한 이 작업에 영향을 받은 테이블에 저장될 속성 배열을 전달할 수도 있습니다.

	User::find(1)->roles()->save($role, ['expires' => $expires]);

<!--chak-comment-Eloquent-ORM-관련된-모델-삽입하기-->

<a name="touching-parent-timestamps"></a>
## 부모의 타임스탬프 값 갱신

하나의 `Post` 에 소속되는 하나의 `Comment`와 같이, 특정 모델이 다른 모델에 `소속될(belongsTo)`때와 같이, 가끔 자식 모델이 업데이트 될 때 부모의 타임스탬프를 갱신하는 것이 유용할 때가 있습니다. 예를 들어 `Comment` 모델이 업데이트 될 때, 자동으로 부모 `Post` 의 `updated_at` 타임스탬프가 갱신되기를 바랄 수도 있습니다. Eloquent는 손쉽게 이를 가능하게 합니다. 단지 자식 모델에 관련 모델명을 포함하는 `touches` 속성을 추가하면 됩니다:

	class Comment extends Model {

		protected $touches = ['post'];

		public function post()
		{
			return $this->belongsTo('App\Post');
		}

	}

이제 `Comment` 가 업데이트 될때, 이를 소유한 `Post`의 `updated_at` 컬럼이 갱신 될것입니다.

	$comment = Comment::find(1);

	$comment->text = 'Edit to this comment!';

	$comment->save();

<!--chak-comment-Eloquent-ORM-부모의-타임스탬프-값-갱신-->

<a name="working-with-pivot-tables"></a>
## 피벗 테이블 작업

앞서 배운대로, 다대다 관계를 위해서는 중간 테이블이 필요합니다. Eloquent은 이 테이블을 조작하는 유용한 방법을 제공하고 있습니다. 예를 들어 `User` 객체가 다수의 `Role`객체를 가지고 있는 관계를 생각해 봅시다. 이 관계에 엑세스 한 뒤에 모델에 대한 `pivot` 테이블에 엑세스 할 수 있습니다.

	$user = User::find(1);

	foreach ($user->roles as $role)
	{
		echo $role->pivot->created_at;
	}

조회한 `Role` 모델에 자동으로 `pivot` 속성이 부여되어 있다는 점을 기억하십시오. 이 속성은 중간 테이블을 나타내는 모델에서 다른 Eloquent모델과 마찬가지로 사용할 수 있습니다.

기본적으로 `pivot`객체는 단순히 키만 가지고 있습니다. 만약 피벗 테이블이 다른 추가적인 특성을 포함하려면 관계를 정의할 때 지정해주어야 합니다.

	return $this->belongsToMany('App\Role')->withPivot('foo', 'bar');

이제 `Role` 모델의 `pivot` 객체에서 `foo`와 `bar` 속성에 엑세스 할 수 있을 것입니다.

만약 피벗 테이블에서 자동으로 `created_at` 과 `updated_at` 타임 스탬프를 갱신하길 원한다면 관계 정의에서 `withTimestamps` 메소드를 사용하면 됩니다.

	return $this->belongsToMany('App\Role')->withTimestamps();

#### 피벗 테이블의 레코드 삭제하기

하나의 모델에 대한 피벗 테이블의 모든 레코드를 삭제하려면 `detach` 메소드를 사용하면 됩니다.

	User::find(1)->roles()->detach();

이 작업은 `roles` 테이블에서는 레코드를 삭제하지 않고 피벗 테이블에 대해서만 동작한다는 점에 주의 하십시오.

#### 피벗 테이블의 레코드 업데이트 하기

때로는 피벗 테이블을 삭제하지 않고, 업데이트가 필요할 수도 있습니다. 피벗 테이블을 업데이트 하고자 한다면 다음처럼 `updateExistingPivot` 메소드를 사용하면 됩니다:

	User::find(1)->roles()->updateExistingPivot($roleId, $attributes);

#### 사용자 정의 피벗 모델 정의하기

	public function newPivot(Model $parent, array $attributes, $table, $exists)
	{
		return new YourCustomPivot($parent, $attributes, $table, $exists);
	}

<!--chak-comment-Eloquent-ORM-피벗-테이블-작업-->

<a name="collections"></a>
## 컬렉션

`get` 메소드를 통해서 또는 하나의 `relationship-관계`를 통해서 Eloquent로 부터 반환되는 모든 멀티 레코드 결과가 반환되는 경우에는 하나의 컬렉션 객체가 반환됩니다. 이 객체는 `IteratorAggregate` PHP 인터페이스의 구현체이므로 배열과 같이 반복될수 있습니다. 그리고 이 객체는 결과를 조작할 수 있는 다양한 메소드들 또한 가지고 있습니다.

#### 컬렉션이 키를 가지고 있는지 확인하기

예를 들어 `contains` 메소드를 사용하여 지정된 primary 키가 결과에 포함되어 있는지 확인 할 수 있습니다.

	$roles = User::find(1)->roles;

	if ($roles->contains(2))
	{
		//
	}

컬렉션은 배열이나 JSON으로 변환 될 수 있습니다:

	$roles = User::find(1)->roles->toArray();

	$roles = User::find(1)->roles->toJson();

컬렉션을 문자열로 변환하면 JSON이 반환됩니다:

	$roles = (string) User::find(1)->roles;

#### 컬렉션의 반복

Eloquent 컬렉션에 포함 된 항목을 반복하거나 필터링 할 수 있는 유용한 메소드들을 가지고 있습니다:

	$roles = $user->roles->each(function($role)
	{
		//
	});

#### 컬렉션의 필터링

컬렉션을 필터링하는 경우, 지정된 콜백은 [array_filter](http://php.net/manual/en/function.array-filter.php) 콜백으로 이용됩니다.

	$users = $users->filter(function($user)
	{
		return $user->isAdmin();
	});

> **주의:** 컬렉션을 필터링하거나 JSON으로 변환하는 경우, `values` ​​메소드를 먼저 호출하여 배열의 키를 초기화 하십시오.

#### 컬렉션의 각 개체에 콜백을 적용하기

	$roles = User::find(1)->roles;

	$roles->each(function($role)
	{
		//
	});

#### 값을 기준으로 컬렉션 정렬하기

	$roles = $roles->sortBy(function($role)
	{
		return $role->created_at;
	});

	$roles = $roles->sortByDesc(function($role)
	{
		return $role->created_at;
	});

#### 값을 기준으로 컬렉션 정렬하기

	$roles = $roles->sortBy('created_at');

	$roles = $roles->sortByDesc('created_at');

#### 사용자 정의 컬렉션 타입 반환하기

때로는, 여러분이 추가한 메소드와 함께 사용자 정의 컬렉션 객체를 반환하기를 원할 수도 있습니다. 이때는 `newCollection` 메소드를 재정의하여 이를 지정할 수 있습니다.

	class User extends Model {

		public function newCollection(array $models = [])
		{
			return new CustomCollection($models);
		}

	}

<!--chak-comment-Eloquent-ORM-컬렉션-->

<a name="accessors-and-mutators"></a>
## Accessors & Mutators

#### Accessor 정의하기

Eloquent는 사용자가 getting 또는 setting을 제공할 경우, 사용자 정의 모델의 속성(attributes)을 쉽게 바꿀 수 있는, accessor라는 편리한 방법을 제공합니다. `getFooAttribute` 메소드를 선언하는 사용자 정의 모델에 accessor를 쉽게 정의합니다. accessor를 정의할 때, 사용자의 데이터베이스 컬럼이 snake-case를 사용하더라도 camel-case를 꼭 사용해야 합니다.:

	class User extends Model {

		public function getFirstNameAttribute($value)
		{
			return ucfirst($value);
		}

	}

위의 예제에서 `first_name` 컬럼에 accessor가 있고, accessor로 속성(attribute) 값이 보내진다는 것에 유의해야 합니다.

#### Mutator 정의하기

Mutators는 Accessor와 비슷한 방법으로 선업됩니다.:

	class User extends Model {

		public function setFirstNameAttribute($value)
		{
			$this->attributes['first_name'] = strtolower($value);
		}

	}

<!--chak-comment-Eloquent-ORM-Accessors---Mutators-->

<a name="date-mutators"></a>
## 날짜 Mutators

기본적으로 Eloquent는 `created_at` 컬럼과 `updated_at` 컬럼을 [Carbon](https://github.com/briannesbitt/Carbon) 클래스의 인스턴스로(네이티브 PHP의 `DateTime` 클래스의 확장형이며, 여러 메소드를 제공하는) 변환해줍니다.  

사용자 정의 모델의 `getDates` 메소드를 재정의 하여 필드를 자동으로 바뀌도록 활성화 또는 비활성화 할 수 있습니다.

	public function getDates()
	{
		return ['created_at'];
	}

사용자는 UNIX 타임스탬프 값, date(`Y-m-d`) 문자열 값, 날짜-시간에 대한 문자열 값, 그리고 `DateTime` / `Carbon` 클래스의 인스턴스 값들을 설정할 수 있습니다.

날짜 변환을 아예 비활성화 하려면, `getDates` 메소드에서 빈 배열을 리턴하면 됩니다.:

	public function getDates()
	{
		return [];
	}

<!--chak-comment-Eloquent-ORM-Date-Mutators-->

<a name="attribute-casting"></a>
## 속성(Attribute) 캐스팅

사용자가 항상 다른 데이터 유형으로 변환 할 속성들을 가지고 있는 경우, 사용자 정의 모델에 `casts` 값을 추가 할 수 있습니다. 그렇지 않으면, 속성들의 값들을 직접 바꿔줘야 되는데, 이는 시간 낭비입니다. 아래는 `casts` 값의 사용 예 입니다.:

	/**
	 * 해당 속성 값들은 기본 타입으로 캐스팅 해야합니다.
	 *
	 * @var array
	 */
	protected $casts = [
		'is_admin' => 'boolean',
	];

이제 사용자가 액세스 할 때 기본 값이 정수로 데이터베이스에 저장되어있는 경우에도 `is_admin` 속성은 항상 boolean으로 캐스팅됩니다. 캐스팅을 지원하는 유형들은 다음과 같습니다 : `integer`, `real`, `float`, `double`, `string`, `boolean`, `object`, `array`.

`array` 캐스트는 직렬화 된 JSON으로 컬럼에 저장하는 작업에 특히 유용합니다. 예를 들어, 데이터베이스에 직렬화 된 JSON을 포함하는 텍스트 형식 필드가있는 경우, `array` 캐스팅을 해당 속성에 추가하면 Eloquent 사용자 정의 모델에 접근할 때 자동으로 역 직렬화 된 PHP 배열 값이 해당 속성 값으로 들어갑니다.:

	/**
	 * 해당 속성 값들은 기본 타입으로 캐스팅 해야합니다.
	 *
	 * @var array
	 */
	protected $casts = [
		'options' => 'array',
	];

사용자가 Eloquent 모델을 사용하는 경우:

	$user = User::find(1);

	// $options은 배열 입니다...
	$options = $user->options;

	// options은 자동으로 JSON으로 직렬화 됩니다...
	$user->options = ['foo' => 'bar'];

<!--chak-comment-Eloquent-ORM-Attribute-Casting-->

<a name="model-events"></a>
## 모델 이벤트

Eloquent 모델들은 라이프 사이클의 여러 지점을 후킹(hook) 할 수 있도록 다음과 같은 이벤트가 발생합니다.: `creating`, `created`, `updating`, `updated`, `saving`, `saved`, `deleting`, `deleted`, `restoring`, `restored`.

새로운 아이템을 처음 저장 할 경우,`created`, `creating` 이벤트가 발생합니다. 만약 항목이 새로운 아이템이 아니거나, `save` 메서드를 호출 한 경우, `updating`, `updated` 이벤트가 발생합니다. 두 경우 모두 `saving`, `saved` 이벤트가 발생합니다.

#### 이벤트를 통해서 저장 취소하기

`creating`, `updating`, `saving`, `deleting` 이벤트에서 `false`가 리턴되는 경우, 작업이 취소됩니다.:

	User::creating(function($user)
	{
		if ( ! $user->isValid()) return false;
	});

#### 이벤트 리스너를 등록하는 경우

사용자의`EventServiceProvider`는 모델 이벤트 바인딩을 등록 할 수있는 편리한 장소를 제공합니다. 사용 예제:

	/**
	 * 프로그램에 다른 이벤트를 등록합니다.
	 *
	 * @param  \Illuminate\Contracts\Events\Dispatcher $events
	 * @return void
	 */
	public function boot(DispatcherContract $events)
	{
		parent::boot($events);

		User::creating(function($user)
		{
			//
		});
	}

<!--chak-comment-Eloquent-ORM-Model-Events-->

<a name="model-observers"></a>
## 모델 관찰자(Observers)

모델 이벤트의 처리를 통합하기 위해, 여러분은 모델 관찰자를 등록 할 수 있습니다. 관찰자 클래스는 다양한 모델 이벤트에 대응하는 메소드를 가질 수 있습니다. 예를 들어,`creating`, `updating`, `saving` 메소드가 관찰자에 있을 수 있고, 다른 어떤 모델 이벤트 이름의 형태로도 있을 수 있습니다. 

따라서, 예를 들어, 모델 관찰자는 다음과 같을 수 있습니다.:

	class UserObserver {

		public function saving($model)
		{
			//
		}

		public function saved($model)
		{
			//
		}

	}

사용자는 `observe` 메서드를 사용하여 관찰자 인스턴스를 등록 할 수 있습니다.:

	User::observe(new UserObserver);

<!--chak-comment-Eloquent-ORM-Model-Observers-->

<a name="model-url-generation"></a>
## 모델 URL 생성하기

사용자가 `route` 또는 `action` 메소드에 대한 모델을 통화시킬 때, 기본키가 생성된 URI를 삽입합니다. 예제 입니다.:

	Route::get('user/{user}', 'UserController@show');

	action('UserController@show', [$user]);

위의 예제에서 `$user->id` 의 값은 생성된 URL의 `{user}` 위치에 삽입됩니다. 만약 사용자의 ID 대신 다른 속성을 사용하고자 하는 경우에는, 사용자 정의 모델의 `getRouteKey` 메소드를 재정의 하면 됩니다.:

	public function getRouteKey()
	{
		return $this->slug;
	}

<!--chak-comment-Eloquent-ORM-Model-URL-Generation-->

<a name="converting-to-arrays-or-json"></a>
## 배열 / JSON으로 변환하기

#### 모델을 배열로 변환하기

JSON API를 구축 할 때, 사용자는 종종 배열이나 JSON으로 사용자 정의 모델의 관계를 바꿔야 할 때가 있습니다. 그래서 Eloquent는 모델과 불러온 배열의 관계를 변환할 때 `toArray` 메소드를 사용합니다.:

	$user = User::with('roles')->first();

	return $user->toArray();

참고. 모델의 전체 컬렉션을 배열로 바꿀 수 있습니다.:

	return User::all()->toArray();

#### 모델을 JSON으로 변환하기

JSON으로 모델을 전환할 경우, `toJson` 메소드를 사용할 수 있습니다.

	return User::find(1)->toJson();

#### 라우트에서 모델 반환하기

참고로 모델 또는 컬렉션이 문자열로 캐스팅 될 때, 그것은 JSON 형태로 변환됩니다. 이것은 Eloquent가 프로그램의 라우트에 직접 객체를 반환할 수 있음을 의미합니다.

	Route::get('users', function()
	{
		return User::all();
	});

#### 배열 또는 JSON 변환에서의 속성값 숨기기

사용자는 비밀번호와 같은 모델의 배열 또는 JSON 형식에 포함 된 속성을 제한 할 수도 있습니다. 이렇게 하기 위해서는, 사용자 정의 모델에 `hidden` 값을 추가하면 됩니다.:

	class User extends Model {

		protected $hidden = ['password'];

	}

> **주의:** 관계를 숨기고자 할 때에는, 동적인 accessor 이름이 아니라, 관계에 대한 **메소드** 이름을 사용하십시오.

또는, 화이트-리스트를 정의해서 `visible` 값을 사용할 수 있습니다.

	protected $visible = ['first_name', 'last_name'];

<a name="array-appends"></a>
때때로, 사용자는 데이터베이스에서 해당 컬럼이 없는 배열 속성을 추가 할 필요가 있습니다. 이렇게 하려면 단순히 값에 대한 accessor를 정의하면 됩니다.:

	public function getIsAdminAttribute()
	{
		return $this->attributes['admin'] == 'yes';
	}

사용자가 accessor를 만든 후, 즉시 사용자 정의 모델의 `appends`에 값을 추가합니다.:

	protected $appends = ['is_admin'];

속성이 `appends` 리스트에 추가되고나면 모델이 배열이나 JSON 형태로 변환될 때 자동으로 포함되어집니다. `appends` 배열의 속성은 모델에 정의된 `visible`와 `hidden`값에 영향을 받습니다.

<!--chak-comment-Eloquent-ORM-Converting-To-Arrays---JSON-->
