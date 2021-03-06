---
layout: post
section-type: post
title: Chrome DevTools Time Line Panel Overview
category: Category
tags: [ 'chrome', 'performance', 'tools', 'timeline' ]
---

기본적으로 웹 서비스를 개발하고 나면 [YSlow][3] 나 [PageSpeed][4] 를 활용하여 기본적인 성능 측정 및 개선을 하고 
이후 크롬 개발자 도구의 네트워크 탭을 보면서 불필요한 리퀘스트나 불필요하게 큰 리소스를 정리하는 방법을 사용했다. 

그런데 얼마전에 개발한 프로젝트에서는 기본적인 성능 측정 & 개선을 했음에도 불구하고 
페이지가 렌더링 되는 타이밍이 느린 현상이 발견되어 좀 더 디테일한 성능 분석이 필요하게 되었다.

내 경우 크롬 타임라인 도구가 있는 것은 알고 있었지만 성능 분석에 어떤식으로 활용해야 할지 몰라서
실제 업무에 많이 활용하지 않았는데 이번 기회에 깊이는 아니더라도 내가 `업무에 활용할 정도만` 알아보기 위해 정리해 본다.

그 첫번째로 타임라인 패널에 어떤 것들이 있는지 살펴보자. 기본적으로 [구글 개발자 사이트][1]에 나온 내용을 일부 정리하고 있으니 원문을 보고 싶으면 [구글 개발자 사이트][1]를 살펴보자.

---

### 타임라인 패널?

일단 타임라인 패널이 어떤 것인지 부터 알아보자.

![타임라인 패널](/img/post_resource/2016-03-12/overview.png "타임라인 패널")

개발자 도구에서 위 이미지와 같이 타임라인 도구를 본적이 있을 것이다.
일단 타임라인 도구를 [구글 개발자 사이트][1]에서는 공식적으로 아래와 같이 설명하고 있다.

> Use the Chrome DevTools Timeline panel to record and analyze all the activity in your application as it runs.
> It's the best place to start investigating perceived performance issues in your application.

> 당신의 애플리케이션이 실행되는 모든 활동을 녹화하고 분석할때 크롬 개발자 도구의 타임라인 패널을 사용해라. 이것은 당신의 애플리케이션 안에 성능 이슈를 이해하기 시작하기 좋다.

그렇다. 성능 분석이 필요할 경우 크롬 개발자 도구의 타임라인 패널을 사용하면 보다 디테일한 성능을 측정할 수 있다는 것이다.

---

### 타임라인 패널 살펴보기

#### Controls Pane
캡쳐 시작/캡쳐 정지/캡쳐하고 싶은 필터링 설정 등의 작업을 할 수 있는 영역

![컨트롤 영역](/img/post_resource/2016-03-12/control_pane.png "컨트롤 영역")

나 같은 경우는 보통 스크립트 전부 선택 한 후 결과를 하나씩 살펴보고 있다. 너무 정보량이 많을 경우 이 영역에서
조절해 가면서 필요한 부분만 필터링 할 수 있다.

---

#### Overview
페이지 성능에 대해서 전반적으로 살펴볼 수 있는 영역. 

![Overview 영역](/img/post_resource/2016-03-12/overview_pane.png "Overview 영역")

좀 더 살펴보면 위에서부터 순서대로 아래와 같이 영역이 표시된다.

##### FPS
초당 프레임을 시각화한 영역으로 녹색바가 높을 수록 FPS 가 높다는 것을 표시한다. 그리고 빨간 박스는 [jank][2] (16 ms 내에 작업 처리가 안된 프레임)가 발생한 프레임을 표시한다.


##### CPU
CPU 사용률을 보여준다. 여기에 표시되는 영역 그래프는 리소스 타입별로 CPU 를 얼마나 소모하는지 보여준다.

##### NET
각각의 컬러를 가진 바는 리소스를 표시한다. 긴 바는 리소스를 받는데 오래 걸린 것을 의미한다. 바에서 옅은 컬러의 부분은 네트워크 waiting time 을 표시하고, 진한 부분은 실제 리소스를 전송 받은 시간을 표시한다.

* HTML: <span class='blue'>파란색</span>
* 스크립트: <span class='yellow'>노란색</span>
* CSS: <span class='purple'>보라색</span>
* 미디어: <span class='green'>녹색</span>
* 그 외: <span class='gray'>회색</span>


##### ScreenShot
[구글 개발자 사이트][1]에는 설명이 없는 것으로 보아 새로 추가된 기능인듯 한데 시간에 흐름에 따른 스크린샷을 표시해 주는 영역도 새로 생겼다!

---

#### Flame Chart
CPU 스택에 대해서 시각화 해서 보여주는 영역으로 개인적으로 실제 성능 분석시 가장 중요한 영역이라 생각한다.
그 이유는 어떤 이벤트가 얼마나 걸리는지 한눈에 파악이 가능하고 성능 개선 포인트를 찾기 쉽게 도와주기 때문이다.

![Flame Chart 영역](/img/post_resource/2016-03-12/flame_pane.png "Flame Chart 영역")

Flame Chart 에는 3개의 라인이 있는데 각각 아래와 같다.

* 파란선: DOMContentLoaded 이벤트가 발생한 시점.
* 녹색선: 첫번째 페인트가 시작된 영역.
* 빨간선: load 이벤트가 발생한 시점을 표시한다.

그리고 컬러풀(?)한 많은 이벤트들이 있는데 가장 위에 있는 이벤트 들이 메인 쓰레드에서 실행되는 이벤트고 그 아래로 갈 수록 특정 이벤트 밑에 속한 자식 이벤트들을 표시한다. 
각 색상들은 아래 `Details`에서 다시 설명하겠지만 각 색상이 웹 페이지에서 일어나는 이벤트들을 표시한다. 이 이벤트를 하나씩 클릭해 보면 `Details` 영역에 상세 설명이 보여지는 것을 확인 할 수 있다.

간단히 가장 넓은 영역을 차지하고 있는 이벤트가 성능 개선 포인트 최우선 타겟이라고 보면 된다. 

---

#### Memory Pane
Memory 사용량 그래프를 보여주는 영역.(새로 추가된 기능)

![Memory 영역](/img/post_resource/2016-03-12/memory_pane.png "Memory 영역")

---

#### Details
Frame Chart에서 이벤트를 클릭할 경우 해당 이벤트에 대한 디테일한 정보를 보여주는 영역

![Details 영역](/img/post_resource/2016-03-12/Details.png "Details 영역")

---

### 타임라인 패널 기본 사용 방법

#### 타임라인 캡쳐하기

타임라인을 캡쳐하기 위한 방법은 크게 두가지가 있다.

##### 자동 캡쳐
크롬 개발자 도구를 열어놓고 타임라인 패널을 선택한 상태에서 페이지를 리로드 하면 자동으로 캡쳐가 시작된다.

##### 수동 캡쳐
타임라인 패널의 녹화 버튼을 클릭하면 빨간색으로 바뀌는데 이때부터 웹 페이지에서 일어나는 모든 상황이 캡쳐가 된다.
녹화를 마치고 싶을때는 빨간 버튼을 다시 누르면 캡쳐된 모든 상황이 분석되어 타임라인에 표시되게 된다.(단축키: 커맨드 + E or 윈도우키 + E)

캡쳐할때 팁으로 아래와 같이 가이드하고 있다.

* 분석하기 쉽게 가능한 짧게 캡쳐
* 캡쳐하는 도중 불필요한 인터렉션 하지 말 것
* 브라우저 캐시 비활성화
* 불필요한 익스텐션 비활성화

---

#### 이벤트의 디테일 정보 보기

캡쳐가 완료되면 타임라인의 Flame Chart 영역에 수 많은 이벤트 들이 캡쳐되어 있을텐데 그 것들을 클릭하면 Details 영역에
해당 이벤트의 상세 정보가 아래와 같이 출력된다.(이벤트 종류에 따라 출력되는 방식도 조금씩 다르지만 큰 구조는 동일하다.)

![Details 샘플](/img/post_resource/2016-03-12/detail_sample.png "Details 샘플")

##### Type
해당 이벤트가 HTML Parse, JS Frame, Function Call 등 어떤 종류의 이벤트인지 표시

##### Total Time
해당 이벤트가 수행되는데 걸린 시간이 ms 로 표시된다. 성능 개선의 궁극적인 목적은 이 시간을 최소화 하는 것이므로
가장 오래 걸린 이벤트 부터 분석을 하면 좋다.

##### Self Time
해당 이벤트에서 자기 자신이 수행된 시간이다. 이 시간이 적으면 적을수록 해당 이벤트 내부의 다른 이벤트들이 그 외 나머지 시간이 사용했다는 것을 의미한다.
Self Time 적게 걸렸다면 그 내부에 있는 콜 스택중 많이 걸리는 것을 찾아서 개선해야 한다는 소리다. 반대로 많이 걸렸다면 해당 이벤트 자체가 오래 걸린 것을 의미한다.

##### Location
해당 이벤트가 발생한 문서를 찾아서 표시해준다. 링크를 클릭하면 해당 파일이 Source 탭으로 열려서 디버깅이 쉽게 도와주므로 많이 사용하게 된다.

##### Stack trace
간단히 호출 스택을 추적해서 보여준다. Location 과 마찬가지로 클릭하여 바로 해당 파일의 라인으로 이동할 수 있다.

##### Warning
이 부분은 상당히 유용한 정보인데 미처 챙기지 못한 위험한 상황에 대한 경고를 보여주고, 개발 가이드가 있을 경우 링크까지 제공하므로
해당 가이드를 보고 개선이 가능하게 도와준다.

##### Aggregated Time
해당 이벤트가 전체 걸린 시간을 어떤 작업에 시간이 걸렸는지 분리해서 보여준다. 샘플의 경우 스크립트 처리하는데 34.46ms 가 걸렸고 화면 렌더링 처리에 1.02ms 가 걸린 것을 보여주고 있다.

그 외에 여러 속성들이 있을 수 있는데 위 정보만 알고 있어도 분석하는데 큰 무리는 없을 것이다.

--- 

여기까지 크롬 개발자 도구의 타임라인 패널에 대한 각 부분을 살펴 보았다.<br>
[다음 포스팅][5]은 내가 업무를 할때 타임라인을 보면서 어떻게 성능 분석을 하는지 포스팅을 하도록 하겠다.

---

### 관련포스팅
[Performance Profiling with the Timeline][5]
 
[1]: https://developers.google.com/web/tools/chrome-devtools/profile/evaluate-performance/timeline-tool?hl=en 'How to Use the Timeline Tool'
[2]: https://developers.google.com/web/fundamentals/performance/rendering/ 'jank'
[3]: http://yslow.org/ 'YSLOW'
[4]: https://developers.google.com/speed/pagespeed/ 'PageSpeed'
[5]: https://kimshinelove.github.io/category/2016/03/13/performance-profiling-with-the-timeline.html 'Performance Profiling with the Timeline'