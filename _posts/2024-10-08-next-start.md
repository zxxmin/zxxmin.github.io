---
title: Next.js
date: 2024-09-09 00:00:01
categories:
- Next
---

React만을 위한 React 전용의 웹개발 프레임워크, 리액트를 보다 더 강력하고 편하게 사용할 수 있는 기능들을 제공한다.<br/>
[한입 크기로 잘라먹는 Next.js(15+)]

# Next.js
React만을 위한 **React 전용의 웹 개발 프레임워크**<br/>
리액트를 보다 더 강력하고 편하게 사용할 수 있는 기능들을 제공<br/>
**리액트 확장판** → 리액트 + 페이지 라우팅, 빌트인 최적화 기능, 다이나믹 HTML 스트리밍 등

# Next.js가 인기있는 이유?
Next.js가 인기있는 이유가 라이브러리가 아니라 프레임워크이기 때문이다?
* React : UI 개발을 위한 JS **라이브러리**
* Next : React 전용 **프레임워크**

## 라이브러리 VS 프레임워크
라이브러리와 프레임워크의 차이 → **주도권이 누구한테 있는가?**

<table>
    <thead>
        <tr>
            <th></th>
            <th>프레임워크</th>
            <th>라이브러리</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th>주도권</th>
            <td>프레임워크</td>
            <td>개발자</td>
        </tr>
        <tr>
            <th></th>
            <td>
                → <b>프레임워크가 제공하는 기능을 이용</b>하거나<br/>
                허용하는 범위 내에서만 추가 도구 사용 가능<br/>
                ex) 페이지 라우팅 → Page Router, App Router
            </td>
            <td>
                → <b>기능 구현을 원하는 방향</b>으로 진행.<br/>
                사용하고 싶은 도구, 기술 사용<br/>
                ex) 페이지 라우팅 → 사용하고 싶은 도구, 기술 사용
            </td>
        </tr>
        <tr>
            <th>자유도</th>
            <td><b>낮음</b> → 거의 모든 기능 제공</td>
            <td><b>높음</b> → 기본 기능 외 제공 없음</td>
        </tr>
        <tr>
            <th></th>
            <td>방법이 정해져있음. 그 틀안에서 개발</td>
            <td>렌더링을 제외한 모든건 다 직접</td>
        </tr>
    </tbody>
</table>

## 결론
프레임워크인 Next.js는 다양한 기능을 기본적으로 제공하는 기능들을 이용해서 많은 문제들을 간단하게 해결 가능.
<img src="/assets/images/next/next-is-framework.webp">

<div class="callout">
    <span>:rocket:</span>
    추후에 Next에 라우팅에 대해 배울 예정. Next 라우팅 기능에는 Page Router와 App Router<br/>
    Page Router → Next 초창기부터 제공되었던 구버전 라우터<br/>
    App Router → Next 13 버전과 함께 처음으로 공개된 신규 라우터. 신규 기능들을 제공<br/>
    현재 과도기로 둘다 사용할 줄 알아야한다.
</div>


# Next App 생성
## App 생성
{% highlight powershell linenos %}
npx create-next-app@14 폴더네임 또는 .
{% endhighlight %}

<img src="/assets/images/next/create-next-app.webp">

→ 페이지 라우팅 실습을 위해 14버전과 App Router? No 사용

## local 서버
{% highlight powershell linenos %}
cd 폴더네임
npm run dev
{% endhighlight %}

<img src="/assets/images/next/start-next.webp">

# Next 폴더 구조
<div style="display:flex; felx-direction:row; gap:20px">
    <div><img src="/assets/images/next/next-folder.webp"></div>
    <div>
        <b>public</b> : 이미지 같은 정적 파일<br/>
        <br/>
        <b>src/pages</b> : 페이지가 되는 파일<br/>
        &ensp;<b>_app.tsx</b> : 리액트의 App.jsx<br/>
        &ensp;&ensp;root 컴포넌트<br/>
        &ensp;&ensp;모든 페이지 역할을 하는 컴포넌트의 부모 컴포넌트<br/>
        &ensp;<b>_document.tsx</b> : 리액트의 index.html
        &ensp;&ensp;페이지 전체에 필요한 html 태그 (meta, script 등등)<br/>
        <br/>
        <b>src/styles</b> : CSS 파일
    </div>
</div>
실습을 위해 next.config.mjs → reatStrictMode → false

# 평가
라이브러리와 프레임워크에 대해서 확실히 짚고 넘어가는 계기가 되었다.<br/>
넥스트를 왜 배워야하는지 리액트보다 어떤 부분이 더 좋은지 비교할 수 있어서 좋았다.