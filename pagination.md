# 페이지네이션(Pagination)

- [설정하기](#configuration)
- [사용법](#usage)
- [페이지 링크에 추가하기](#appending-to-pagination-links)
- [JSON으로 변환](#converting-to-json)

<a name="configuration"></a>
## 설정

다른 프레임 워크에서는 페이지 출력 처리를 하는것은 굉장히 성가신 일입니다. 하지만 라라벨에서 페이징 처리는 식은 죽 먹기입니다. 라라벨에서는 현재 페이지를 기준으로 페이지 링크들의 “범위”를 손쉽게 생성할 수 있습니다.

<!--chak-comment-페이지네이션(Pagination)-설정-->

<a name="usage"></a>
## 사용법

데이터들의 페이징을 처리 할 수 있는 몇 가지 방법이 있습니다. 가장 쉬운 방법은 `paginate` 메소드를 쿼리빌더 혹은 Eloquent model에서 사용하는 것입니다.

#### 데이터베이스 결과에 페이징하기

	$users = DB::table('users')->paginate(15);

> **참고** 현재 라라벨에서`groupBy` 문을 사용하는 경우 페이지를 생성하는 것은 효율적으로 실행될 수 없습니다. 만약 `groupBy` 문의 결과값에 페이징 처리를 사용하고 싶다면 여러분이 직접 데이터베이스를 조회하고 paginator를 만드는 것을 추천합니다.

#### 직접 Paginator 만들기

때때로 여러분은 항목들의 배열을 전달하는 pagination 인스턴스를 직접 만들고자 할 수도 있습니다. 이러한 요구사항에 따라 `Illuminate\Pagination\Paginator` 혹은 `Illuminate\Pagination\LengthAwarePaginator`의 인스턴스를 직접 생성하여 paginator를 수동으로 만들 수도 있습니다.

#### Eloquent Model에서의 페이징

또한, 여러분은 [Eloquent](/docs/5.0/eloquent) models에서도 페이징 처리를 할 수 있습니다:

	$allUsers = User::paginate(15);

	$someUsers = User::where('votes', '>', 100)->paginate(15);

`paginate` 메소드에 넘겨지는 인자는 여러분이 한 페이지 당 표시하고 싶어하는 항목들의 개수입니다. 여러분은 결과들을 검색하고  `render` 메소드를 사용하여 데이터들을 view에 표시하고 페이지번호와 링크를 만들 수 있습니다:

	<div class="container">
		<?php foreach ($users as $user): ?>
			<?php echo $user->name; ?>
		<?php endforeach; ?>
	</div>

	<?php echo $users->render(); ?>

지금까지 보신것들이 페이징 시스템에 필요한 전부입니다! 복잡하게 현재 페이지를 프레임워크에게 알릴 필요가 없다는 것을 기억하세요. 라라벨은 현재 페이지를 자동으로 결정할 것입니다.

아래의 메소드들로 추가적인 페이징 관련 정보들을 가져올 수 있습니다:

- `currentPage`
- `lastPage`
- `perPage`
- `hasMorePages`
- `firstItem`
- `lastItem`
- `url`
- `nextPageUrl`
- `firstItem`
- `lastItem`
- `total`
- `count`

#### “간단한 Pagination"

만약 페이지 번호 없이 “다음”과 “이전” 링크 만을 pagination view에서 보여준다면, 더 효율적으로 쿼리를  수행하는 옵션인`simplePaginate` 메소드를 사용할 수 있습니다:

	$someUsers = User::where('votes', '>', 100)->simplePaginate(15);

#### Paginator URI 커스터마이징

paginator에서 제공하는 `setPath` 메소드를 통해 URI를 변경 할 수 있습니다:

	$users = User::paginate();

	$users->setPath('custom/url');

위의 예제는 다음과 같은 URLs를 만듭니다: http://example.com/custom/url?page=2

<!--chak-comment-페이지네이션(Pagination)-사용법-->

<a name="appending-to-pagination-links"></a>
## 페이지 링크에 추가하기

Paginator의 `appends` 메소드를 사용하면 페이지 링크에 쿼리 스트링을 추가할 수 있습니다:

	<?php echo $users->appends(['sort' => 'votes'])->render(); ?>

위의 예제는 아래와 같은 형태의 URLs를 만들것입니다:

	http://example.com/something?page=2&sort=votes

만약 “hash fragment”를 페이지 번호의 링크에 추가하고 싶다면 `fragment` 메소드를 사용하면 됩니다:

	<?php echo $users->fragment('foo')->render(); ?>

위의 예제와 같이 메소드를 사용하면 아래와 같은 형태의 URLs를 만들것입니다:

	http://example.com/something?page=2#foo

<!--chak-comment-페이지네이션(Pagination)-페이지-링크에-추가하기-->

<a name="converting-to-json"></a>
## JSON으로 변환하기

`Paginator` 클래스는 `Illuminate\Contracts\Support\JsonableInterface` contract을 구현하고`toJson` 메소드를 제공합니다. 따라서 라우트로부터 `Paginator` 객체를  JSON으로 변환할 수 있습니다. 변환된 JSON의 형태는 `total`, `current_page`, 그리고 `last_page`와 같은 메타정보를 포함하고 있습니다. 인스턴스의 데이터는 JSON 배열의 `data` 키를 통해 사용할 수 있습니다.

<!--chak-comment-페이지네이션(Pagination)-JSON으로-변환하기-->
