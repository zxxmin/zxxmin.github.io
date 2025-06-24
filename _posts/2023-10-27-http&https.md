---
title: 🌏 HTTP, HTTPS
date: 2023-10-27 00:00:01
permalink: /cs/http&https
categories:
- CS 지식
tag:
- CS 지식
---

# HTTP
## HTTP 프로토콜
**HyperText Transfer Protocol**의 약자로 클라이언트와 서버가 통신하기 위해 정의된 규약<br/>
어떻게 메시지를 교환할지 정해 놓은 규칙. 예를 들면 내가 A 데이터를 요청하면 너는 B 데이터를 반환 하라는 식의 통신 규칙<br/>
이런 규칙을 따름으로써 기기와 서비스를 만든 회사가 달라도 서로 데이터를 주고 받을 수 있음.

![](/assets/images/cs/HTTP.gif)

## HTTP 구조
클라이언트에서 **요청(Request)** 를 보내면 **응답(Response)** 을 함.
- **URL 분석 및 접속**<br/>
  웹 브라우저는 URL을 분석해 서버의 IP주소와 포트 (HTTP 기본은 80)를 이용해 서버와 TCP/IP 연결 요청
- **Request 헤더 전송**<br/>
  브라우저에서 요청 파일명 등이 기술된 헤더를 전송
- **Response 바디 전송**<br/>
  필요한 경우 로그인 폼에 입력한 데이터나 첨부 파일 등의 추가적인 데이터를 전송
- **Response 헤더 전송**<br/>
  서버에서 헤더를 수신하고 응답 상태를 확인하며, 바디의 Content-Type 등 확인
- **Response 바디 전송**<br/>
  바디가 있는 경우, 서버에서 수신한 바디를 헤더에 기술된 Content-Type에 따라서 text/html 인 경우 HTML 렌더링,
  image/jpeg 인 경우 그림을 띄우는 등 적절한 해석

> 예를 들어 특정 링크를 누르면 홈페이지에 접속<br/>
  → 사용자는 링크를 클릭 해 서버에 홈페이지 정보를 요청 (Request) 하여 통신 시작.<br/>
    서버는 사용자의 요청에 따라 홈페이지 정보를 응답 (Response) 하고 통신 종료.<br/>
    홈페이지 정보를 전달받은 사용자의 브라우저는 홈페이지 정보를 화면에 띄움.<br/><br/>
    **HTTP 통신 과정의 시작과 끝**

## HTTP Method
메서드는 요청의 종류를 서버에 알려주기 위해 사용.
- **GET** : URL에 해당하는 자료의 전송을 요청.
- **HEAD** : GET 요청으로 반환될 데이터 중 헤더 부분에 해당하는 데이터만 요청. 해당 자원이 존재하는지 혹은 서버에 문제가 없는지를 
  확인하기 위해 사용.
- **POST** : 클라이언트가 서버에서 처리할 수 있는 자료를 보냄. 
- **PATCH** : URL의 데이터를 부분적으로 수정할 것을 요청. 
- **PUT** : URL에 지정한 데이터를 저장할 것을 요청. 
- **DELETE** : URL의 정보를 제거할 것을 요청. 
- **TRACE** : 클라이언트가 서버에게 송신한 요청의 내용을 반환해 줄 것을 요청. 
- **CONNECT** : 클라이언트가 특정 종류의 프록시 서버에게 연결을 요청. 
- **OPTIONS** : 해당 URL에서 지원하는 요청 메세지의 목록을 요청.

### GET과 POST
### GET
- URL 뒤쪽에 쿼리스트링으로 데이터를 붙여서 서버로 데이터를 보내 정보가 겉으로 드러난다.
- URL의 크기가 제한적이라 보낼 수 있는 정보량의 한계가 있음.
- 보안 취약

### POST
- 데이터가 body 안에 숨겨져서 발송.
- 보안을 위해서 POST 사용.

### GET을 사용하는 이유
위 설명만 보면 GET은 보낼 수 있는 데이터도 한계적이고 보안도 상대적으로 취약한데 **왜 사용하는걸까?**

일반적으로 GET은 DB에서 자료를 가져와서 **읽을 때** 주로 사용.<br/>
동영상, 이미지, 텍스트를 읽어오는 등 서버에 저장된 데이터를 가져오는데 사용.<br/>
POST는 서버의 DB 값이나 상태를 바꾸는 **등록, 수정**을 할 때 주로 사용.<br/>
게시판에 글을 작성, 회원가입을 한다던지 DB 영향을 줄 때 사용.

## HTTP Request
웹 브라우저는 웹 서버에 데이터를 **요청**하는 **클라이언트 프로그램**.<br/>
요청은 서버가 인식할 수 있는 약속된 형식(HTTP 형식)을 따라야함.<br/>
요청 데이터는 **HEADER 와 BODY**로 구성

![](/assets/images/cs/HTTPRequest.png)

**필수 요소로 요청의 제일 처음에 와야하는 3개의 필드**
- 요청 메서드 : GET, PUT, POST, PUSH, OPTIONS 등의 요청 방식
- 요청 URI : 요청하는 자원의 위치를 명시
- HTTP 프로토콜 버전 : 웹 브라우저가 사용하는 프로토콜 버전

**그 외 키 값**
- Host : 요청을 보내는 Host<br/>
  ex) www.google.co.kr
- Content-Type : 요청에 바디가 있는 경우 그 파일 포맷<br/>
  ex) Content-Type: application/json
- Cookie : 웹 브라우저에 저장된 쿠키들
- User-Agent : 클라이언트의 정보, 이를 통해 사용하는 브라우저 감지

## HTTP Response

![](/assets/images/cs/HTTPResponse.png)

- 프로토콜과 응답 코드 : 웹 브라우저가 사용하는 프로토콜, 서버의 응답 상태 (1xx - 5xx)
- Set-Cookie : 웹 브라우저에게 쿠키 생성을 요청
  ex) Set-Cookie: UserID=tester; Max-Age=3600; Version=1
- Content-Type : 응답에 바디가 있는 경우 그 포맷
  ex)  Content-Type: text/html; charset=utf-8

## Status Code
HTTP 통신을 통해 전달하는 상태 코드는 요청에 대한 응답의 결과를 세자리의 번호로 나타냄

1XX : 요청을 정상적으로 받은 것을 응답, 계속해서 작업중임을 뜻함<br/>
2XX : 클라이언트의 요청을 수신, 승낙하였고, 정상적으로 요청이 수행될 것임을 의미<br/>
3XX : Redirection과 관련한 동작이 수행 되었을 때 돌려 받는 상태 코드<br/>
4XX : 클라이언트가 보낸 요청이 잘못 되었음을 의미<br/>
5XX : 서버에서 요청을 받아 로직을 수행하는 과정에서 문제가 생겼을 때 받게되는 상태 코드<br/>
[상태코드 자세히보기](https://developer.mozilla.org/ko/docs/Web/HTTP/Reference/Status)

# HTTPS
**HyperText Transfer Protocol over Secure Socket Layer**의 약자로 간단히 말하면 보안이 강화된 HTTP<br/>
일반적으로 **SSL** (전자상거래에서의 데이터 보안을 위해서 개발한 통신 레이어, 표현 계층의 프로토콜로 응용 계층 아래에 있기 때문에 
어떤 응용 계층의 데이터라도 암호화 가능) 또는 **TSL** 프로토콜을 이용해 데이터를 암호화<br/>
HTTP는 기본적으로 평문 데이터 전송을 원칙으로 하기 때문에 개인 프라이버시가 오가는 서비스들에서는 사용이 힘듬.<br/>
HTTPS는 SSL 레이어 위에 HTTP를 통과 시키는 방식. 즉, 평문의 HTTP 문서는 SSL 레이어를 통과하면서 암호화 되어 목적지에 도착하고 목적지에서는 SSL 레이어를 통과하면서 복화화 되어 웹 브라우저에 전달

![](/assets/images/cs/HTTPS.png)

# HTTP vs HTTPS 차이점

![](/assets/images/cs/HTTPvsHTTPS.png)
