# 레디스(Redis)

- [소개](#introduction)
- [설정](#configuration)
- [사용법](#usage)
- [파이프라이닝](#pipelining)

<a name="introduction"></a>
## 소개

[레디스](http://redis.io)는 키-밸류 기반의 오픈소스 저장소입니다. 레디스는 키에 [문자열](http://redis.io/topics/data-types#strings), [해쉬](http://redis.io/topics/data-types#hashes), [리스트](http://redis.io/topics/data-types#lists), [세트](http://redis.io/topics/data-types#sets), 그리고 [정렬 세트](http://redis.io/topics/data-types#sorted-sets)를 사용할 수 있으므로 데이터 구조 서버로 자주 거론되고 있습니다.

라라벨에서 레디스를 사용하기전에 컴포저로 `predis/predis` 패키지를 설치해주어야 합니다.

> **주의:** 이미 PECL을 통해서 레디스 PHP 익스텐션을 설치하였다면, `config/app.php`에서 Redis 별칭-alias를 수정해야 합니다.

<!--chak-comment-레디스(Redis)-소개-->

<a name="configuration"></a>
## 설정

어플리케이션에서 사용할 레디스의 설정은 `config/database.php` 파일에서 지정할 수 있습니다. 파일 내부의 `redis` 배열이 어플리케이션에서 사용할 레디스 서버의 설정 정보를 담고 있습니다:

	'redis' => [

		'cluster' => true,

		'default' => ['host' => '127.0.0.1', 'port' => 6379],

	],

기본적으로 설정된 서버는 개발시에는 충분할 수 있습니다. 하지만 환경에 맞게 설정을 변경할 수도 있습니다. 간단하게 레디스 서버의 이름과 그에 맞는 호스트 및 포트번호를 설정하면 됩니다.

옵션은 라라벨 레디스 클라이언트에게 레디스 노드에 클라이언트 측 샤딩을 수행 할 수 있도록 하여 노드를 풀링하고 사용 가능한 RAM을 가능한 많이 생성할 수 있도록 합니다. 하지만 클라이언트 샤딩은 페일오버를 처리하지는 않기 때문에 다른 기본 저장소를 위해 사용할 캐시 데이터를 취급하는 데 주로 적합합니다.

만약 레디스 서버에 인증이 필요하다면 레디스 서버 설정 배열에 `password` 키/밸류 값을 추가하면 됩니다.

<!--chak-comment-레디스(Redis)-설정-->

<a name="usage"></a>
## 사용법

`Redis::connection` 메소드를 호출하여 레디스 인스턴스를 가져올 수 있습니다:

	$redis = Redis::connection();

이 구문은 기본 레디스 서버의 인스턴스를 반환 합니다. 만약 클러스터링을 사용하지 않고 있으면서, 레디스 설정에서 정의되어 있는 특정 서버 인스턴스를 가져오려면 미리 지정된 서버의 이름을 `connection` 메소드에 파라미터로 넘겨주면 됩니다:

	$redis = Redis::connection('other');

레디스 클라이언트의 인스턴스를 가져왔다면 이를 통해서 어떤 [레디스 명령어](http://redis.io/commands)라도 실행할 수 있습니다. 라라벨은 매직 메소드를 통해서 레디스 서버에 명령어를 전달합니다:

	$redis->set('name', 'Taylor');

	$name = $redis->get('name');

	$values = $redis->lrange('names', 5, 10);

메소드의 매개 변수가 매직 메소드를 통해서 전달된다는 것에 주의 하십시오. 물론 `command` 메소드를 사용하여 명령어를 서버에 직접 전달 할 수도 있습니다:

	$values = $redis->command('lrange', [5, 10]);

만약 기본 커넥션을 통해서 명령어를 실행 시키는 것이라면 간단하게 `Redis` 클래스의 스태틱 매직 메소드를 통해서 실행할 수 있습니다:

	Redis::set('name', 'Taylor');

	$name = Redis::get('name');

	$values = Redis::lrange('names', 5, 10);

> **참고:** 레디스 [캐시](/docs/5.0/cache)와 [세션](/docs/5.0/session) 드라이버가 라라벨에 포함되어 있습니다.

<!--chak-comment-레디스(Redis)-사용법-->

<a name="pipelining"></a>
## 파이프라이닝

파이프라이닝은 다수의 명령어를 한번에 서버로 보내야 할 때 사용되어집니다. `pipeline` 명령어를 통해서 시작할 수 있습니다:

#### 서버에 다수의 명령어들 파이핑하기

	Redis::pipeline(function($pipe)
	{
		for ($i = 0; $i < 1000; $i++)
		{
			$pipe->set("key:$i", $i);
		}
	});

<!--chak-comment-레디스(Redis)-파이프라이닝-->
