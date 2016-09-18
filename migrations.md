# 마이그레이션 & 시딩(Migrations & Seeding)

- [소개](#introduction)
- [마이그레이션 생성하기](#creating-migrations)
- [마이그레이션 실행하기](#running-migrations)
- [마이그레이션 롤백하기](#rolling-back-migrations)
- [데이터베이스 시딩(초기값 입력하기)](#database-seeding)

<a name="introduction"></a>
## 소개

마이그레이션은 데이터베이스용 버전 컨트롤러의 일종입니다. 마이그레이션은 여러분의 팀이 데이터베이스 스키마를 수정하고 현재의 스키마 상태를 유지할 수 있도록 해줍니다. 마이그레이션은 일반적으로 어플리케이션의 스키마를 쉽게 관리할 수 있는 [스키마 빌더](/docs/5.0/schema)와 함께 사용됩니다.

<!--chak-comment-마이그레이션---시딩(Migrations---Seeding)-소개-->

<a name="creating-migrations"></a>
## 마이그레이션 생성하기

마이그레이션을 생성하기 위해서는 `make:migration`아티즌 CLI 명령어를 사용합니다:

	php artisan make:migration create_users_table

마이그레이션 파일들은 `database/migrations` 폴더에 저장되고 프레임워크가 마이그레이션의 순서를 알 수 있도록 타임스탬프를 포함하고 있습니다.

`--table`과 `--create`옵션은 테이블의 이름과 마이그레이션 하면서 새 테이블을 만들 것인지를 결정하는 데 사용할 수 있습니다.

	php artisan make:migration add_votes_to_users_table --table=users

	php artisan make:migration create_users_table --create=users

<!--chak-comment-마이그레이션---시딩(Migrations---Seeding)-마이그레이션-생성하기-->

<a name="running-migrations"></a>
## 마이그레이션 실행하기

#### 실행되지 않은 전체 마이그레이션 실행

	php artisan migrate

> **참고 :** 마이그레이션 실행 시 "class not found" 에러 메세지가 출력된다면 `composer dump-autoload` 명령어를 실행해보십시오.

### 실제 제품 서버에서 강제로 마이그레이션 실행하기

일부의 마이그레이션 작업은 구조 변경이 있을 수 있으며 이로 인해 데이터를 잃을 수도 있습니다. 이러한 위험들로부터 실제 제품 서버의 데이터베이스를 보호하기 위해 마이그레이션 명령어를 실행하기 전에 확인 메시지가 출력됩니다. 확인 메시지 없이 명령어을 실행시키기 위해서는 `--force` 플래그를 사용하면 됩니다:

	php artisan migrate --force

<!--chak-comment-마이그레이션---시딩(Migrations---Seeding)-마이그레이션-실행하기-->

<a name="rolling-back-migrations"></a>
## 마이그레이션 롤백하기

#### 가장 최근의 마이그레이션 작업 롤백하기

	php artisan migrate:rollback

#### 모든 마이그레이션 작업 롤백하기

	php artisan migrate:reset

#### 모든 마이그레이션 작업을 롤백하고 다시 실행하기

	php artisan migrate:refresh

	php artisan migrate:refresh --seed

<!--chak-comment-마이그레이션---시딩(Migrations---Seeding)-마이그레이션-롤백하기-->

<a name="database-seeding"></a>
## 데이터베이스 시딩

라라벨에서는 시드(seed) 클래스를 사용해서 테스트 데이터를 데이터베이스에 설정하는 간단한 방법을 제공합니다. 모든 시드 클래스는 `database/seeds` 폴더에 저장됩니다. 시드 클래스의 이름은 원하는 대로 작성할 수 있지만 될 수 있으면 `UserTableSeeder`등과 같이 직관적인 이름 규칙을 따르는 것이 좋습니다. 기본값으로 `DatabaseSeeder`클래스가 정의되어 있습니다. 이 클래스에서 `call` 메소드로 다른 시드 클래스를 호출해서 시딩(seeding) 순서를 조정할 수 있습니다.

#### 데이터베이스 시드(seed) 클래스 예제

	class DatabaseSeeder extends Seeder {

		public function run()
		{
			$this->call('UserTableSeeder');

			$this->command->info('User table seeded!');
		}

	}

	class UserTableSeeder extends Seeder {

		public function run()
		{
			DB::table('users')->delete();

			User::create(['email' => 'foo@bar.com']);
		}

	}

데이터베이스에 초기값을 설정(seed)하려면 `db:seed` 아티즌 CLI 명령어를 사용하면 됩니다:

	php artisan db:seed

기본적으로 `db:seed` 명령어는 다른 시드(seed) 클래스들을 호출하는 `DatabaseSeeder`클래스를 실행합니다. 하지만 `--class`옵션을 사용해서 특정 시더(seeder) 클래스가 개별적으로 실행되도록 지정할 수 있습니다:

	php artisan db:seed --class=UserTableSeeder

`migrate:refresh`명령어를 사용하면 데이터베이스 초기값을 설정할 때 모든 마이그레이션들을 롤백한 다음 다시 실행 할 것입니다:

	php artisan migrate:refresh --seed

<!--chak-comment-마이그레이션---시딩(Migrations---Seeding)-데이터베이스-시딩-->
