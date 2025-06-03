---
title: 🔀 CSR, SOP, CORS
date: 2024-06-05 00:00:01
permalink: /cs/csp&sop&cors
categories:
- CS 지식
- ETC
tag:
- CS 지식
- ETC
---

> 다른 사이트의 스크립트를 접근해 title을 가져오려다가 CORS 이슈가 발생했다.<br/>
다른 사이트 접근하는거 자체에 너무나도 이슈가 있는건데 이런 기본적인거에 무지해서 무식하게 너무 쉽게 해결될 줄 알았다.<br/>
그래서 CORS 이슈에 대해 공부를 하다가 이런 보안 정책이 왜 필요하나? 궁금증으로 XSS 부터 차근차근 공부해보는 중…!

# 웹 브라우저는 어떻게 XSS 취약점에 대비하나
XSS 공격은 서버에서 받은 콘텐츠를 브라우저가 신뢰한다는 점을 악용.<br/>
즉, XSS 공격의 근본적 문제는 스크립트 파일 등을 브라우저가 실행시켜준다는 점.<br/>

브라우저는 어떻게 공격들에 대비하고 있을까?<br/>
공격에 대비하고 있는 정책들인 CSP, SOP를 먼저 알아보자.<br/>

## CSP
**(Content Security Policy, 콘텐츠 보안 정책)**

- 특정 유형의 공격을 탐지하고 완화하는 데 도움이 되는 추가 보안 계층
- 웹 어플리케이션에서 허용되는 리소스의 유형과 출처를 명시적으로 정의하여 XSS와 같은 공격을 방지하는데 사용
- 웹 어플리케이션에서 허용되는 스크립트 실행 위치, 이미지 로딩 위치, 스타일 적용 위치 등 제한하는 정책을 설정.
- [MDN Web Docs : 컨텐츠 보안 정책 (CSP)](https://developer.mozilla.org/ko/docs/Web/HTTP/CSP#위협)

## SOP
**(Same Origin Policy, 동일 출처 정책)**

- 같은 출발지 정책
- 웹 브라우저에서 실행되는 스크립트 언어를 대상으로 하는 보안정책
- 동일한 출처에서 로드된 문서 간에만 상호 작용 허용, 다른 출처에서 로드된 문서에 대한 접근 제한
- 웹 페이지의 스크립트는 해당 페이지와 동일한 프로토콜, 호스트, 포트 번호를 가진 웹 서버에서만 로드된 리소스와 상호 작용
- 네이버 브라우저는 네이버 백엔드에만 요청 가능
- [MDN Web Docs : 동일 출저 정책 (SOP)](https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy)

> 내가 쓴 코드 : 브라우저야. 내가 보내주는 사이트에 접근해서 &lt;title&gt;태그의 text 가져와<br/>
브라우저 : SOP로 인해 거부… 못가져와<br/>
이래서 &lt;title&gt;태그의 text를 못가져온 것.

> 그래서 어떻게 SOP에 거부안당해….???<br/>
개발을 하면서 발생하는 SOP의 불편함을 해소하면서 보안을 지키기 위한 것이 CORS!!!<br/>
??? : “CORS 지키면, ‘다른 출처’에서도 데이터를 불러올 수 있게 해줄께”

# CORS
**(Cross-Origin Resource Sharing, 교차 출처 리스소 공유)**

- 가로질러서 데이터를 나눠주자
- 도메인이 다른 서버끼리 리소스를 주고 받을 때 보안을 위해 설정된 정책
- 네이버 브라우저에서 다음 백엔드로 요청할 수 있음.
- [MDN Web Docs : 교차 출저 리소스 공유 (CORS)](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)

> 프론트엔드와 백엔드가 협업하면서 각자 따로 서버를 띄우게 되었을 경우 
  React 서버 (3000 포트)와 Springboot 서버 (8080 포트)가 리소스를 주고 받으려한다면 포트가 달라 서로 다른 출처로 판단되어 CORS 위반 에러가 발생.

> 그럼 출처는 무엇이고, 같은 출처와 다른 출처는 어떻게 구분을 할까?

## 출처 (Origin)
![](/assets/images/cs/origin_1.png)

출처는 접근 시 사용하는 URL scheme (프로토콜), host (도메인), port 로 정의

**두 URL의 프로토콜, 호스트, 포트(명시되니 경우) 가 모두 같아야 동일한 출처 Same Origin**<br/>
3가지 중 하나라도 다르면 **Cross Origin**

“scheme/host/port tuple (스키마/호스트/포트 튜플)” 혹은 “tuple (튜플)” 로 참조<br/>
[MDN Web Docs : 출저](https://developer.mozilla.org/ko/docs/Glossary/Origin)

![](/assets/images/cs/origin_2.png)

```bash
http://www.naver.com/index.html 로 출처 비교 예시
```

- 동일 출처 → 경로만 다름<br/>
```bash
http://www.naver.com/dir/inner/index.html
```

- 실패 → 다른 프로토콜<br/>
```bash
https://www.naver.com/index.html
```

- 실패 → 다른 포트 ( http:// 는 기본적으로 80 포트 )<br/>
```bash
http://www.naver.com:81/index.html
```

- 실패 → 다른 호스트<br/>
```bash
http://newsstand.naver.com/index.html
```

## CORS 동작 시나리오 3가지
[동작 시나리오 3가지](https://velog.io/@effirin/CORS%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80#cors%EC%9D%98-%EB%8F%99%EC%9E%91-%EC%8B%9C%EB%82%98%EB%A6%AC%EC%98%A4-3%EA%B0%80%EC%A7%80)

## CORS 이슈 해결 방법
### 1. Access-Control-Allow-Origin 세팅
CORS 정책 위반으로 인한 문제를 해결하는 가장 대표적인 방법은, 
정석대로 서버에서 모든 클라이언트에 요청에 대한 cross-origin HTTP 요청을 허가하는 **Access-Control-Allow-Origin: * 를 헤더에 추가**

이때 * 을 세팅하면 모든 출저에서 오는 요청을 받아들이겠다는 의미인데 바꿔서 생각하면 이상한 출처에서 오는 요청도 다 허용하는 것이기 때문에 보안에 취약하다.<br/>
그러니 가급적  Access-Control-Allow-Origin: 에 정확한 출처를 명시해주는게 좋다.

API 서버들은 SOP나 CORS에 관계 없이 접근이 가능해야 하고, 
사용자의 쿠키 등 중요한 데이터를 보관하지 않는 경우에는 Access-Control-Allow-Origin: * 와 같은 헤더를 적용

### 2. Proxy
프론트 개발자들은 대부분 웹팩과 **webpack-dev-server 를 사용**하여 이 라이브러리가 제공하는 프록시 기능을 사용하면 아주 편하게 CORS 정책을 우회할 수 있음.

로컬 환경에서 /api 로 시작하는 URL로 보내는 요청에 대해 브라우저는 localhost:8000/api 로 요청을 보내는 것으로 알고 있지만, 
사실 뒤에서 **웹팩이 출처를 동일하게 맞춰서 요청을 프록싱**해주기 때문에 마치 CORS 정책을 지킨 것 처럼 브라우저를 속이면서 원하는 서버와 자유롭게 통신이 가능

**[프로젝트하면서 해결한 방법](/aitea/crawling)**

# CSP, SOP, CORS의 간단 정리
- **CSP**<br/>
  정책을 통해 특정 출처에서 허용되는 리소스 및 스크립트 실행 규칙을 지정
- **SOP**<br/>
  브라우저의 기본적인 출처 간 보안을 담당하는 정책. 스크립트 및 다른 웹 리소스의 동일 출처 여부를 확인하여 차단
- **CORS**<br/>
  프로토콜, 도메인, 포트가 서로 다른 출처 간에 리소스를 전달하는 방식을 제어하는 헤더 매커니즘
