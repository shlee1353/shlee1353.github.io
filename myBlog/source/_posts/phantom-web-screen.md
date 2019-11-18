---
title: PhantomJS 웹 스크린 캡쳐기능 사용
date: 2019-07-11 11:47:10
categories:
- etc
tags:
---

![](/image/phantom-web-screen/1.png)

PhamtomJS는 헤드리스 브라우저(사용자 인터페이스 미제공)이며 웹페이지 자동화에 사용됩니다. 브라우저(IE, Chrome, Safari, etc.) 화면없이 자바스크립트 API를 통해 웹을 컨트롤 할 수 있는 강력한 도구입니다. 오늘은, 팬텀JS의 다양한 기능 중 스크린 캡쳐에 대해 알아 보도록 하겠습니다. 서비스 개발 시 스크린 캡쳐는 왜 필요한가? 에 대한 답변은 아래 링크 참고 하도록 하겠습니다.

[스크린 캡쳐 활용 예시](https://m.search.naver.com/search.naver?query=%EC%8B%9D%EB%8B%B9&where=m&sm=mtp_hty)

![](/image/phantom-web-screen/2.png)

지도영역 안에 들어간 A, B, C 마커가 표시된 지도는 png 이미지 파일입니다. 사용자가 화면 접속시 서버에서 네이버 지도와 마커를 사용해서 화면을 만들어 주고 필요한 영역만큼만 캡쳐를 해서 background-image 속성에 추가하는 과정을 거치게됩니다. 이렇게, 팬텀JS를 활용하면 브라우저 화면없이 스크린캡쳐가 가능합니다.

## 팬텀JS 맛보기
팬텀JS는 강력한 기능을 가지고 있지만 사용하기가 매우 용이합니다. 다양한 설치 방법이 있지만, brew를 통해 단 한 줄로 설치할 수 있습니다.

```js
$ brew install phantomjs
```

글로벌로 설치되었기 때문에 Node.js처럼 어디서든 명령어 실행이 가능합니다. 우선, 빈 폴더에 test.js 파일을 만든 뒤 아래와 같이 코드를 작성합니다.

```js
// test.js 파일
var system = require('system');
console.log(system.args[0]); // test.js
console.log(system.args[1]); // arg1
console.log(system.args[2]); // arg2
console.log(system.args[3]); // arg3
```

커맨드 라인에 $ phantomjs test.js arg1 arg2 arg2를 입력한 후 터미널창을 확인하면 test.js파일이 실행된 것을 알 수 있습니다. 간단한 테스트를 마쳤으니, 스크린 캡쳐 기능을 사용하도록 하겠습니다. capture.js 파일을 생성 한 뒤 아래 코드를 복사 후 붙여넣습니다.

```js
// capture.js 파일
var page = require('webpage').create();
var t0 = performance.now();
var t1 = 0;
page.viewportSize = { width: 1525, height: 1240 };
page.open('
https://www.naver.com/', function () {
    page.render('test.png');
    t1 = performance.now();
    console.log("Call to doSomething took " + (t1 - t0) + " milliseconds.")
    phantom.exit();
});
```

첫 번째 행에서 webpage 모듈을 불러와서 viewportSize, open, render 메소드를 순차적으로 사용해서 최종적으로 test.png 파일을 생성하는 과정입니다. 이 외에도, 팬텀JS를 기반으로 Jasmine, QUnit, Mocha 등을 활용한 기능 테스트 및 네트워크 성능 분석 자동화(YSlow, Jenkins)가 가능하며, SVG, Canvas등의 웹컨텐츠도 캡쳐할 수 있습니다.