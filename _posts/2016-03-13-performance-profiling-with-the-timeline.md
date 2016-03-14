---
layout: post
section-type: post
title: Performance Profiling with the Timeline
category: Category
tags: [ 'chrome', 'performance', 'tools', 'timeline', 'profiling' ]
---

[이전 포스팅][1]에서 크롬 개발자 도구 중 타임라인 기능에 대해서 살펴보았는데 이번에는 타임라인 기능을 가지고
`샘플 페이지에서 리소스에 따라서 타임라인이 어떻게 변화하는지 보고`, `실제 운영되고 있는 사이트를 골라서 분석해 보는 것을 정리`해 보려고 한다.

--- 

## 성능 분석 준비하기

[이전 포스팅][1]에서 몇가지 타임라인 캡쳐 팁에 대해서 언급한 적이 있을 것이다. 

그 중에 `브라우저 캐시 비활성화` 및 `익스텐션 비활성화`를 먼저 하고 시작하자. 

왜냐하면 브라우저 캐시를 타게 된다면 각 리소스들이 다운로드 받는 시간이 정상적으로 캡쳐되지 않을 것이고
크롬 익스텐션이 활성화 된 상태라면 웹 페이지에서 사용하는 것 외에 익스텐션이 사용하는 라이브러리, 이벤트 등 내가 분석할
필요가 없는 것들까지 같이 분석이 되어 혼란이 가중되기 때문이다.

일단 크롬 개발자 도구부터 열자.

---

### 익스텐션 비활성화
기본적으로 크롬을 사용하는 사람이라면 익스텐션이 상당히 많이 깔려 있을 것이다. 유용하기도 하고 편리한 것들도 많다.
하지만 설정에는 별도로 전체 비활성화 하는 기능이 없고 단순히 하나씩 사용함/사용안함 처리밖에 할 수 없는 것 같다.(혹시 다른 방법이 있다면 댓글 부탁..)

그래서 나는 크롬의 기능 중 시크릿 창을 활용한다.
 
먼저 아래와 같이 크롬 확장프로그램 관리 페이지로 접속한다.

![확장프로그램 관리](/img/post_resource/2016-03-13/connect-extension-manager.png "확장프로그램 관리")

접속해 보면 아래와 같이 설치된 확장 프로그램들이 나오는데 여기서 중요한 건 `시크릿 모드에서 허용`이라는 체크박스다. 모두 체크해제 한다.
보통 개발할때는 시크릿 모드를 켤일도 많이 없거니와 시크릿 모드에서 확장 프로그램을 모두 사용해야 하는 것도 아니다.

![확장프로그램 페이지](/img/post_resource/2016-03-13/extension-manager.png "확장프로그램 페이지")

이제 시크릿 모드로 크롬을 열면 된다. 시크릿 모드로 여는 방법은 크롬 아이콘에서 오른쪽 클릭하면 아래와 같이 `새 시크릿 창`이라는 메뉴가
별도로 있다.(윈도우도 마찬가지로 알고 있다.)

![새 시크릿 창 열기](/img/post_resource/2016-03-13/new-secret-window.png "새 시크릿 창 열기")

크롬이 시크릿 창으로 열리게 되면 아래와 같이 익스텐션 없이 모든 설정이 깔끔하게 뜨는 것을 볼 수 있다. 그리고 우측 상단에 시크릿창을 표시하는
탐정 이미지도 표시되어 있는 것을 볼 수 있다.

![시크릿 창](/img/post_resource/2016-03-13/secret-window.png "시크릿 창")

---

### 브라우저 캐시 비활성화
보통 *Settings > General > Disable cache* 를 비활성화 하라고 하는데 나같은 경우는 설정까지 들어가기 귀찮아서
네트워크 패널을 활용한다. 

아래 이미지 처럼 네트워크 패널에 Disable Cache 체크박스가 있는데 이걸 체크하면 설정에서 체크한 것과 동일하다.
실제 설정에 들어가 보면 체크되어 있는 것도 확인 할 수 있다.

![Dsiable Cache](/img/post_resource/2016-03-13/check-disable-cache.png "Dsiable Cache")

---

## 리소스 추가에 따른 타임라인의 변화
이제 타임라인을 사용할 준비는 끝났다. 본격적인 웹 페이지 성능 분석에 들어가기 전에 기본적으로 리소스에 따라 타임라인이
어떻게 변하는지 부터 알아보자. 테스트는 빈 HTML 파일 부터 시작해서 HTML, CSS, JS, Image 리소스를 추가하면서
타임라인이 어떻게 변화하는지 알아보는게 목적이다. 이 구조를 알아야 다른 사이트를 분석했을때 이런 부분을 어떻게 개선해야
성능이 이끌어낼 수 있는지 알 수 있기 때문이다.

### 단순 텍스트
아래와 같이 단순히 Hello World 를 본문에 넣고 타임라인을 돌려보았다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>리소스에 따른 타임라인 변화</title>
</head>
<body>
Hello World!
</body>
</html>
```

아래는 위 코드를 타임라인을 돌려본 결과이다.

![figure-1](/example/2016-03-13/img/figure-1.png "figure-1")

당연히 별게 없으니 아무것도 없을 줄 알았으나 해당 페이지가 그려지기 위한 이벤트들이 캡쳐된 것을 확인 할 수 있다.
하나씩 설명을 해주고 싶지만 진짜 별것도 없고, 아무 코드가 없어도 기본적으로 생기는 이벤트들이기 때문에 우리가 성능 개선할 여지도 없다.
재미난건 우리가 알고 있는 이벤트들(readystatechange, DOMContentLoaded, load, pageshow)도 같이 캡쳐 된다.
진짜 별거 없긴 하지만 샘플 페이지 하나 만들어서 테스트로 한번 돌려보길 바란다.

---

### CSS(Style)
이제 간단한 스타일을 추가해 보았다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>리소스에 따른 타임라인 변화</title>

  <style>
    * {
      font-size:100px;
      color: white;
      background-color:cadetblue;
    }
  </style>
</head>
<body>
Hello World!
</body>
</html>
```

![figure-2](/example/2016-03-13/img/figure-2.png "figure-2")

기존 타임라인과 달라진게 보이는가? 오른쪽 하단에 녹색 바 같은 것이 추가된 것이 보일 것이다. 해당 이벤트를 클릭해보면 아래와 같이 나온다.

![figure-3](/example/2016-03-13/img/figure-3.png "figure-3")

그리고 타임라인 상에는 미미해서 눈에 띄지 않아도 스타일 계산하는 시간이 늘어났을 것이다. 

Rasterize가 무엇인지 당연히 의미가 있겠지만 이 포스팅에서는 불필요하다.
중요한건 단 하나! `스타일 하나를 추가하더라도 크롬 브라우저는 무언가 추가 작업을 한다.`

또 다른 예제를 보자.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>리소스에 따른 타임라인 변화</title>
  <link rel="stylesheet" href="https://ssl.pstatic.net/sstatic/search/pc/2016/css/content_0303.css" class="href">
</head>
<body>
Hello World!
</body>
</html>
```

이번에는 아무 css 나 그냥 가져다가 링크를 걸어 보았다.
위 예제를 보면 스타일이 많이 추가 되었으니 연산도 많이 하겠지? 라는 생각을 했다.

![figure-4](/example/2016-03-13/img/figure-4.png "figure-4")

이번에는 파란선(DOMContentLoaded)이벤트 이후에 Parse Stylesheet 라는 이벤트가 추가되었다. 그리고 해당 이벤트는 녹색선(First Paint)이벤트 이전에
발생한 것을 볼 수 있다. 이렇게 링크로 스타일 시트를 추가할 경우 위와 같이 별도의 Parse Stylesheet 이벤트가 발생하는 것을 알 수 있다.

아마 추측컨데 `head` 안에 링크를 걸어서 그렇지 `body`안에 링크를 걸었다면 녹색선(First Paint) 이후에 이벤트가 발생할 것이다.

![figure-5](/example/2016-03-13/img/figure-5.png "figure-5")

역시 그렇다. 이것이 의미하는 바는 스타일을 body 에 선언할 경우 화면이 그려진 이후에 스타일이 처리되고 그 처리된 결과가 다시 `DOM`에 반영 될 것이라는 것이다.
간혹 사이트 보면 화면이 깨졌다가 점차적으로 스타일을 잡아가는 경우가 있는데 딱 이 상황일 것이라 알 수 있다.

왜 스타일을 `body`에 넣으면 안되는지 확실히 확인 하였다. 성능 개선한다고 무조건 리소스를 밖으로 빼지 말자.

---

### JS(Javascript)

이제 스크립트를 넣어보자. 시간이 오래 걸리도록 단순히 for 문으로 시간을 지연 시켰다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>리소스에 따른 타임라인 변화</title>
</head>
<body>
<script>
  for(var i = 0; i < 1000; i++) {
    console.log(i);
  }
</script>

Hello World!

<script>
  for(var i = 0; i < 1000; i++) {
    console.log(i);
  }
</script>
</body>
</html>
```

![figure-6](/example/2016-03-13/img/figure-6.png "figure-6")

재미난 현상이 보인다. Parse HTML 아래로 Evaluate Script 가 있는게 보일 것이다.

간단히 말로 풀어보면 `body`를 처리하고 있는데 중간에 `script`태그를 만나 무언가 별도의 처리를 한 다음에 이후 Parse HTML 을 했다는 소리다.
중간에 들어간 스크립트로 인해서 파란선, 빨간선, 녹색선 전부 뒤로 밀려났다. 

이게 무엇을 뜻할까? 스크립트가 처리되기 전까지는 화면에 아무것도 보이지 않는 다는 소리다. 가장 피해야할 것 중 하나이다.
성능 관련해서 하지 말라는 것들 중 하나가 `body` 태그 안에 인라인 스크립트를 넣지 말라는게 이 이유인 것이다.

이 포스팅에서 이 부분의 성능 개선 방법을 논할건 아니지만 간단한 키워드를 드리면 `Defer Loading`, `Lazy Loading`, `setTimeout` 등 많은 방법이 있다.

다른 예제를 보자.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>리소스에 따른 타임라인 변화</title>
  <script src="//code.jquery.com/jquery-1.12.0.min.js"></script>
</head>
<body>
Hello World!
</body>
</html>
```

특별한 것 없이 흔히들 사용하는 방식으로 `head`에 `jQuery` 라이브러리를 추가하였다.

![figure-7](/example/2016-03-13/img/figure-7.png "figure-7")

기존에 로드 시간이 100ms 도 안걸리던 페이지가 갑자기 600ms 까지 치솟았다. 그리고 Flame Chart 영역을 봐도
중간 중간 빈 곳이 많다. 

맞다. 예상하는대로 `jQuery` 라이브러리 받는 시간이다. 이렇게 사용하는 라이브러리를 `head`에 넣고 사용하면 해당 리소스가 준비되기 전에
많은 시간을 소모한다. 특히 jQuery 는 즉시 실행함수로 묶여 있기 때문에 `head`에 넣게 되면 치명적이다. 이 차이는 모바일과 같이 네트워크가 느릴 수록 
기하급수적으로 늘어난다.

그렇기 때문에 보통 스크립트 태그는 `body` 하단에 넣거나 위에서 언급했던 `Defer Loading`, `Lazy Loading`과 같은 기법을 많이 사용하는게 좋다.
그리고 당연한 얘기겠지만 특정 페이지에서 필요 없을 경우에는 아예 스크립트 로딩 부분을 빼주는게 가장 좋다.

---

### Image

이제 이미지를 넣어보고 타임라인이 어떻게 변화하는지 살펴보자.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>리소스에 따른 타임라인 변화</title>
</head>
<body>
<div>
  <img src="https://images.unsplash.com/photo-1454817481404-7e84c1b73b4a?ixlib=rb-0.3.5&q=80&fm=jpg&crop=entropy&s=5f4cef962c8ddea0da93e59299350831" alt="" width="100">
  <img src="https://images.unsplash.com/photo-1454982523318-4b6396f39d3a?crop=entropy&dpr=2&fit=crop&fm=jpg&h=825&ixjsv=2.1.0&ixlib=rb-0.3.5&q=50&w=1450" alt="" width="100">
</div>
<div>
  Hello World!<br>
</div>
</body>
</html>
```

먼가 엄청 복잡한 일이 일어났다. 하나씩 살펴보자. 

![figure-8](/example/2016-03-13/img/figure-8.png "figure-8")

일단 파란색, 녹색 이벤트는 이미 30ms 근처에서 발생했다. 

그리고 스크린샷을 보면 `Hello World!` 텍스트는 이미 출력되어 있는 것을 볼 수 있다.

그 이후에 메인 스레드 부분에 파란 줄이 쳐져 있고 그 아래 녹색 블럭 들이 보인다. 
파란색 이벤트를 눌러보면 `Receive Data`라고 확인 할 수 있는데 이미지를 다운로드 과정이고 그 아래 중간 중간 녹색 블럭은 화면에 이미지를 렌더링 하는 이벤트인 것이다.
 
이후 최종적으로 641ms 지점에서 빨간선(Load)이벤트가 발생한 것을 확인 할 수 있다.

여기서 알 수 있는 것은 실제 사용자는 이미지가 로드 되는 대로 화면에 그려지는 이미지들을 바로 확인 할 수 있지만(AFT) 보통 성능 측정을 하면
빨간선(Load) 기준으로 시간을 측정하기 때문에 이 페이지의 경우 로딩 속도는 641ms 가 되는 것이다.

그럼 실제 수치상의 로딩 속도를 줄이려면 어떻게 해야 할까? 라는 질문에 접근 할 수 있는 것이다. 말그대로 이 부분이 이미지 관련된 개선 포인트가 되는 것이다.

이 부분도 마찬가지로 보통 `Lazy Loading` 처리를 하여 onLoad 타임을 앞당기는 방법을 많이 취한다.

---

여기까지 해서 리소스들에 따라서 타임라인이 어떻게 변화하는지 살펴보았는데 단순히 이미지/JS/CSS 정도만 테스트 해봤는데
다른 리소스도 마찬가지다. 페이지에 무언가 추가를 하면 그만큼의 비용이 소모되는 것을 알 수 있다. 

그래서 타임라인을 보면서 어떤 부분이 불필요하고, 나중에 필요한지, 그리고 어떤 위치에 리소스를 놔야 최적의 결과를 가져갈 수 있는지
프로파일링이 필요한 것이다.

이제 본격적으로 웹 페이지 성능 분석을 해보자. 타임라인만 보고 어떤 정보를 확인 할 수 있는지, 그리고 그 정보들이
어떤 것을 의미하는지 `주관적으로 생각하는 것`들을 풀어낸다고 볼 생각이다. 분석하는 사람마다 어느 부분을 중점적으로 보는지
다르기 때문에 참고만 하면 좋겠다. 

그리고 가끔 `모바일 웹 페이지는 어떻게 분석하면 좋나요? PC 에서 분석하면 정확하지 않을거 같은데..`
라는 말을 하는 분들을 만나면 저는 전혀 상관 없다고 말씀드리고 싶다. 왜냐면 어차피 성능이라는게 상대적이기 때문에 PC 에서 분석하더라도
하드웨어 성능 및 네트워크 속도에 따른 차이는 있을수 있겠지만 `내가 분석하고 개선된 부분은 동일하게 모바일도 개선`되기 때문이다.

---

## Mobile Facebook
아래는 모바일 페이스북 페이지를 타임라인 패널에서 본 결과이다. 일단 간접 출현한 분들은 양해를 부탁한다..

![타임라인 패널](/img/post_resource/2016-03-13/facebook-timeline.png "타임라인 패널")

---

### First Paint

![figure-1](/img/post_resource/2016-03-13/facebook-figure-1.png "figure-1")

일단 녹색선(First Paint)이 보인다. 600ms 이전에 사용자가 볼 수 있는 화면이 그려지기 시작한 것을 확인 할 수 있고 그 위에 보이는 스크린 샷을 보면
실제로 상단 파란색 영역부터 그려지는 것을 볼 수 있다.(참고로 그 이전에 보이는 화면은 아마 새로고침 전의 화면까지 캡쳐된게 아닐까 생각이 든다.)

---

### AFT(Above the Fold Time)
나는 보통 사용자 입장에서 언제 완전히 로드된 시간을 보는지가 가장 중요하다고 생각한다. 그것을 보통 AFT 라고 하는데 먼저 AFT의 정의부터 알아보자.

구글의 Jake Brutlag의 말을 인용해 보면:

> AFT was defined as the moment when content “above the fold” (aka “what you see in your browser window”) stops changing and reaches its final state.

능력이 미천하여 제대로 해석은 안되지만 간단히 말해:

> 사용자가 브라우저에서 볼 때 컨텐츠가 변경되지 않고 상태가 변경되지 않으면 AFT 라고 정의한단다.

그럼 한번 찾아보자.

![figure-2](/img/post_resource/2016-03-13/facebook-figure-2.png "figure-2")

위 이미지를 보면 거의 변화가 없는 최종 스크린샷이 약 1700ms 근처인 것을 확인 할 수 있다. 그렇다면 해당 페이지의 AFT 타임은 1700ms 인 것이다.

근데 특이한 건 그 이후로도 지속적으로 캡쳐가 일어나고 있는데 `알 수도 있는 사람` 부분에서 동그라미가 계속 돌아가기 때문에 끝나지 않는 것이다.

---

### DOMContentLoaded Event & Load Event

다음은 실제 성능 측정에서 흔히 나오는 DOMContentLoaded, Load 이벤트가 발생한 시간이다. 
사실 이 부분은 네트워크 탭에서 측정해 보면 아래와 화면 아랫 부분에 정리해서 잘 노출된다. 

![figure-4](/img/post_resource/2016-03-13/facebook-figure-4.png "figure-4")

굳이 타임라인에서 볼 필요는 없지만 한번 확인해 보자. 

![figure-3](/img/post_resource/2016-03-13/facebook-figure-3.png "figure-3")

위 이미지를 보면 1200ms 이전에 빨간선과 파란선이 그려져 있는 것을 볼 수 있다.

그러면 DOMContentLoaded 와 Load 이벤트는 1100ms 정도에 발생한 것을 알 수 있다.

---

### 개선포인트 찾기

솔직히 페이스북 정도 되는데서 개선포인트를 찾기는 쉽지 않을 것이다.
살짝 억지스러운 부분도 있을 수 있겠지만 그래도 일단 찾아 보자. 

![figure-5](/img/post_resource/2016-03-13/facebook-figure-5.png "figure-5")

위 이미지를 보면 이전에 미리 알아본 것 중에 `body`에 스크립트를 넣었을 때 발생하는 현상이 존재하는 것을 알 수 있다.
실제 저 로직이 어떤 건지는 모르겠지만 저 부분을 개선할 부분이 있을 것 같다.

![figure-6](/img/post_resource/2016-03-13/facebook-figure-6.png "figure-6")

다음으로 넘어가 보자. 

Flame Chart에서 약 100ms 정도 차지하고 있는 스크립트가 보인다.
확인해보니 load 이벤트에 스크립트 작업을 걸어놓은 것 같다.
 
좀 더 찾아 내려가 보자.

![figure-7](/img/post_resource/2016-03-13/facebook-figure-7.png "figure-7")

호출 스택을 내려가다 보니 왠지 `onload`, `_complete` 같은게 나오는거 보니 ajax 통신으로 뭔가 받아오고
그 이후 처리를 위해 이벤트를 걸어놓은 것 같다.

디테일 영역에 나온 파일 링크(`mfCiZyg41E1.js:formatted:24`)를 클릭해 보자.

![figure-8](/img/post_resource/2016-03-13/facebook-figure-8.png "figure-8")

클릭해 보니 위와 같이 해당 소스가 열리고 어떤 스크립트인지 보인다.

아래 코드를 보니 추측대로 무언가 요청하고 요청 한 뒤에 콜백을 실행하고 있다. 
해당 코드가 어떤 로직인지는 모르겠지만 코드 개발자일 경우 이 부분이 개선이 필요한지, 아니면 그대로 유지해야 할지 알 수 있을 것이다.

```javascript
q.load(v.coreResources, function() {
            j.guard(function() {
                if (v.clearMCache)
                    k.clear();
                if (v.onload)
                    new Function(v.onload)();
                if (v.onafterload)
                    new Function(v.onafterload)();
                new r().handle(v.serverJSData);
                s.invoke('m:root:render');
            }, 'onload')();
        });
```

---

모바일 페이스북 사이트를 타임라인으로 간단히 분석해 봤다.

좀 더 디테일한 분석을 할 수 있겠지만 개인적인 생각으로 불필요한 것 같다. 기본적으로 [YSlow][3] 나 [PageSpeed][4]로
성능 체크를 하고 넘어왔다면 결국 어떤 스크립트 부분이 시간을 많이 잡아먹고 개선이 필요한지 찾을 수만 있어도 현업에서는
충분히 활용 할 수 있다고 본다.(메모리나 FPS 까지 살펴본 적은 아직 현업에서는 없어서 그다지 필요한지는 모르겠다..)

---

별 내용도 없는 것 같은데 엄청 길어진 것 같다.

마지막으로 이번 포스팅을 정리하면서 하고 싶은 말이다.

> 성능 개선은 시간이 있을때 하는 것보다 틈틈히 하는 것이 좋다.(항상 시간은 부족하다)

> 타임라인에서 특정 리소스가 시간을 잡아먹고 있다고 해서 무조건 개선해야 하는건 아니다. 상황에 따라 합리적으로 개선하자.

> 성능은 누구 한명이 관심 가져서 될 문제가 아니다. 최소한 성능 지표를 읽는 능력은 키워놓자.

다음 포스팅은 자동 캡쳐가 아닌 내가 원하는 타이밍에 캡쳐해서 어떤 것에 활용 할 수 있는지 포스팅 해봐야 겠다.

---

### 관련포스팅
[Chrome DevTools Time Line Panel Overview][1]

[1]: https://kimshinelove.github.io/category/2016/03/12/chrome-developtools-timeline-1.html 'CHROME DEVTOOLS TIME LINE PANEL OVERVIEW'
[3]: http://yslow.org/ 'YSLOW'
[4]: https://developers.google.com/speed/pagespeed/ 'PageSpeed'