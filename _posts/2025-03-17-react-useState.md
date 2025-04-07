---
title: useState
date: 2024-04-28 00:00:01
categories:
- React
tags:
- React
---

리액트 훅의 기본. 컴포넌트의 상태(state)를 관리할 수 있는 훅.<br/>
[한입 크기로 잘라먹는 리액트]

# useState 
리액트 훅의 기본. 컴포넌트의 상태(state)를 관리할 수 있는 훅.<br/>
**상태 유지 값**과 **그 값을 갱신하는 함수** 반환

## 문법
```javascript
const [ staet, setState ] = useState(initialState);
```
- state : 상태 값 저장 변수
- setState : 상태 값 갱신 함수
- initialState : 상태 초기

## 예시
```javascript
import { useState } from 'react';

const ProfileFun = () => {
    const [ email, setEmail ] = useState('');
    
    return (
        <>
            <label htmlFor="email">email</label>
            <input
                value={email}
                id='email'
                onChange={() => {setEmail(e.target.value)}}
            />
        </>
    )
}

export default ProfileFun
```

## 상태값 변경 시 리렌더링 발생
useState에서 반환된 배열의 두번쨰 값인 setter 함수를 호출하면 상태 값을 변경할 수 있고, 상태 값이 변경되면 해당 컴포넌트는 다시 렌더링.<br/>
useState에 인자로 전달된 값을 상태의 초기값으로 사용하고 이후 setter 함수에 의해 상태의 값이 변경되면 다음 렌더링에서는 그 상태를 유지.

위의 예시를 보면 onChange 이벤트 시 setEmail을 호출하여 change 이벤트가 일어날 때마다 렌더링 되고 렌더링 되면서 변경된 값이 유지.

<div class="callout">
    <span>:bulb:</span>
    setState 호출 → 상태 변경 → 리렌더링 (변경된 상태 값 사용)
</div>
<br/>