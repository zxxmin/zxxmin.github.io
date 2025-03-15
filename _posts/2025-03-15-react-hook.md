---
title: Hook🪝
date: 2024-09-10 00:00:01
categories:
- React
---

함수형 컴포넌트에서 React state와 생명주기 기능을 연동할 수 있게 해주는 함수.<br/>
[한입 크기로 잘라먹는 리액트]

# Hook
React 버전 16.8 부터 React 요소로 새로 추가.
Hook을 이용하여 기존 Class 바탕의 코드를 작성할 필요 없이 상태 값과 여러 React 기능을 사용할 수 있음.<br/>
함수형 컴포넌트에서 React state와 생명주기 기능을 연동할 수 있게 해주는 함수.

Hook에는 리액트에서 제공하는 내장 훅 (useState, useEffect, ...)과 사용자가 직접 정의할 수 있는
Custom Hooks가 있음.

# Hook이 생기기 전
함수형 컴포넌트에 Hook이 나오기 전까지는 일반적으로 클래스형 컴포넌트를 많이 사용.<br/>
하지만 클래스형 컴포넌트에서 상태(state)를 사용하고, 생명주기 메서드를 사용하는 방식은
많은 문제들과 불편함을 가지고 있음.

1. 컴포넌트 간의 상태 로직을 재사용하기 어려움.
2. 복잡한 컴포넌트들의 이해가 어려움.<br/>
→ render props 나 HOC(Higher Order Component)와 같은 패턴들
3. Class 문법 자체의 어려움.<br/>
→ Class 함수에서의 this 개념

# Hook이 생긴 후
1. 컴포넌트 간의 계층을 바꾸지 않고 상태 로직을 재사용할 수 있음.
2. 하나의 컴포넌트를 생명주기가 아닌 기능을 기반으로 하여 작은 함수 단위로 나눌 수 있음.
3. Class 문법 없이도 React 기능을 사용할 수 있게끔 해줌.

# Hook 규칙
1. 같은 훅을 여러 번 호출 가능
```javascript
const Profile = () => {
    // useState 여러번 호출 가능
    const [name, setName] = useState('Jimin');
    const [surname, setSurname] = useState('Song');
}
```

2. 컴포넌트 **최상위(at the top level)에서만 호출**할 수 있음.<br/>
**반복문, 조건문, 중첩된 함수 내에서 훅을 사용할 수 없음.**<br/>
→ 컴포넌트 렌더링 될 떄마다 항상 동일한 순서로 Hook이 호출되는 것이 보장.<br/>
이는 React가 useState와 useEffect 가 여러 번 호출되는 중에서도 훅의 상태를 올바르게 유지.<br/>
   [Hook의 규칙 - React](https://ko.legacy.reactjs.org/docs/hooks-rules.html)

3. 훅은 **React 함수 내에서만 호출**할 수 있음.<br/>
함수형 컴포넌트에서 호출해야하며, 추가적으로 custom hooks에서 또한 호출 가능.

4. **비동기 함수(async 키워드가 붙은 함수)는 콜백 함수로 사용할 수 없음.**
```javascript
export default function App() {
    // useEffect Hook 내부에 비동기 함수가 들어가므로 에러 발생
    useEffect(async () => {
        await Promise.reslove(1)
    }, [])
    
    return (
        <div>Test</div>
    )
}
```

# Hook의 종류
<img src="/assets/images/react/hook1.png">

기본적으로 컴포넌트 상태를 관리할 수 있는 `useState`와 컴포넌트 생애 주기에 개입할 수 있는 `useEffect`,
컴포넌트 간의 전역 상태를 관리하는 `useContext` 훅을 제공하고 있음.<br/>
추가로 제공하는 Hook은 기본 Hook의 동작 원리를 모방해서 만들어졌음.

### 추가적인 Hook의 기본적인 역할
- **useReducer**<br/>
상태 업데이트 로직을 reducer 함수에 따로 분리
- **useRef**<br/>
컴포넌트나 HTML 엘리먼트를 레퍼런스로 관리
- **useImperativeHandle**<br/>
useRef의 레퍼런스를 상위 컴포넌트로 전달
- **useMemo, useCallback**<br/>
의존성 배열에 적힌 값이 변할 때만 값 또는 함수를 다시 정의
- **useLayoutEffect**<br/>
모든 DOM 변경 후 브라우저가 화면을 그리기 이전 시점에 effect를 동기적으로 실행
- **useDebugvalue**<br/>
사용자 정의(custom) Hook의 디버깅을 도와줌