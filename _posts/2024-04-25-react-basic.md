---
title: 리액트의 기본
date: 2024-04-25 00:00:03
categories:
- React
---

컴포넌트, JSX, Props, 이벤트, State, 리렌더링, Ref, 리액트 훅 등 기본 기능을 알아보자<br />
[한입 크기로 잘라먹는 리액트]

# 컴포넌트
리액트는 컴포넌트 기반의 UI 라이브러리. 컴포넌트는 리액트의 핵심 개념 중 하나.<br />
페이지의 모든 요소를 컴포넌트 단위로 쪼개어 개발하고, 완성된 컴포넌트를 레고 조립하듯이
하나로 합쳐 페이지를 구성하기 때문이다.

컴포넌트는 주로 JavaScript의 클래스나 함수를 이용해 만드는데, 클래스 컴포넌트를 만드는 방식은
기본 설정 코드를 작성하는 등 함수로 만드는 컴포넌트에 비해 단점이 많아 선호하지 않는다.<br/>
* 인프런 section 17 복습

Hedaer.js
{% highlight javascript linenos %}
const Header = () => {
    return (
        <header>
            <h1>header</h1>
        </header>
    );
};
{% endhighlight %}

App.js
{% highlight javascript linenos %}
import "./App.css"
import Header from "./Header 경로"

function App() {
    return (
        <div className="App">
            <Hedaer />
            <Body />
            <Footer />
        </div>
    )
}
export default App;
{% endhighlight %}

컴포넌트 이름은 항상 대문자로 시작한다.<br />
App 처럼 다른 컴포넌트를 return 문 내부에 포함하는 컴포넌트 : **부모 컴포넌트**<br />
Header 처럼 App의 return 문에 포함된 컴포넌트 : **자식 컴포넌트**<br />
컴포넌트는 부모-자식 관계(= 컴포넌트 트리)라는 계층 구조를 형성한다.
컴포넌트 트리에서 App는 항상 최상위에 존재하므로 '루트 컴포넌트'라고 한다.

