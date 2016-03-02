---
layout: post
section-type: post
title: 웹에서 특정 카메라 소스를 지정해서 사용 할 수 없을까?
category: Category
tags: [ 'issue', 'conflict', 'leapmotion', 'webcam', 'camera', 'javascript', 'MediaStreamTrack', 'getSources' ]
---

[립 모션][1]을 가지고 업무를 하던 중 웹 페이지에서 카메라와 립모션을 같이 사용 할 수 없는 문제가 있어서<br>
해결한 내용을 정리해본다.

---

### 이슈 상황
[립 모션][1] SDK 를 깔고 웹 브라우저에 로드를 한 뒤 립 모션 정상 작동하는 것 확인하고<br>
이후 아래와 같이 카메라를 연동하여 작업을 하려고 하는데 카메라 인식이 안되는 현상 발생하였다.

```javascript
navigator.webkitGetUserMedia({
       video: true 
},
(stream) => { 
	... something when success ...
},
(error) => {
	... something when error ...
});
```

---

### 이슈 원인
[립 모션][1]이 자체적으로 카메라 센서를 가지고 활동을 모니터링 하는데 웹에서 `navigator.webkitGetUserMedia`를 사용하면
브라우저는 하나의 카메라만 인식해서 [립 모션][1]을 인식할 경우 카메라가 작동하지 않는 상황이다. <br>
반대로 카메라를 인식할 경우 [립 모션][1]이 정상작동하지 않는다.

---

### 해결 방법
처음에는 뭔가 브라우저나 [립 모션][1]의 설정이 있을 줄 알았으나 아무리 찾아봐도 그런 정보는 없었다.
그래서 고민 끝에 브라우저에서 현재 설치된 미디어 중 내가 원하는 것을 `Javascript` 레벨에서 선택해서 사용 할 수 없을까? 하고 찾아보다가 [`MediaStreamTrack.getSources`][2] API를 발견했다.
이 [예제][3]를 보면 어떤 용도인지 쉽게 이해할 수 있다.

그래서 기존의 코드를 변경해서 아래와 같이 내가 사용할 소스를 직접 선택해서 `navigator.webkitGetUserMedia` 전달해 주니
원하는대로 [립 모션][1] 과 웹 캠이 정상 작동하는 것을 확인했다.

```javascript

	// 사용가능한 비디오 찾아서 옵션 id 지정
    MediaStreamTrack.getSources((oSourceList) => {
      // 소스중 비디오인 것 찾아서 첫번째거 사용
      var oVideoSource = oSourceList.find((source) => {
        return source.kind === 'video';
      });

      // 없을 경우를 대비한 방어코드 추가
      if(!oVideoSource) {
        return false;
      }

      // 찾아진 비디오 아이디 추출
      var sourceId = oVideoSource.id;

      // 찾아진 비디오로 비디오 엘리먼트 초기화
      if (navigator.webkitGetUserMedia) {
        navigator.webkitGetUserMedia({
          video: {
            optional: [{
              sourceId: sourceId
            }]
          }
        }, (stream) => {
          ... something when success ...
        }, (error) => {
          ... something when error ...
        });
      }
    });
```

---

### 결론
이슈는 [립 모션][1]과의 연동에서 발생했지만 여러개의 카메라를 페이지 하나에서 출력하길 원할때(CCTV 화면 같은..) 원하는 소스를 선택해서
출력하는게 가능할 것 같다. 문제는 브라우저 서포트가 생각보다 많지 않으니 꼭 **지원하는 브라우저**를 확인 후 사용해야 한다.

[1]: https://www.leap-motion.kr/ '립 모션'
[2]: https://developer.mozilla.org/ko/docs/Web/API/MediaStreamTrack 'MediaStreamTrack'
[3]: https://simpl.info/getusermedia/sources/ '예제'