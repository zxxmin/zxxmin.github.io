---
title: 리액트의 기본
date: 2024-04-25 00:00:03
categories:
- React
tags:
- React
---

리액트의 기본! 컴포넌트, State, Ref, 리액트 훅<br />
[한입 크기로 잘라먹는 리액트]

# 컴포넌트
리액트는 컴포넌트 기반의 UI 라이브러리. 컴포넌트는 리액트의 핵심 개념 중 하나.<br />
페이지의 모든 요소를 컴포넌트 단위로 쪼개어 개발하고, 완성된 컴포넌트를 레고 조립하듯이
하나로 합쳐 페이지를 구성하기 때문이다.

컴포넌트는 주로 JavaScript의 클래스나 함수를 이용해 만드는데, 클래스 컴포넌트를 만드는 방식은
기본 설정 코드를 작성하는 등 함수로 만드는 컴포넌트에 비해 단점이 많아 선호하지 않는다.<br/>

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

# State
동적인 리액트 컴포넌트를 만들기 위해 State 이용

## useState
함수 useState로 State를 생성
{% highlight javascript linenos %}
import { useState } from "react"

const [ light, setLight ] = useState('off');
{% endhighlight %}
**light** : 현재 상태의 값을 저장하고 있는 **변수**. 이 변수를 **'State 변수'** 라고 지칭<br/>
**setLight** : State 변수의 값을 변경하는, 상태를 업데이트 하는 **함수**. 이 함수를 **'set 함수'** 라고 지칭<br/>
**useState** : 호출할 때 인수로 값을 전달하면 이 값이 State 초깃값

## State 끌어올리기 (State Lifting)
State 값이나 set 함수를 여러 컴포넌트에서 사용하는 경우, 최상위인 컴포넌트에 관리하는 기능

리액트에서 컴포넌트 간에 데이터를 전달할 때 Props 사용. **전달 방향은 언제나 부모로부터 자식에게 전달하는 방식**
=> **단방향 데이터 흐름** 마치 일방통행 차선을 연상<br/>
데이터의 흐름을 쉽게 이해할 수 있어 관리 용이

반면 State를 변경하는 이벤트는 **자식에서 부모를 향해 역방향으로 전달** 되어야한다.<br/>
리액트 앱을 설계할 때는 **데이터는 위에서 아래로**, **이벤트는 아래에서 위로** 향하도록 설계 필요.



# Ref
Reference(참조)의 줄임말로, 돔(DOM) 요소를 직접 조작할 수 있음.

## useRef
ref 객체 생성
{% highlight javascript linenos %}
import { useRef } from "react"

function Body() {
    const textRef = useRef();

    const onChangeText = (e) => {
        textRef.current.value = "";
    }

    const onClickBtn = () => {
        textRef.current.focus();
    }

    return (
        <div>
            <input type="text" ref={textRef} onChange={onChangeText} />
            <button onClick={onClickBtn}>제출</button>
        </div>
    )
}
{% endhighlight %}

# 리액트 훅 (React Hook)
함수로 만든 리액트 컴포넌트에서 클래스로 만든 리액트 컴포넌트의 기능을 이용하도록 도와주는 함수들.<br/>
리액트 훅은 이름 앞에 항상 **use**를 붙임.
- **useState**<br/>
가장 기본적인 Hook. **컴포넌트의 상태를 관리**
- **useRef**<br/>
**DOM에 직접 접근**할 때 사용. 변수 값이 변하더라도 **리렌더링 유발 방지**하고 싶을 때.
- **useEffect**<br/>
**컴포넌트 내의 상태 변화**가 있을 때, 이를 감지하여 특정 작업을 해줄 수 있는 훅.
- **useCo**ntext
- **useReducer**<br/>
**다양한 상태를 다른 값으로 업데이트해주고 싶을 때** 사용하는 훅
- **useCallback**<br/>
**의존성 배열 내의 값이 변할 때** 재정의.
- **useMemo**<br/>
**특정 값이 변할 때**만 정의. 즉, **변수의 최적화**를 위한 훅.

등등이 있음.