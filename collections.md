# 컬렉션(Collections)

- [소개](#introduction)
- [기본 사용법](#basic-usage)

<a name="introduction"></a>
## 소개

`Illuminate\Support\Collection` 클래스는 배열 데이터를 사용하기 위한 유연하고 편리한 래퍼(wrapper)를 제공합니다. 다음 사용 예를 확인하십시오. 배열을 통해서 새로운 collection 인스턴스를 생성하기 위해서 `collect` 헬퍼함수를 사용합니다:

	$collection = collect(['taylor', 'abigail', null])->map(function($name)
	{
		return strtoupper($name);
	})
	->reject(function($name)
	{
		return empty($name);
	});


보시는 바와 같이 `Collection` 클래스는 map 메소드와 reject메소드를 체이닝 방식으로 사용할 수 있게 해줍니다. 다시 말해서 모든 `Collection`의 메소드는 `Collection`의 인스턴스를 반환합니다. 더 자세한 내용을 원하면 다음을 읽어보세요!

<!--chak-comment-컬렉션(Collections)-소개-->

<a name="basic-usage"></a>
## 기본 사용법

#### 컬렉션 생성하기

앞서 설명한대로 `collect` 헬퍼 함수는 주어진 배열 대신 새로운 `Illuminate\Support\Collection` 인스턴스를 반환합니다. 또는, `Collection` 클래스의 `make`를 사용할 수도 있습니다:

	$collection = collect([1, 2, 3]);

	$collection = Collection::make([1, 2, 3]);

당연하게도 [Eloquent](/docs/5.0/eloquent)객체의 컬렉션은 항상 `Collection` 인스턴스를 반환합니다. 하지만 어플리케이션의 어디에서라도 `Collection`을 편하게 사용할 수 있습니다.

#### 컬렉션 둘러보기

컬렉션에서 사용 가능한 메소드를 여기에 모두 나열하는 것보다(상당히 많으므로) [API 문서](http://laravel.com/api/master/Illuminate/Support/Collection.html)를 살펴보시기 바랍니다!

<!--chak-comment-컬렉션(Collections)-기본-사용법-->
