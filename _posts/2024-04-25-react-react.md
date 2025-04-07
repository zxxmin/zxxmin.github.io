---
title: Let us React 🕹️
date: 2024-04-25 00:00:02
categories:
- React
tags:
- React
---

찐 프론트 개발자가 되기위한 리액트의 시작!<br />
[한입 크기로 잘라먹는 리액트]

# React???
복잡한 웹사이트를 쉽고 빠르게 개발할 수 있는 **Node.js의 라이브러리**<br />
페이스북 개발팀이 만들어 2013년 오픈소스로 공개됨<br />
리액트를 사용하면 사용자와 인터렉션 *(상호작용, 소통이라는 뜻. 좋아요 버튼이나 채팅처럼 사용자와 웹 서비스 간에*
*양방향으로 소통하는 기능을 지칭)* 이 원활한 웹 애플리케이션을 쉽게 구축할 수 있다.

## 리액트 특징

### 컴포넌트 기반의 유연성
리액트는 모듈화를 이용해 중복 코드를 제거한다. 여러 페이지에서 공통으로 사용하는 코드를 **'컴포넌트'** 단위의
모듈로 만들어 놓고 **필요할 때 호출**해서 사용한다.<br />
컴포넌트는 리액트를 대표하는 중요 개념 중 하나, **페이지를 구성하는 요소**라는 의미
<div class="callout">
    <span>:woman_technologist:</span>
    회사에서 프로젝트를 할 때, 모든 페이지에서 공통으로 사용되고 있는 header나 menu는 대다수의 html에
    똑같이 작성하는 것 보다 파일을 각각 따로 관리를 하는 부분이 유지보수도 편하고, 백엔드 개발자 분들도
    작업하기 수월하다고 생각하여 파일을 나눴다.<br />
    <br />
    여기서 중요한 건... 내가 작업하면서 확인할 때 어떻게 조립해서 보지...? 라고 생각이 들었고, <br />
    promise로 함수를 만들어서 공통 파일을 불러와서 작업을 했었다.<br />
    <br />
    이 때 내가 나눈 파일들이 '컴포넌트' 라고 할 수 있고,<br />
    컴포넌트를 조립을 할 때는 복잡한 함수를 사용했는데 리액트에서는 간단하게 조립할 수 있다.
</div>

**프로젝트 당시 promise로 컴포넌트 파일을 조립하는 방법**
{% highlight javascript linenos %}
const callHeader = await fetch('wrap.html')
    .then(res => res.text())
    .then(data => {
        const parser = new DOMParser();
        const htmlDoc = parser.parseFromString(data, 'text/html');
        const bodyContent = htmlDoc.querySelector('body').innerHTML;
        document.querySelector('body').innerHTML += bodyContent;
    })
    .catch(error => console.log('Error:', error));

const contents = document.querySelector('.CONTENTS-WRAPPER');
const container = document.querySelector('body > div');

if(contents && container) {
    contents.appendChild(container);
}
{% endhighlight %}

**리액트 방법**<br />
Header.js
{% highlight javascript linenos %}
function Header() {
    return (
        <header>
            <h1>안녕하세요</h1>
        </header>
    )
}
export default Header;
{% endhighlight %}

App.js
{% highlight javascript linenos %}
import Header form "/Header 경로"

function APP() {
    return (
        <div className="App">
            <Header />
        </div>
    )
}
{% endhighlight %}

### 쉽고 간단한 업데이트
업데이트란 웹 페이지의 정보를 교체하는 일. 인스타그램의 '좋아요' 기능 같은 것.<br />
js로 돔을 조작하면 페이지를 새롭게 렌더링하여 업데이트를 한다.<br />
그런데 돔은 트리 구조로 이루어져 있기 때문에 구성이 복잡하면 정확히 원하는 요소를 찾기 어려움.

리액트는 데이터가 바뀌어 **업데이트가 필요하면, 교체가 필요한 요소는 삭제**하고
**수정 사항을 반영한 요소를 다시 만들어 통째로 업데이트** 한다.<br />
자동차가 고장나면 부품을 수리하는게 아니라 새 차로 바꾸는 것과 같다.

### 빠른 업데이트
돔의 업데이트가 필요 이상으로 많아지면 브라우저의 성능을 떨어뜨리게 된다.
브라우저 성능이 떨어지면 렉(lag) 현상이 일어나거나 심할 경우 응답 불능 상태에 빠진다.<br />

리액트는 여러 번의 업데이트를 모았다가 한 번에 처리하기 위해 버추얼 돔(Virtual DOM)을 활용한다.<br />
버추얼 돔은 가상의 돔으로 **변경 사항이 생기면 버추얼 돔을 업데이트** 하는 식으로 변경 사항을 모았다가
**한 번에 실제 돔을 업데이트**한다.
결과적으로 여러번의 업데이트를 모아 한 번에 수행하므로, 업데이트가 잦아도 브라우저의 성능을 떨어뜨리지 않는다.

