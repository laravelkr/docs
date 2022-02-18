# 라라벨 한국어 매뉴얼

[![All Contributors](https://img.shields.io/badge/all_contributors-67-orange.svg?style=flat-square)](#contributors)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](https://github.com/laravelkr/docs/pulls)
[![GitHub issues](https://img.shields.io/github/issues/laravelkr/docs.svg)](https://github.com/laravelkr/docs/issues)
[![GitHub license](https://img.shields.io/github/license/laravelkr/docs.svg)](https://github.com/laravelkr/docs/blob/master/license.md)
[![GitHub stars](https://img.shields.io/github/stars/laravelkr/docs.svg?style=social)](https://github.com/laravelkr/docs)

이 페이지는 라라벨을 이해하고 사용하는데 영어라는 장벽으로 인해서 겪는 문제점들을 해소하기 위해서 마련되었습니다.

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
 * [라라벨 5.8](https://laravel.kr/docs/5.8)
 * [라라벨 6.x](https://laravel.kr/docs/6.x)
 * [라라벨 7.x](https://laravel.kr/docs/7.x)
 * [라라벨 8.x](https://laravel.kr/docs/8.x)

## 기여방법

라라벨의 한글 매뉴얼은 많은 분들의 기여를 통해서 완성되었습니다. 현재도 계속적으로 업데이트가 이루어 지고 있으며, 오타나 수정사항에 대해서는 [laravel@laravel.kr](mailto:laravel@laravel.kr)에 문의주시거나 [Github](https://github.com/laravelkr/docs) 에서 [Pull Request](https://github.com/laravelkr/docs/pulls) 를 보내주시면 검토후 반영됩니다.


### 1. 깃허브 이슈 등록 

[이슈](https://github.com/laravelkr/docs/issues/new) 에 수정 요청사항을 등록해주시면 됩니다

### 2. Pull Request 

풀리퀘스트의 경우 아래의 작업룰에 따라 등록을 해주셔야 합니다
기본 작업 가이드는 아래와 같으며 자세한 사항은 [WIKI](https://github.com/laravelkr/docs/wiki) 를 참조해 주시기 바랍니다

#### 오타 및 표현의 오류 수정

- 해당하는 버전에 맞는 pre-kr-{version}의 브랜치에 pull request를 해주시면 됩니다
  - pre-kr 브랜치의 경우 반드시 kr 폴더 내에 있는 파일만 수정하시고 커밋을 하셔야합니다 
- kr-{version} 브랜치의 경우 실제 매뉴얼을 서비스하기 위한 문서 파일 이므로 관리자가 직접 수정하고 있습니다
  - kr-{버전} 브랜치의 pr도 같이 주신다면 문서의 반영이 조금 더 빠르게 됩니다 

#### 영어 원문의 업데이트

- 원문의 수정사항을 업데이트 할 경우 pre-kr 브랜치에 [원문 레포](https://github.com/laravel/docs) 의 해당하는 버전에 맞는 브랜치의 커밋을 merge 합니다
  - 즉 최상위 폴더에 있는 md파일의 경우 원본의 커밋히스토리와 merge 히스토리만이 존재해야합니다
- 이후 merge 전의 내용과 비교하여 수정된 내용을 kr 폴더의 영어 부분에 모두 업데이트 해 줍니다
  - 이 작업은 현재 수기로 모두 원문을 체크해서 진행하고 있습니다
  - 이 과정을 위해 merge한 내용은 반드시 한번에 모두 번역한 이후 PR을 요청하셔야 합니다
- 위의 과정이 종료된 이후 kr폴더내의 내용 검수가 끝나면 영어 부분을 모두 삭제한 이후 한글 부분만 남기고 kr브랜치의 md파일에 업데이트 합니다



### 기여자 보상

- 매뉴얼 레포지터리 안내 페이지 및 홈페이지 매뉴얼 페이지에 전체 기여자 리스트를 표시해드리고 있습니다.
- 매뉴얼 페이지 하단에 해당 문서의 번역자를 표시해 드리고 있습니다.


## 라이센스 및 상업적 이용

라라벨 한국어 매뉴얼은 영문 매뉴얼과 동일하게 MIT 라이센스를 따릅니다. 상업적 사용을 원하시는 경우 제한없이 사용하실 수 있으나, 매뉴얼 업데이트 사항을 알리거나, 관련 사용 정보를 수집하기 위해서 사용시 [laravel@laravel.kr](mailto:laravel@laravel.kr) 로 사용처에 대해서 간략하게 알려주시기 바랍니다.

## 커뮤니티

*라라벨 코리아 커뮤니티* [laravel.kr](https://laravel.kr)는 라라벨을 사용하거나 관심 있는 사람들이 모여 뉴스와 정보를 공유하고 문제를 같이 해결하기 위해서 마련한 곳입니다. 다양한 의견을 보내주세요. `라라벨 코리아 커뮤니티(Laravel Korea Community)`에서 확인하실 수 있습니다.

또한 페이스북 그룹([Laravel Korea](https://www.facebook.com/groups/laravelkorea/) 와 [Modern PHP User Group](https://www.facebook.com/groups/655071604594451/))에서도 활발하게 논의가 이루어 지고 있습니다.

## 채팅 참여 방법

채팅에 참여하려면 [디스코드](https://discord.gg/9BuTr5K63g) 채널 `#📚번역-좀-하시죠`로 접속하시면 됩니다

## Contributors

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tr>
    <td align="center"><a href="http://findstar.pe.kr"><img src="https://avatars2.githubusercontent.com/u/1266944?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Jung-Soo Ahn</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=findstar" title="Documentation">📖</a></td>
    <td align="center"><a href="http://kkame.net"><img src="https://avatars3.githubusercontent.com/u/4939813?v=4?s=100" width="100px;" alt=""/><br /><sub><b>KKAME</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=kkame" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/natz92"><img src="https://avatars1.githubusercontent.com/u/25763747?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Natz</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=natz92" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/ukits"><img src="https://avatars1.githubusercontent.com/u/946148?v=4?s=100" width="100px;" alt=""/><br /><sub><b>SungUk Byun</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=ukits" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/whdckszxxx"><img src="https://avatars2.githubusercontent.com/u/14170948?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Jong Chan Park</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=whdckszxxx" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/kwonmory"><img src="https://avatars3.githubusercontent.com/u/12936720?v=4?s=100" width="100px;" alt=""/><br /><sub><b>KWONMORY</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=kwonmory" title="Documentation">📖</a></td>
    <td align="center"><a href="https://lastgleam.github.io"><img src="https://avatars0.githubusercontent.com/u/18328030?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Donghee KIM</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=lastgleam" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://mingeun.com"><img src="https://avatars2.githubusercontent.com/u/19664237?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Mingeun Kim</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=getsolaris" title="Documentation">📖</a></td>
    <td align="center"><a href="https://acidf0x.github.io"><img src="https://avatars0.githubusercontent.com/u/35107271?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Duhui Cho</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=AcidF0x" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/idpokute"><img src="https://avatars1.githubusercontent.com/u/5393574?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Mike Sehui Park</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=idpokute" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/comsiro"><img src="https://avatars3.githubusercontent.com/u/12705399?v=4?s=100" width="100px;" alt=""/><br /><sub><b>comsiro</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=comsiro" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/youngiggy"><img src="https://avatars1.githubusercontent.com/u/1668413?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Joo Youngik</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=youngiggy" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/NoGeunYoug"><img src="https://avatars2.githubusercontent.com/u/22785651?v=4?s=100" width="100px;" alt=""/><br /><sub><b>NoGeunYoug</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=NoGeunYoug" title="Documentation">📖</a></td>
    <td align="center"><a href="http://leehyunseok.com"><img src="https://avatars2.githubusercontent.com/u/6157033?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Hyunseok Lee</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=smartbos" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://github.com/Hann"><img src="https://avatars2.githubusercontent.com/u/718811?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Jinsoo Han</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=Hann" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/YangMinJoo"><img src="https://avatars2.githubusercontent.com/u/24889107?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Cherry Pie</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=YangMinJoo" title="Documentation">📖</a></td>
    <td align="center"><a href="https://yupmin.net/"><img src="https://avatars1.githubusercontent.com/u/880878?v=4?s=100" width="100px;" alt=""/><br /><sub><b>yun young-jin</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=yupmin" title="Documentation">📖</a></td>
    <td align="center"><a href="http://web-front-end.tistory.com/"><img src="https://avatars2.githubusercontent.com/u/9584768?v=4?s=100" width="100px;" alt=""/><br /><sub><b>David Kwon</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=tienne" title="Documentation">📖</a></td>
    <td align="center"><a href="http://kr.vuejs.org"><img src="https://avatars0.githubusercontent.com/u/1451365?v=4?s=100" width="100px;" alt=""/><br /><sub><b>ChangJoo Park(박창주)</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=ChangJoo-Park" title="Documentation">📖</a></td>
    <td align="center"><a href="https://zerglinggo.net/"><img src="https://avatars0.githubusercontent.com/u/3365053?v=4?s=100" width="100px;" alt=""/><br /><sub><b>ZerglingGo</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=ZerglingGo" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/dspaudio"><img src="https://avatars1.githubusercontent.com/u/869240?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Wonkyoo Nam</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=dspaudio" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://github.com/GoodGoodJM"><img src="https://avatars3.githubusercontent.com/u/8029093?v=4?s=100" width="100px;" alt=""/><br /><sub><b>GoodGoodMan</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=GoodGoodJM" title="Documentation">📖</a></td>
    <td align="center"><a href="http://modernpug.org"><img src="https://avatars3.githubusercontent.com/u/8666157?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Modern PHP User Group</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=ModernPug" title="Documentation">📖</a></td>
    <td align="center"><a href="https://www.xpressengine.io/"><img src="https://avatars3.githubusercontent.com/u/1429259?v=4?s=100" width="100px;" alt=""/><br /><sub><b>XpressEngine</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=XpressEngine" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/inyoungk"><img src="https://avatars2.githubusercontent.com/u/48192948?v=4?s=100" width="100px;" alt=""/><br /><sub><b>inyoungk</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=inyoungk" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/strawoo"><img src="https://avatars0.githubusercontent.com/u/11594582?v=4?s=100" width="100px;" alt=""/><br /><sub><b>WooHyunPark</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=strawoo" title="Documentation">📖</a></td>
    <td align="center"><a href="https://blog.meteopark.dev"><img src="https://avatars1.githubusercontent.com/u/8869584?v=4?s=100" width="100px;" alt=""/><br /><sub><b>meteopark</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=meteopark" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/anym0re"><img src="https://avatars3.githubusercontent.com/u/9912065?v=4?s=100" width="100px;" alt=""/><br /><sub><b>danlee</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=anym0re" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="http://velmont.cafe24.com"><img src="https://avatars3.githubusercontent.com/u/3744320?v=4?s=100" width="100px;" alt=""/><br /><sub><b>HoraceVelmont</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=horace-velmont" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/KangPilGyu"><img src="https://avatars3.githubusercontent.com/u/39696372?v=4?s=100" width="100px;" alt=""/><br /><sub><b>eiffeltop</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=KangPilGyu" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/jungmyungzZ"><img src="https://avatars0.githubusercontent.com/u/42092924?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Kim Jungmyung</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=jungmyungzZ" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/cherryred5959"><img src="https://avatars2.githubusercontent.com/u/32331576?v=4?s=100" width="100px;" alt=""/><br /><sub><b>cherry</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=cherryred5959" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/lucyholic"><img src="https://avatars3.githubusercontent.com/u/37027571?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Lucy Kim</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=lucyholic" title="Documentation">📖</a></td>
    <td align="center"><a href="http://jaedong.kim"><img src="https://avatars0.githubusercontent.com/u/978944?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Jaedong Kim</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=jdssem" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/fwang-laralabs"><img src="https://avatars1.githubusercontent.com/u/26479627?v=4?s=100" width="100px;" alt=""/><br /><sub><b>fwang-laralabs</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=fwang-laralabs" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://github.com/dhtmdgkr123"><img src="https://avatars1.githubusercontent.com/u/27611405?v=4?s=100" width="100px;" alt=""/><br /><sub><b>dhtmdgkr123</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=dhtmdgkr123" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/picory"><img src="https://avatars3.githubusercontent.com/u/3766114?v=4?s=100" width="100px;" alt=""/><br /><sub><b>picory</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=picory" title="Documentation">📖</a></td>
    <td align="center"><a href="https://devseok.github.io/"><img src="https://avatars3.githubusercontent.com/u/33000980?v=4?s=100" width="100px;" alt=""/><br /><sub><b>ohjinseok</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=devSeok" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/NalCoder"><img src="https://avatars0.githubusercontent.com/u/57889823?v=4?s=100" width="100px;" alt=""/><br /><sub><b>NalCoder</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=NalCoder" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/soilSpoon"><img src="https://avatars1.githubusercontent.com/u/9105017?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Hee</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=soilSpoon" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/initred"><img src="https://avatars3.githubusercontent.com/u/34208843?v=4?s=100" width="100px;" alt=""/><br /><sub><b>InitRed</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=initred" title="Documentation">📖</a></td>
    <td align="center"><a href="https://ahfk8762.tistory.com/"><img src="https://avatars2.githubusercontent.com/u/35286689?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Kwon Sung Doo</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=KDudoo" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://github.com/kamikax"><img src="https://avatars1.githubusercontent.com/u/6329608?v=4?s=100" width="100px;" alt=""/><br /><sub><b>kamikax</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=kamikax" title="Documentation">📖</a></td>
    <td align="center"><a href="https://curlychoi.dev"><img src="https://avatars0.githubusercontent.com/u/1305855?v=4?s=100" width="100px;" alt=""/><br /><sub><b>curlychoi</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=curlychoi" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/humble92"><img src="https://avatars3.githubusercontent.com/u/1191154?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Daniel</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=humble92" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/sungjoomoon"><img src="https://avatars2.githubusercontent.com/u/61780681?v=4?s=100" width="100px;" alt=""/><br /><sub><b>sungjoomoon</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=sungjoomoon" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/stseira"><img src="https://avatars3.githubusercontent.com/u/199357?v=4?s=100" width="100px;" alt=""/><br /><sub><b>YeongGwon Alex Jeong</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=stseira" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/nugmubs"><img src="https://avatars2.githubusercontent.com/u/61725446?v=4?s=100" width="100px;" alt=""/><br /><sub><b>nugmubs</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=nugmubs" title="Documentation">📖</a></td>
    <td align="center"><a href="https://bitbucket.org/ji667623/"><img src="https://avatars1.githubusercontent.com/u/19298367?v=4?s=100" width="100px;" alt=""/><br /><sub><b>JasSung Cho</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=ji6676" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="http://www.palgle.com"><img src="https://avatars3.githubusercontent.com/u/2672043?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Samgu Lee</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=cable8mm" title="Documentation">📖</a></td>
    <td align="center"><a href="http://junwoo.kim"><img src="https://avatars0.githubusercontent.com/u/6765995?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Junwoo Kim</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=dasider41" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/shkimolulo"><img src="https://avatars1.githubusercontent.com/u/45745078?v=4?s=100" width="100px;" alt=""/><br /><sub><b>shkimolulo</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=shkimolulo" title="Documentation">📖</a></td>
    <td align="center"><a href="https://medium.com/@shaul1991"><img src="https://avatars1.githubusercontent.com/u/35905295?v=4?s=100" width="100px;" alt=""/><br /><sub><b>JI HOON KIM</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=shaul1991" title="Documentation">📖</a></td>
    <td align="center"><a href="https://blog.decorus.io"><img src="https://avatars1.githubusercontent.com/u/8151366?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Kazuma Itou</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=decorus-kazuma" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/webmin41"><img src="https://avatars0.githubusercontent.com/u/39331373?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Mingi_Jeon</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=webmin41" title="Documentation">📖</a></td>
    <td align="center"><a href="http://onoffmix.dev"><img src="https://avatars1.githubusercontent.com/u/60088641?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Kim Chanhyung</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=kimchanhyung98" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://github.com/njhyuk"><img src="https://avatars2.githubusercontent.com/u/17216521?v=4?s=100" width="100px;" alt=""/><br /><sub><b>njhyuk</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=njhyuk" title="Documentation">📖</a></td>
    <td align="center"><a href="https://hajaebong.github.io/"><img src="https://avatars0.githubusercontent.com/u/31947247?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Jaebong,Ha</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=hajaebong" title="Documentation">📖</a></td>
    <td align="center"><a href="https://imon.dev"><img src="https://avatars1.githubusercontent.com/u/18586282?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Youchan</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=YouchanChung" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/Jeongwoo-Han"><img src="https://avatars0.githubusercontent.com/u/60590468?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Jeongwoo-Han</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=Jeongwoo-Han" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/wjdgkrud45"><img src="https://avatars3.githubusercontent.com/u/61770473?v=4?s=100" width="100px;" alt=""/><br /><sub><b>wjdgkrud45</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=wjdgkrud45" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/ongyjho"><img src="https://avatars2.githubusercontent.com/u/58408421?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Yeojin Hong</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=ongyjho" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/junho3"><img src="https://avatars3.githubusercontent.com/u/54342973?v=4?s=100" width="100px;" alt=""/><br /><sub><b>junho3</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=junho3" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://github.com/gyoungeunbae"><img src="https://avatars3.githubusercontent.com/u/24830023?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Ola</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=gyoungeunbae" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/JJRomi"><img src="https://avatars1.githubusercontent.com/u/27665422?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Romi</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=JJRomi" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/Hyeongwon"><img src="https://avatars1.githubusercontent.com/u/14510347?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Hyeongwon</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=Hyeongwon" title="Documentation">📖</a></td>
    <td align="center"><a href="https://blog.silnex.kr"><img src="https://avatars2.githubusercontent.com/u/24711610?v=4?s=100" width="100px;" alt=""/><br /><sub><b>silnex</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=SilNex" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/Khon0316"><img src="https://avatars3.githubusercontent.com/u/20215769?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Khon0316</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=Khon0316" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/sangchul-moon"><img src="https://avatars3.githubusercontent.com/u/38758061?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Sangchul Moon</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=sangchul-moon" title="Documentation">📖</a></td>
    <td align="center"><a href="https://gist.github.com/ojongchul/d97a2d5581f271e247a00508b9eb93ee"><img src="https://avatars0.githubusercontent.com/u/25588378?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Oh Jongchul</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=ojongchul" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://github.com/danics7"><img src="https://avatars3.githubusercontent.com/u/5405310?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Chisoo Yang</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=danics7" title="Documentation">📖</a></td>
    <td align="center"><a href="https://papion93.github.io/"><img src="https://avatars0.githubusercontent.com/u/49216709?v=4?s=100" width="100px;" alt=""/><br /><sub><b>PAPION</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=PAPION93" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/DungGi"><img src="https://avatars1.githubusercontent.com/u/57477524?v=4?s=100" width="100px;" alt=""/><br /><sub><b>DungGi</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=DungGi" title="Documentation">📖</a></td>
    <td align="center"><a href="https://z9n.net"><img src="https://avatars1.githubusercontent.com/u/5427199?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Terrorboy</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=Terrorboy" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/JeongJaeSoon"><img src="https://avatars3.githubusercontent.com/u/53788601?v=4?s=100" width="100px;" alt=""/><br /><sub><b>JeongJaeSoon</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=JeongJaeSoon" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/mapd3692"><img src="https://avatars3.githubusercontent.com/u/13864840?v=4?s=100" width="100px;" alt=""/><br /><sub><b>mapd3692</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=mapd3692" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/gommmm"><img src="https://avatars3.githubusercontent.com/u/9735109?v=4?s=100" width="100px;" alt=""/><br /><sub><b>gommmm</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=gommmm" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://github.com/coolhong"><img src="https://avatars2.githubusercontent.com/u/3918019?v=4?s=100" width="100px;" alt=""/><br /><sub><b>YoungsuHong</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=coolhong" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/truekim819"><img src="https://avatars3.githubusercontent.com/u/19642477?v=4?s=100" width="100px;" alt=""/><br /><sub><b>truekim819</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=truekim819" title="Documentation">📖</a></td>
    <td align="center"><a href="https://ucyang.com/"><img src="https://avatars.githubusercontent.com/u/31183209?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Unchun Yang</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=ucyang" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/woongbin"><img src="https://avatars.githubusercontent.com/u/15007903?v=4?s=100" width="100px;" alt=""/><br /><sub><b>sirwoongke</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=woongbin" title="Documentation">📖</a></td>
    <td align="center"><a href="http://dev.epiloum.net"><img src="https://avatars.githubusercontent.com/u/74719609?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Jaewon Kim</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=Epiloum" title="Documentation">📖</a></td>
    <td align="center"><a href="http://pinkward.co.kr"><img src="https://avatars.githubusercontent.com/u/28586768?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Leejungyoung</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=jungyoung" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/misstur"><img src="https://avatars.githubusercontent.com/u/47712114?v=4?s=100" width="100px;" alt=""/><br /><sub><b>misstur</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=misstur" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://github.com/corean"><img src="https://avatars.githubusercontent.com/u/225376?v=4?s=100" width="100px;" alt=""/><br /><sub><b>corean</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=corean" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/dndn0120"><img src="https://avatars.githubusercontent.com/u/20917601?v=4?s=100" width="100px;" alt=""/><br /><sub><b>dndn0120</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=dndn0120" title="Documentation">📖</a></td>
    <td align="center"><a href="https://yuns.space"><img src="https://avatars.githubusercontent.com/u/57185499?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Yun-Hwan Jeon</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=yunh03" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/Laeng"><img src="https://avatars.githubusercontent.com/u/11701767?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Laeng</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=Laeng" title="Documentation">📖</a></td>
    <td align="center"><a href="http://@wondongho.tistory.com"><img src="https://avatars.githubusercontent.com/u/30614857?v=4?s=100" width="100px;" alt=""/><br /><sub><b>DongHo Won</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=DongHoWon" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/wonjun3991"><img src="https://avatars.githubusercontent.com/u/48777603?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Wonjun</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=wonjun3991" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/castell9000"><img src="https://avatars.githubusercontent.com/u/20631577?v=4?s=100" width="100px;" alt=""/><br /><sub><b>JIHWAN BAEK</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=castell9000" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://github.com/N0FreeLunch"><img src="https://avatars.githubusercontent.com/u/47421082?v=4?s=100" width="100px;" alt=""/><br /><sub><b>JJW</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=N0FreeLunch" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/Kyungseo-Park"><img src="https://avatars.githubusercontent.com/u/45850400?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Kyoungseo Park</b></sub></a><br /><a href="https://github.com/laravelkr/docs/commits?author=Kyungseo-Park" title="Documentation">📖</a></td>
  </tr>
</table>

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->

## Acknowledgments

Thanks to [JetBrains](https://www.jetbrains.com) for supporting us with a [free Open Source License](https://www.jetbrains.com/buy/opensource).
