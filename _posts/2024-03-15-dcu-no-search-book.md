---
title: 🖥️ 책 검색이 안되는 이슈
date: 2024-03-15 00:00:01
permalink: /dcu/noSearchBook
categories:
- 트러블슈팅
- 다채움
tag:
- 트러블슈팅
- 다채움
---

# 개요
- 특정 컴퓨터에서 책 찾기 검색이 되지 않는다는 문의.

# 분석
오류가 재현되지 않아 재현되는 컴퓨터에 양해를 구하고 원격을 걸어서 원인을 확인했다.<br/>
먼저 API 문제인가 싶어서 토큰 확인 및 저장에 대해 확인 결과 밀리의 서재 API를 사용하고 있는 다른 페이지들은 잘 되고 있다.<br/>
**크롬, 웨일, 엣지 3개의 브라우저 확인 결과 책 검색만 되지 않고 있다.**<br/>
try, catch로 error가 나면 콘솔에 error을 표시하게 했는데, 콘솔에 오류가 뜨는 것도 아니고 원인이 뚜렷하지 않았다. 
당시 **사용자 네트워크 문제로 생각하고 네트워크 오류에 맞는 대응**을 하기로 했다.

# 방법
## 방법 1.
먼저 고안한 방법은 **status가 200이 아닐 때 리로드**를 시키는 것.<br/>
하지만 너무 불필요한 리로드 인 것 같아서 최후의 방법으로 보류.

## 방법 2.
**status가 200이 아닐 때 토큰 유효성 검사**를 다시 하는 로직을 생각.<br/>
이것 또한 불필요한 서버호출이라고 판단해서 보류했다.

## 방법 3.
**5초 이상 promise 가 pending 시 네트워크가 불안정하니 다시 시도하라는 alert**을 띄우는 것을 생각했고, 
지금 대응하기엔 적당한 방법이라 진행하려고 하는 도중 밀리의 서재 API의 url 이 운영 서버가 아니라 개발 서버로 되어 있는 것을 발견...

## 원인
밀리의 서재 **API의 url 이 운영 서버가 아니라 개발 서버**로 되어 있는 것.<br/>
테스트를 하고 미쳐 못바꾼 것이 화근이 되었다. 변경을 하고 오류가 있던 특정 컴퓨터에서 확인 결과 이상이 없었다.

# 해결
테스트 할 때 마다 매번 바꾸는 것이 효율성이 떨어지고, 위 같은 상황을 방지하고자 현재 접속해있는 url에 따라 API url을 변경 하도록 수정.

```javascript
let serverName = window.location.hostname;
let url;
switch (serverName) {
    case "devnaver.com":
        url = "https://devgoogle.com";
        break;
    case "naver.com":
        url = "https://google.com";
        break;
}
```

# WHY
근데 개발할때는 문제가 없었던 개발서버로는 **왜 검색이 되지 않았을까???**

다른 개발자분들과 위 사건으로 이야기를 해보면서 **가장 일리 있는 추측**으로는<br/>
웹에서 응답대기 시간이 있는데 일정 시간을 넘기면 끊어버린다. 이런걸 **Time out** 이라고 한다.<br/>
'프로그램이 특정 시간 내에 성공적으로 수행되지 않아서 진행이 자동적으로 중단되는 것' 이라는 의미가 있고, 응답을 무한정 기다릴 수 없기 때문에 
중단을 시키는 것이다.

통상 개발 서버는 용량이 작아서 낮은 스펙으로 셋팅을 하게 되는데 불러오는 데이터가 많으면 느려지게 되고 **응답 대기시간을 초과하게 되면서** 
타임아웃이 일어나 **아무런 반응을 하지 않는 것 처럼** 보여지는 것 같다.

# 결과
API의 url을 운영서버로 변경하고 잘된다는 답변을 받았고, 전체적으로 **약 10%** 정도 검색 속도 및 기능이 향상이 되었다.<br/>
이런 사소한 실수는 있으면 안되는데, 다음에 코드를 작성할때는 조금 더 효율성 있는 코드를 생각하고 작성 해야겠다.