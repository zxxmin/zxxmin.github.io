---
title: 🍪 Cookie, Web Storage, Token - Token 저장
date: 2024-02-02 00:00:01
categories:
  - CS 지식
  - ETC
tag:
  - CS 지식
  - ETC
---

# 안전한 Token 저장
- 로그인에서 중요한 점이 바로 **보안**이다.
  로그인을 위해 JWT[(Json Web Token)](https://velog.io/@vamos_eon/JWT%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EA%B0%80-1)를 
  발급 받은 후 브라우저에 안전하게 저장하는 것이 필요하다.<br/>
  일반적으로 토큰을 저장하는 저장소는 **localStorage와 Cookie**로 나뉜다.

## localStorage 저장
localStorage 경우 간편하지만 **XSS 공격에 취약**하다는 단점이 있다.

**XXS(Cross Site Scripting attack)**는 해커가 **사용자의 브라우저 환경에서 악의적인 자바스크립트 코드를 실행하는 경우**를 말한다.
localStorage는 **자바스크립트로 접근 가능**하므로, 만약 사용자의 브라우저가 악의적인 코드에 노출되게 된다면 해커에게 바로 토큰을 탈취 당한다.

## Cookie 저장
쿠키에 `http-only`와 `Secure` 설정하면, 자바스크립트로 쿠키에 접근할 수 없기 때문에 **XSS 공격에 상대적으로 안전**하다.
(물론, 위 설정으로 토큰 정보를 볼 수 없지만 XSS 공격으로 HTTP 요청을 위조할 수 있기 때문에 완전히 안저한 것은 아님)

Cookie 도 단점이 존재한다. Cookie 는 최대 4KB의 용량만 저장할 수 있기 때문에 용량이 큰 토큰의 경우 저장이 불가능할 수 있고,
**CSRF 공격에 취약**할 수 있다.

**CSRF** 공격은 사용자의 의도와 무관하게 **특정 HTTP 요청을 하게 만드는 공격 방법**으로,
대표적으로 SNS 등에 광고성 글을 올리는 공격 등을 말할 수 있다.
토큰은 HTTP 요청마다 `RequestHeader`에 담겨 보내기 때문에, 만약 서버에서 Cookie에 담긴 토큰 정보를 이용해 사용자 권한을 검증한다면
CSRF 공격 시 문제가 된다.

이 때문에 HTTP 요청 시 `RequestHeader`에 토큰 정보를 담아 보내지 않고, **Autorization 헤더를 별도 세팅**하여 보내주고 있다.
API에 Authroization 헤더를 사용하는 만큼, **Cookie는 XSS로부터 안전한 저장소 역할을 하고 HTTP 요청마다 client에서 쿠키에 저장된 
토큰 정보를 받아 헤더를 세팅하여 HTTP 요청을 하는 방식**으로 구현한다.