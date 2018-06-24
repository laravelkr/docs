# Collections 컬렉션

- [Introduction 소개](#introduction)
- [Basic Usage 기본 사용법](#basic-usage)

<a name="introduction"></a>
## Introduction 소개

The `Illuminate\Support\Collection` class provides a fluent, convenient wrapper for working with arrays of data. `Illuminate\Support\Collection` 클래스는 배열 데이터를 사용하기위한  유연하고 편리한 래퍼(wrapper)를 제공합니다. For example, check out the following code. 다음 사용예를 확인하십시오. We'll use the `collect` helper to create a new collection instance from the array: 배열을 통해서 새로운 collection 인스턴스를 생성하기 위해서 `collect` 헬퍼 함수를 사용합니다. 

	$collection = collect(['taylor', 'abigail', null])->map(function($name)
	{
		return strtoupper($name);
	})
	->reject(function($name)
	{
		return empty($name);
	});


As you can see, the `Collection` class allows you to chain its methods to perform fluent mapping and reducing of the underlying array. 보시는 바와 같이 `Collection` 클래스는 map 매소드와 reject메소드를 체이닝 방식으로 사용할 수 있게 해줍니다. In general, every `Collection` method returns an entirely new `Collection` instance. 다시말해서 모든 `Collection`의 메소드는 `Collection`의 인스턴스를 반환합니다. To dig in further, keep reading!  더 자세한 내용을 원하면 다음을 읽어보세요. 


<a name="basic-usage"></a>
## Basic Usage 기본 사용법

#### Creating Collections 컬렉션 생성하기

As mentioned above, the `collect` helper will return a new `Illuminate\Support\Collection` instance for the given array. 앞서 설명한대로 `collect` 헬퍼 함수는 주어진 배열 대신 새로운 `Illuminate\Support\Collection` 인스턴스를 반환합니다. You may also use the `make` command on the `Collection` class: 또는 `Collection` 클래스의 `make` 를 사용할 수도 있습니다. 

	$collection = collect([1, 2, 3]);

	$collection = Collection::make([1, 2, 3]);

Of course, collections of [Eloquent](/docs/{{version}}/eloquent) objects are always returned as `Collection` instances; 당연하게도 [Eloquent](/docs/{{version}}/eloquent)객체의 컬렉션은 항상 `Collection` 인스턴스를 반환합니다. however, you should feel free to use the `Collection` class wherever it is convenient for your application. 하지만 애플리케이션의 어디에서라도 편하게 `Collection`을 사용할 수 있습니다. 

#### Explore The Collection 컬렉션 둘러보기

Instead of listing all of the methods (there are a lot) the Collection makes available, check out the [API documentation for the class](http://laravel.com/api/master/Illuminate/Support/Collection.html)! 컬렉션에서 사용가능한 메소드를 여기에 모두 나열하는 것 보다 (상당히 많으므로) [API 문서](http://laravel.com/api/master/Illuminate/Support/Collection.html)를 살펴보시기 바랍니다!
