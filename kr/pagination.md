# Pagination

- [Configuration](#configuration)
- [Usage](#usage)
- [Appending To Pagination Links](#appending-to-pagination-links)
- [Converting To JSON](#converting-to-json)

<a name="configuration"></a>
## Configuration
## 설정

In other frameworks, pagination can be very painful. Laravel makes it a breeze. Laravel can generate an intelligent "range" of links based on the current page. The generated HTML is compatible with the Bootstrap CSS framework.
다른 프레임 워크에서는 페이징 처리를 하는것은 굉장히 성가신 일입니다. 하지만 Laravel에서 페이징 처리는 식은 죽 먹기입니다. Laravel은 현재 페이지에 따라 링크들의 지능형 “범위”를 생성할 수 있습니다.

<a name="usage"></a>
## Usage

There are several ways to paginate items. The simplest is by using the `paginate` method on the query builder or an Eloquent model.
아이템들을 페이징 처리 할 수 있는 몇가지 방법이 있습니다. 가장 쉬운 방법은 `paginate` 메소드를 query builder 혹은 Eloquent model 에서 사용하는 것이지요.

#### Paginating Database Results

	$users = DB::table('users')->paginate(15);

> **Note:** Currently, pagination operations that use a `groupBy` statement cannot be executed efficiently by Laravel. If you need to use a `groupBy` with a paginated result set, it is recommended that you query the database and create a paginator manually.
> **노트** 현재, `groupBy` 문을 사용하여 페이지를 생성하는 것은 라라벨에서 효율적으로 실행될 수 없습니다. 만약 `groupBy` 문을 결과값에 페이징 처리 하는데 사용하고 싶다면 여러분이 직접 database를 조회하고 paginator를 만드는 것을 추천합니다.

#### Creating A Paginator Manually
#### 직접 Paginator 만들기

Sometimes you may wish to create a pagination instance manually, passing it an array of items. You may do so by creating either an `Illuminate\Pagination\Paginator` or `Illuminate\Pagination\LengthAwarePaginator` instance, depending on your needs.
때때로 여러분은 항목들의 배열을 전달하는 pagination instance를 직접 만들기를 원할 수 있습니다.  요구에 따라 `Illuminate\Pagination\Paginator` 혹은 `Illuminate\Pagination\LengthAwarePaginator` instance를 생성하여 paginator를 직접 수동으로 만들 수 있습니다.

#### Paginating An Eloquent Model
#### Eloquent Model 에서의 페이징

You may also paginate [Eloquent](/docs/5.0/eloquent) models:
여러분은 [Eloquent](/docs/5.0/eloquent) models 에서도 역시 페이징 처리를 할 수 있습니다.

	$allUsers = User::paginate(15);

	$someUsers = User::where('votes', '>', 100)->paginate(15);

The argument passed to the `paginate` method is the number of items you wish to display per page. Once you have retrieved the results, you may display them on your view, and create the pagination links using the `render` method:
`paginate` 메소드에 넘겨지는 인수는 여러분이 한 페이지 당 표시하고 싶어하는 항목들의 개수 입니다.  여러분은 결과들을 검색하고 그것들을 view에 표시하고 페이지번호와 링크를  `render` 메소드를 사용하여 만들것입니다.

	<div class="container">
		<?php foreach ($users as $user): ?>
			<?php echo $user->name; ?>
		<?php endforeach; ?>
	</div>

	<?php echo $users->render(); ?>

This is all it takes to create a pagination system! Note that we did not have to inform the framework of the current page. Laravel will determine this for you automatically.
이것들이 페이징 시스템을 만드는데 필요한 모든 것입니다! 현재 페이지를 프레임워크에게 알릴 필요가 없다는 것을 기억하세요. 라라벨은 현재 페이지를 자동으로 결정할 것입니다.


You may also access additional pagination information via the following methods:
아래의 메소드들로 추가적인 페이징 정보들을 가져올 수 있습니다.

- `currentPage`
- `lastPage`
- `perPage`
- `hasMorePages`
- `url`
- `nextPageUrl`
- `total`
- `count`

#### "Simple Pagination"
#### “간단한 Pagination"

If you are only showing "Next" and "Previous" links in your pagination view, you have the option of using the `simplePaginate` method to perform a more efficient query. This is useful for larger datasets when you do not require the display of exact page numbers on your view:
만약 페이지 번호 없이 “다음” 과 “이전” 링크 만을 pagination view에서 보여준다면, 더 효율적으로 쿼리를  수행하는 옵션인`simplePaginate` 메소드를 활용할 수 있습니다.

	$someUsers = User::where('votes', '>', 100)->simplePaginate(15);

#### Customizing The Paginator URI
#### Paginator URI 커스터마이징

You may also customize the URI used by the paginator via the `setPath` method:
paginator 에서 제공하는 `setPath` 메소드를 통해 URI를 커스터마이즈 할 수 있습니다.

	$users = User::paginate();

	$users->setPath('custom/url');

The example above will create URLs like the following: http://example.com/custom/url?page=2
위의 예제는 다음과 같은 URLs를 만듭니다: http://example.com/custom/url?page=2

<a name="appending-to-pagination-links"></a>
## Appending To Pagination Links
페이지 번호 링크에 추가하기

You can add to the query string of pagination links using the `appends` method on the Paginator:
Paginator의 `appends` 메소드를 사용하면 페이지 번호의 링크에 쿼리 스트링을 추가할 수 있습니다.

	<?php echo $users->appends(['sort' => 'votes'])->render(); ?>

This will generate URLs that look something like this:
위의 예제는 아래와 같은 형태의  URLs를 만들것입니다:

	http://example.com/something?page=2&sort=votes

If you wish to append a "hash fragment" to the paginator's URLs, you may use the `fragment` method:
만약 “hash fragment”를 페이지 번호의 링크에 추가하고 싶다면 `fragment` 메소드를 사용하면 됩니다.

	<?php echo $users->fragment('foo')->render(); ?>

This method call will generate URLs that look something like this:
위의 예제와 같이 메소드를 사용하면 아래와 같은 형태의 URLs를 만들것입니다.

	http://example.com/something?page=2#foo

<a name="converting-to-json"></a>
## Converting To JSON
## JSON으로 변환하기.

The `Paginator` class implements the `Illuminate\Contracts\Support\JsonableInterface` contract and exposes the `toJson` method. You may also convert a `Paginator` instance to JSON by returning it from a route. The JSON'd form of the instance will include some "meta" information such as `total`, `current_page`, and `last_page`. The instance's data will be available via the `data` key in the JSON array.

The `Paginator` class implements the `Illuminate\Contracts\Support\JsonableInterface` contract and exposes the `toJson` method.  또한 `Paginator` instance를  라우트로부터 반환하여 `Paginator` 인스턴스를 JSON으로 변환할 수 있습니다.  인스턴스의 JSON’d 형태는 `total`, `current_page`, 그리고 `last_page`와 같은 메타정보를 포함할 것입니다. 인스턴스의 데이터는 JSON 배열의 `data` 키를 통해 사용할 수 있습니다.
