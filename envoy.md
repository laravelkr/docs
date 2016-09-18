# Envoy(Envoy Task Runner)

- [소개](#introduction)
- [설치](#envoy-installation)
- [작업 수행하기](#envoy-running-tasks)
- [다수의 서버](#envoy-multiple-servers)
- [병렬 실행](#envoy-parallel-execution)
- [작업 매크로](#envoy-task-macros)
- [알림](#envoy-notifications)
- [envoy 업데이트](#envoy-updating-envoy)

<a name="introduction"></a>
## 소개

[라라벨 Envoy](https://github.com/laravel/envoy)는 원격 서버에서 정의된 공통의 작업을 수행하기 위한 간결한 문법을 제공합니다. 블레이드 스타일의 문법을 사용하여 손쉽게 배포, 아티즌 명령어 등의 작업을 설정할 수 있습니다.

> **주의:** Envoy는 PHP 버전 5.4 이상의 Mac 또는 Linux 운영 체제에서 동작합니다.

<!--chak-comment-Envoy(Envoy-Task-Runner)-소개-->

<a name="envoy-installation"></a>
## 설치

먼저 컴포저의 `global` 명령어를 통해서 Envoy를 설치합니다:

	composer global require "laravel/envoy=~1.0"

`~/.composer/vendor/bin` 디렉토리를 여러분의 PATH에 추가하여 터미널에서 `envoy` 명령어를 실행할 때 `envoy`를 바로 찾을 수 있도록 설정하십시오.

그 다음 프로젝트 루트 디렉토리에 `Envoy.blade.php` 파일을 생성합니다. 다음은 간단한 예제입니다:

	@servers(['web' => '192.168.1.1'])

	@task('foo', ['on' => 'web'])
		ls -la
	@endtask

보시다 시피 파일의 제일 윗 부분에 `@servers`에 배열이 정의되어 있습니다. 작업들의 선언 부분에서 `on` 옵션을 통해서 이 서버들을 참조할 수 있습니다. `@task` 선언 부분 안에는 작업이 수행될 때 서버에서 실행될 Bash 코드를 기입합니다.

`init` 명령어를 사용하여 Envoy 파일의 스텁을 쉽게 만들 수 있습니다:

	envoy init user@192.168.1.1

<!--chak-comment-Envoy(Envoy-Task-Runner)-설치-->

<a name="envoy-running-tasks"></a>
## 작업 실행하기

작업을 수행하려면 설치 한 Envoy에서 `run` 명령을 실행하십시오:

	envoy run foo

필요한 경우 명령어의 스위치를 사용하여 Envoy 파일에 변수를 전달할 수 있습니다:

	envoy run deploy --branch=master

Blade 표기법으로 지정한 옵션을 사용할 수 있습니다:

	@servers(['web' => '192.168.1.1'])

	@task('deploy', ['on' => 'web'])
		cd site
		git pull origin {{ $branch }}
		php artisan migrate
	@endtask

#### 부트스트래핑


```@setup``` 지시어를 사용하여 Envoy 파일에서 변수 선언을하거나 일반적인 PHP 코드를 실행 시킬 수 있습니다:

	@setup
		$now = new DateTime();

		$environment = isset($env) ? $env : "testing";
	@endsetup

PHP 파일을 인클루드 하기 위해 ```@include```를 이용할 수도 있습니다:

	@include('vendor/autoload.php');

#### 작업을 수행하기 전에 확인하기

서버에 특정 작업을 실행하기 전에 확인 메시지를 추가하고 싶은 경우에는, `confirm` 지시어를 사용할 수 있습니다 :

	@task('deploy', ['on' => 'web', 'confirm' => true])
		cd site
		git pull origin {{ $branch }}
		php artisan migrate
	@endtask

<!--chak-comment-Envoy(Envoy-Task-Runner)-작업-실행하기-->

<a name="envoy-multiple-servers"></a>
## 다수의 서버

손쉽게 여러서버에서 작업을 실행 시킬 수 있습니다. 간단하게 작업 선언부에 서버의 목록을 기입하면 됩니다:

	@servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

	@task('deploy', ['on' => ['web-1', 'web-2']])
		cd site
		git pull origin {{ $branch }}
		php artisan migrate
	@endtask

By default, the task will be executed on each server serially. Meaning, the task will finish running on the first server before proceeding to execute on the next server. 기본적으로 작업은 각 서버에서 순차적으로 실행 됩니다. 즉, 첫 번째 서버에서 실행이 끝나면 다음 서버의 실행으로 이동합니다.

<!--chak-comment-Envoy(Envoy-Task-Runner)-다수의-서버-->

<a name="envoy-parallel-execution"></a>
## 병렬 실행

여러 서버에서 동시에 작업을 수행하려면 그냥 `parallel` 옵션을 작업 선언에 지정하십시오:

	@servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

	@task('deploy', ['on' => ['web-1', 'web-2'], 'parallel' => true])
		cd site
		git pull origin {{ $branch }}
		php artisan migrate
	@endtask

<!--chak-comment-Envoy(Envoy-Task-Runner)-병렬-실행-->

<a name="envoy-task-macros"></a>
## 작업 매크로

매크로는 하나의 명령으로 순차적으로 실행하는 일련의 작업을 정의합니다. 예를 들면 :

	@servers(['web' => '192.168.1.1'])

	@macro('deploy')
		foo
		bar
	@endmacro

	@task('foo')
		echo "HELLO"
	@endtask

	@task('bar')
		echo "WORLD"
	@endtask

이제 `deploy` 매크로는 하나의 간단한 명령으로 실행됩니다:

	envoy run deploy

<!--chak-comment-Envoy(Envoy-Task-Runner)-작업-매크로-->

<a name="envoy-notifications"></a>
<a name="envoy-hipchat-notifications"></a>
## 알림

#### HipChat

`@hipchat` 지시어를 사용하여 작업을 수행 후, 여러분의 팀의 HipChat 룸에 알림을 보낼 수 있습니다:

	@servers(['web' => '192.168.1.1'])

	@task('foo', ['on' => 'web'])
		ls -la
	@endtask

	@after
		@hipchat('token', 'room', 'Envoy')
	@endafter

또한, 사용자 정의 메시지를 HipChat 룸에 지정할 수도 있습니다. ```@setup``` 또는 ```@include```를 통해서 선언된 변수를 메시지에서 사용할 수 있습니다:

	@after
		@hipchat('token', 'room', 'Envoy', "$task ran on [$environment]")
	@endafter

이것은 서버에서 작업이 실행 된 것을 팀에 알릴 수 있는 매우 간단한 방법입니다.

#### Slack

[Slack](https://slack.com)에 알림을 보내려면 다음 표기법을 사용할 수 있습니다:

	@after
		@slack('hook', 'channel', 'message')
	@endafter

Slack 사이트에 `Incoming WebHooks` 통합을 작성하여 webhook URL을 검색 할 수 있습니다. `hook` 인자는 수신되는 Webhooks Slack 통합에 의해서 제공되어지는 전체 webhook URL 이어야 합니다. 예를 들어 :

`Incoming WebHooks`

	https://hooks.slack.com/services/ZZZZZZZZZ/YYYYYYYYY/XXXXXXXXXXXXXXX

여러분은 채널 인자에 대해 다음중 하나를 선택할 수 있습니다:

- `#channel` 채널에 알림 보내기
- `@user` 사용자에게 알림 보내기

`channel` 인자가 없는 경우에 기본 채널이 사용되어 집니다.

> Note의: Slack 알림은 작업이 성공적으로 완료되었을 시에만 보내집니다.

<!--chak-comment-Envoy(Envoy-Task-Runner)-알림-->

<a name="envoy-updating-envoy"></a>
## Envoy 업데이트

Envoy를 업데이트 하려면 컴포저를 사용합니다:

	composer global update

<!--chak-comment-Envoy(Envoy-Task-Runner)-Envoy-업데이트-->

