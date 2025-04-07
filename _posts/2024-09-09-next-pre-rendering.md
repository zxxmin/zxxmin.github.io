---
title: Pre Rendering
date: 2024-09-09 00:00:02
categories:
- Next
tags:
- Next
---

Client Side Rendering의 단점을 효율적으로 해결하는 렌더링 방식<br/>
[한입 크기로 잘라먹는 Next.js(15+)]

# React의 렌더링 방식
## Client Side Rendering
클라이언트 즉, 웹 브라우저에서 **직접 화면을 렌더링**하는 방식<br/>

<img src="/assets/images/next/client-side-rendering.png">

### 장점
* 초기 접속 이후의 페이지 이동이 빠름<br/>
→ 초기 요청 시 서버로부터 받은 JS Bundle 안에 모든 페이지의 컴포넌트가 들어가 있어서 초기 접속 후 페이지 이동 시 **서버에 페이지 요청을 할 필요가 없기 때문**<br/>
→ 페이지 이동 시 서버에서 새로운 페이지를 요청하지 않고 **브라우저가 자체적으로 React 앱을 실행하여 해당 컴포넌트로 갈아끼우기 때문**에 초기 접속 이후의 페이지 이동이 매우 빠르고 쾌적

### 단점
* 초기 접속 속도가 느림<br/>
→ FCP가 느림. 사용자가 요청을 하고 서버가 index.html을 주면 빈 화면을 먼저 렌더링하고 JS Bundle들을 받아온 후 JS 실행하여 화면이 보이기까지의 시간이 느림

### FCP (First Contentful Paint)
요청 시작 시점부터 컨텐츠가 화면에 처음 나타나는데 걸리는 시간.<br/>
요청시간 ↔ 컨턴체 렌더링<br/>
FCP가 느려지면 페이지 이탈률이 높아짐!!

<br/>

# Next의 렌더링 방식
## 사전 렌더링
브라우저의 요청에 사전에 렌더링이 완료된 HTML을 응답하는 렌더링 방식<br/>
Client SIde Rendering의 단점을 효율적으로 해결

<img src="/assets/images/next/server-side-rendering.webp">

### 장점
* 서버에서 빈 껍데기인 index.html을 보내는 CSR과는 다르게 **서버에서 JS를 실행하여 렌더링 된 HTML을 응답. FCP가 빠름**

그 후 JS Bundle 파일을 받아 html 파일과 JS 파일을 연결하는 작업인 수화(Hydration)을 통해 상호작용된 페이지를 볼 수 있음.<br/>
→ TTI : Time To Interactive

그 후 페이지 이동은 CSR과 똑같음

# 결론
html을 미리 렌더링하는 사전렌더링으로 Client Side Rendering의 단점 극복.<br/>
빠른 FCP 달성 + 빠른 페이지 이동 가능