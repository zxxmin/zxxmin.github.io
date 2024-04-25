---
title: Node.js에 대하여
date: 2024-04-25 00:00:01
categories:
- React
---

<div class="callout">
    <span>:woman_technologist:</span>
    Node.js는 단순 자바스크립트 런타임이며, 게임과 게임기에 비유하자면<br />
    자바스크립트는 게임, Node.js는 게임을 구동하는 게임기에 비유할 수 있다.<br /><br />
    런타임이라는 설명은 구체적으로 나오지 않아 정확하게 이해하고 싶어서 파고파고 들었다.
</div>
<br>

# Node.js???
<div class="callout">
    <span>:desktop_computer:</span>
    Node.js는 Chorme V8 JavaScript 엔진으로 빌드 된 JavaScript 런타임 입니다.
    - Node.js 공식 홈페이지
</div>

## 런타임???
Node.js는 **런타임** 이라고 한다. 자꾸 js 같은 언어로 이해하고 있었던 나 자신 한심해...<br />
런타임은 특정 언어로 만든 **프로그램을 실행할 수 있는 환경**을 뜻한다.<br />
정확하게 런타임을 이해하려면 JavaScript 부터 이해해야한다.

## JavaScript
간단하게 설명하자면,<br />
정적인 언어인 HTML을 조작해서 웹페이지를 동적으로 바꿔주는 기능을 한다.
<div class="callout">
    <span>:woman_technologist:</span>
    HTML은 JavaScript가 조작하는데 JavaSciprt 해석은 누가하나???
</div>

## JavaScript 해석 누가하고 있냐?
바로 **브라우저**이다. 브라우저에는 자바스크립트 해석 엔진이 있다. (브라우저의 종류에 따라 쓰는 엔진이 다름)
따라서 **기존에는 자바스크립트를 인터넷 브라우저에서만 실행** 할 수 있었다.<br />
그러나 2008년에 구글이 V8 엔진을 사용하여 크롬을 출시했고, V8엔진이 너무 뛰어나서 V8 엔진 기반의 노드 프로젝트가 시작했다. Node.js(V8) 두둥 등장.<br />
Node.js는 브라우저 내 말고도 **다른 환경에서 자바스크립트를 사용할 수 있게 해준다.**<br />
따라서 **Node.js는 JavaScript 실행 환경 (=런타임)** 이다.