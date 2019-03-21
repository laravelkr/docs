# 라라벨 한국어 매뉴얼

이 문서는 라라벨 공식 매뉴얼을 한국어로 번역하여 제공합니다. 라라벨을 이해하고 프레임워크를 사용하는데 영어라는 장벽으로 인해서 겪는 문제점들을 해소하기 위해서 마련되었습니다.

## 라라벨 프레임워크

라라벨은 PHP 에서 사용할 수 있는 가장 모던하고 세련된 프레임워크이며, 유연하고 세련된 기능을 제공합니다. 라라벨은 PHP개발이 즐겁고 좀더 창의적일 수 있도록 하기 위해서 만들어졌습니다. 라라벨을 사용하면 PHP를 사용한 웹 개발에 있어서, 보다 중요한 비지니스 로직을 구현하는데 집중할 수 있습니다.

[php 프레임워크 트렌드 살펴보기](https://www.google.com/trends/explore?q=codeigniter,laravel,cakephp)


## 매뉴얼 현황
 * [라라벨 5.0](https://laravel.kr/docs/5.0)
 * [라라벨 5.1](https://laravel.kr/docs/5.1)
 * [라라벨 5.2](https://laravel.kr/docs/5.2)
 * [라라벨 5.3](https://laravel.kr/docs/5.3)
 * [라라벨 5.4](https://laravel.kr/docs/5.4)
 * [라라벨 5.5](https://laravel.kr/docs/5.5)
 * [라라벨 5.6](https://laravel.kr/docs/5.6)
 * [라라벨 5.7](https://laravel.kr/docs/5.7)

## 기여방법

라라벨의 한글 매뉴얼은 많은 분들의 기여를 통해서 완성되었습니다. 현재도 계속적으로 업데이트가 이루어 지고 있으며, 오타나 수정사항에 대해서는 [laravel@laravel.kr](mailto:laravel@laravel.kr)에 문의주시거나 [Github](https://github.com/laravelkr/docs) 에서 [Pull Request](https://github.com/laravelkr/docs/pulls) 를 보내주시면 검토후 반영됩니다.


### 깃허브 이슈 등록 

[이슈](https://github.com/laravelkr/docs/issues/new) 에 수정 요청사항을 등록해주시면 됩니다

### Pull Request 

풀리퀘스트의 경우 아래의 작업룰에 따라 등록을 해주셔야 합니다
기본 작업 가이드는 아래와 같으며 자세한 사항은 [WIKI](https://github.com/laravelkr/docs/wiki)를 참조해 주시기 바랍니다

#### 오타 및 표현의 오류 수정

- 해당하는 버전에 맞는 pre-kr-{version}의 브랜치에 pull request를 해주시면 됩니다
- pre-kr 브랜치의 경우 반드시 kr 폴더 내에 있는 파일만 수정하시고 커밋을 하셔야합니다 
- kr-{version} 브랜치의 경우 실제 매뉴얼을 서비스하기 위한 문서 파일 이므로 관리자가 직접 수정하고 있습니다
- kr-{버전} 브랜치의 pr도 같이 주신다면 문서의 반영이 조금 더 빠르게 됩니다 

#### 영어 원문의 업데이트

- 원문의 수정사항을 업데이트 할 경우 pre-kr 브랜치에 [원문 레포](https://github.com/laravel/docs)의 해당하는 버전에 맞는 브랜치의 커밋을 merge 합니다
  - 즉 최상위 폴더에 있는 md파일의 경우 원본의 커밋히스토리와 merge 히스토리만이 존재해야합니다
- 이후 merge 전의 내용과 비교하여 수정된 내용을 kr 폴더의 영어 부분에 모두 업데이트 해 줍니다
  - 이 작업은 현재 수기로 모두 원문을 체크해서 진행하고 있습니다
  - 이 과정을 위해 merge한 내용은 반드시 한번에 모두 번역한 이후 PR을 요청하셔야 합니다
- 위의 과정이 종료된 이후 kr폴더내의 내용 검수가 끝나면 영어 부분을 모두 삭제한 이후 한글 부분만 남기고 kr브랜치의 md파일에 업데이트 합니다



### 기여자 보상
- 현재 pre-kr의 kr 폴더내 파일에 커밋한 이력을 기준으로 매뉴얼 사이트에서 문서 하단에 기여자분들의 깃헙 정보를 추가해드리고 있습니다
  - 향후 issue 등록에 대한 기여보상 방식을 추가예정입니다


## 라이센스 및 상업적 이용

라라벨 한국어 매뉴얼은 영문 매뉴얼과 동일하게 MIT 라이센스를 따릅니다. 상업적 사용을 원하시는 경우 제한없이 사용하실 수 있으나, 매뉴얼 업데이트 사항을 알리거나, 관련 사용 정보를 수집하기 위해서 사용시 [laravel@laravel.kr](mailto:laravel@laravel.kr) 로 사용처에 대해서 간략하게 알려주시기 바랍니다.

## 커뮤니티

*라라벨 코리아 커뮤니티* [laravel.kr](https://laravel.kr)는 라라벨을 사용하거나 관심 있는 사람들이 모여 뉴스와 정보를 공유하고 문제를 같이 해결하기 위해서 마련한 곳입니다. 다양한 의견을 보내주세요. `라라벨 코리아 커뮤니티(Laravel Korea Community)`에서 확인하실 수 있습니다.

또한 페이스북 그룹([Laravel Korea](https://www.facebook.com/groups/laravelkorea/) 와 [Modern PHP User Group](https://www.facebook.com/groups/655071604594451/))에서도 활발하게 논의가 이루어 지고 있습니다.

## 채팅 참여 방법

채팅에 참여하려면 [슬랙](https://modernpug.slack.com)으로 [참가하기](http://slack-invite.modernpug.org/)를 신청하면 됩니다. 채널은 `#laravel`에서 이야기 하고 있습니다.

## Contributors

- [XpressEngine](https://xpressengine.io)
- [ModernPHP](http://www.modernpug.org/)
