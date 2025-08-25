---
title: 전역 상태 관리 라이브러리
date: 2025-08-18 00:00:01
permalink: /react/globalStateLibrary
categories:
  - React
tag:
  - React
---

# 📌 상태(State)란?
간단히 말해, **UI를 그리는 데 필요한 데이터**
React에서는 **상태가 변하면 화면이 다시 렌더링**되기 때문에, 상태를 어떻게 관리하느냐가 **성능과 가독성에 큰 영향**을 미침.

# 📂 상태의 종류
## 1. 지역 상태 (Local State)
* **특정 컴포넌트 안에서만 관리되는 상태**
* 다른 컴포넌트와 공유하지 않음
* 예 : 입력창의 값, 체크박스 상태, 모달 열림 여부 등

## 2. 컴포넌트 간 상태 (Shared State)
* **여러 컴포넌트가 공유하는 상태**
* 보통 **상위 컴포넌트에서 하위 컴포넌트로 props 전달**
* 이 과정에서 `props drilling` 발생<br/>
  `props drilling` : 데이터를 전달하기 위해 필요 없는 중간 컴포넌트들까지 props를 거쳐야 하는 상황

## 3. 전역 상태 (Global State)
* **프로젝트 전체에 접근 가능한 상태**
* 부모에서 자식으로 데이터 전달 (`props drilling` 방식 활용)
* 예 : 사용자 정보, 테마 설정, 다국어 설정, 로그인 여부 등

# ⚠️ 상태 관리가 필요한 이유
* 부모-자식 관계가 아니면 **직접적인 데이터 전달이 어려움**
* 전역 상태를 관리하지 않으면, 결국 **`props drilling` 이 심해져 유지보수가 어려워짐**

# 🌐 상태 관리 라이브러리
상태 관리 라이브러리를 사용하며 **데이터를 중앙에서 관리**하고 필요한 컴포넌트에서만 가져다 쓸 수 있음.

상태 관리 라이브러리는 다양하지만, **Context API**(라이브러리는 아니지만, React 기본 제공), **Redux**(npm trends 1위), 
**Recoil**(프로젝트 사용 경험), **Zustand**(앞으로 사용 예정) 총 4가지 비교

![](/assets/images/react/global-state-1.png)

## 📌 Context API
**React 컴포넌트 트리 안에서 전역 상태를 공유할 수 있도록** 만들어진 방법.<br/>
다만, 본질적으로는 종속성 주입을 위한 도구이기 때문에, Redux나 Recoil 같은 **전역 상태 관리 툴**이라고 하기 다소 애매

⭐️ Context API는 이미 존재하는 상태를 다른 컴포넌트들과 쉽게 공유할 수 있게 해주는 역할 (전역 상태 공유)

### Context API 구성
1. Context
   - **전역 상태의 저장소** 같은 개념
   - `createContext()`로 생성
   - 내부에 `Provider`와 `Consumer` 정의
   - `Consumer`는 Context 통해 상태에 접근 가능
   
2. Provider
   - Context에 **데이터를 공급**하는 역할
   - `value` 속성에 전달한 값이 전역 상태로 공유됨
   - 보통 **루트 컴포넌트**에서 정의

3. Consumer
   - Provider가 제공한 **전역 상태를 받아 사용**하는 역할
   - 클래스형 컴포넌트가 Hooks를 쓰지 않는 경우 주로 사용
   - 함수형 컴포넌트에서는 `useContext`로 대체 가능
   - **Provider의 value를 받아 쓸 수 있는 컴포넌트**

4. useContext (Hook)
   - 최근에는 `Consumer` 대신 `useContext` 훅을 써서 더 간결하게 작성

### Context API 예시
- src / store / context / useCounterContext.tsx
  ![](/assets/images/react/useCounterContext.png)

- src / pages / context / ContextPage.tsx
  ![](/assets/images/react/ContextPage.png)

- src / pages / context / components / CounterDisplay.tsx
  ![](/assets/images/react/CounterDisplay_context.png)

- src / pages / context / components / CounterButtons.tsx
  ![](/assets/images/react/CounterButtons_context.png)

### ✅ 장점
- React 기본 제공으로 별도의 라이브러리 설치 없이 사용 가능.
- 코드 구조가 비교적 단순하여 러닝 커브가 낮음.

### ❌ 단점
- Provider에서 value가 변경되면 해당 Context를 사용하는 모든 컴포넌트가 리렌더링.
- 상태가 많아질수록 Context 파일이 커지고, 유지보수가 어려워짐.
- 중첩 Provider 가독성 저하.

## 📌 Redux
**React에서 가장 많이 사용**하는 상태 관리 라이브러리<br/>
특징은 **기존 state를 직접 수정하지 않고, 새로운 state를 생성하여 업데이트**<br/>
**상태 변경은 반드시 Action이라는 객체를 통해**서만 이루어지고, 이 Action은 순수 함수인 Reducer를 거쳐 처리되는데, 
이 과정 덕분에 **어떤 상태가 어떻게 변하는지 예측 가능**

### Redux 상태 변경 경로
- 기존 방식 (props drilling)<br/>
  `A → B → C → D → E → F → G`

- Redux 방식<br/>
  `A → Store → G`<br/>
  **Store를 통해 바로 접근 가능**하며, 불필요한 props 전달이 사라지고 **상태를 직접 읽고 변경**할 수 있어 훨씬 효율적

### Redux 구성
1. Store
   - **상태를 보관**하는 중앙 공간
   - 컴포넌트와는 별개로 존재
   - 필요한 상태 Store 안에 담고, 컴포넌트는 Store에서 상태 정보를 가져옴

2. Action
   - **상태 변경 요청 객체**
   - 일반적으로 `{ type: "INCREASE", payload: 1 }` 형식
   - `dispatch()` 함수를 통해 Store에 전달

3. Reducer
    - **상태 변화**를 만드는 **순수 함수**
    - `(state, action) => newState` 형태
    - 기존 state를 직접 변경하지 않고, **새로운 state 객체를 반환**
    - action의 `type`에 따라 state 업데이트

### Redux 예시
⭐️ 루트 컴포넌트에 Provider로 store를 전역으로 공급해야 컴포넌트들이 Redux를 사용
- src / Main.tsx
  ![](/assets/images/react/Main_redux.png)

- src / store / redux / counterSlice.ts
  ![](/assets/images/react/counterSlice.png)

- src / store / redux / store.ts
  ![](/assets/images/react/store.png)

- src / store / redux / ReduxPage.tsx
  ![](/assets/images/react/ReduxPage.png)

- src / store / redux / CounterDisplay.tsx
  ![](/assets/images/react/CounterDisplay_redux.png)

- src / store / redux / CounterButtons.tsx
  ![](/assets/images/react/CounterButtons_redux.png)

### ✅ 장점
- 전역 상태 관리 라이브러리 중 1위.
- 모든 상태 업데이트를 action으로 정의하고 action 정보에 기반하여 reducer 에서 상태를 업데이트 하기 때문에 상태를 쉽게 예측 가능.
- Redux DevTools 를 통해 상태 관리의 디버깅이 편리

### ❌ 단점
- Hook 기반이 아님.
- 코드가 복잡하고 러닝커브가 높음.
- 보일러플레이트 코드 많음.
- 비동기 요청을 위한 Redux-thunk, Redux-Saga 등의 서드파티 라이브러리가 필요.

## 📌 Recoil
Facebook에서 개발한 React 상태 관리 라이브러리로 사용방식은 useState 와 매우 비슷.<br/>
특징은 **상태를 atom 단위로 관리**하여 필요한 컴포넌트만 해당 상태를 구독하도록 만들 수 있어 **불필요한 리렌더링을 최소화**<br/>
파생 상태를 계산하는 selector 는 **내부적으로 메모이제이션을 지원**하여 입력이 같으면 이전에 계산한 값을 재사용함으로써 **불필요한 연산을 줄일 수 있음**<br/>
또한 별도의 **추가 라이브러리 없이 비동기 상태 관리 지원.**

### Recoil 구성
Atom, Hooks, Selector
1. Atom
   - Recoil에서 관리하는 **하나의 상태 단위**
   - 고유한 key와 기본값을 설정
   - 상태가 변경되면 해당 atom을 구독하는 모든 컴포넌트가 리렌더링
   - 값이 변하지 않으면 구독 중인 컴포넌트는 리렌더링되지 않음
     useRecoilState (Hook)

2. useRecoilState (Hook)
   - `useState`와 동일한 방식으로 atom 값을 읽고 수정할 수 있는 훅

3. useRecoilValue (Hook)
   - atom의 값을 **읽기 전용**으로 가져오는 훅

4. useSetRecoilState (Hook)
   - atom 값을 **수정 전용**으로 설정하는 훅

5. Selector
   - atom에서 **파생된 값을 계산하거나 가공**하는 함수
   - **메모이제이션**을 통해 동일한 입력 값이면 이전 계산 값을 재사용
   - 비동기 처리도 가능

### Recoil 에시
⭐️ 루트 컴포넌트에 `<RecoilRoot>`로 감싸 초기 세팅
- src / Main.tsx
  ![](/assets/images/react/Main_recoil.png)

- src / store / recoil / counterRecoil.ts
  ![](/assets/images/react/counterRecoil.png)

- src / store / recoil / RecoilPage.tsx
  ![](/assets/images/react/RecoilPage.png)

- src / pages / recoil / components / CounterDisplay.tsx
  ![](/assets/images/react/CounterDisplay_recoil.png)

- src / pages / recoil / components / CounterButtons.tsx
  ![](/assets/images/react/CounterButtons_recoil.png)

### ✅ 장점
- useState 훅과 비슷하게 동작하여 직관적이고 간단한 구조
- Selector로 비동기 처리가 가능

### ❌ 단점
- redux 처럼 따로 디버깅툴이 없어 상대적으로 불편
- 전역 상태에 어디서든 직접 접근해 수정할 수 있어, 어떤 컴포넌트가 언제 상태를 변경했는지 추적하기 어려움.
- 의존성이 여러 방향으로 엮이면서 점점 예측 불가

## 📌 Zustand
Redux와 같은 **Flux 패턴(Action → Dispatcher → Store → View)**을 사용하지만, 훨씬 **간소화된** 상태 관리 라이브러리.<br/>
작고 빠르며 확장 가능하고 React Hooks 기반으로 만들어짐.<br/>

특징은 **클로저를 활용하여 스토어 내부 상태를 관리**하므로 특정 라이브러리에 종속되지 않고, 바닐라 자바스크립트 환경에서도 사용 가능.

### Zustand 설치
```terminaloutput
yarn add zustand
```

### Zustand 예시
- 기본 사용
  - create 함수로 스토어 생성
  - create 함수의 콜백은 set, get 매개변수를 가지며, 이를 통해 상태를 변경하거나 조회 할 수 있음.
  - create 함수의 콜백이 반환하는 객체에서의 속성은 상태(State)이고, 메소드는 액션(Action) 이라고 함.
  - create 함수 호출에서 반환하는 스토어 훅(Hook)은,<br/>
    useCounterStore 같이 `use 접두사 + 이름 + Store 접미사`로 명명해서 각 컴포넌트에서 사용

```javascript
import { create } from 'zustand'

export const use이름Store = create((set, get) => {
  return {
    상태: 초깃값,
    액션: 함수
  }
})
```

- src / store / zustand / useCounterStore.ts
  - set, get 매개변수(함수)는 액션에서 사용할 수 있음.
  - get 함수를 호출하면 상태와 액션을 가진 스토어 객체를 얻을 수 있음.
  - set 함수를 호출하면, 상태를 변경할 수 있음.
  - set 함수를 호출할 때 콜백을 사용하면, get 함수를 사용하지 않아도 바로 스토어 객체를 얻을 수 있음. 변경할 상태를 속성으로 포함한 객체를 콜백에서 반환

  ![](/assets/images/react/useCounterStore.png)

- src / pages / zustand / ZustandPage.tsx
  ![](/assets/images/react/ZustandPage.png)

- src / pages / zustand / components / CounterDisplay.tsx
  - 컴포넌트에서 스토어 훅(use이름Store)을 가져와 호출할 때 선택자 함수를 전달해 원하는 상태나 액션을 얻을 수 있음.
  - 상태가 변경되면 컴포넌트 리렌더링
  - 같은 스토어의 다른 상태를 다른 컴포넌트가 불필요하게 리렌더링 되지 않도록, 한 번의 훅 호출로 하나의 상태만 가져와야함.
    
  ![](/assets/images/react/CounterDisplay_zustand.png)

- src / pages / zustand / components / CounterButtons.tsx
  ![](/assets/images/react/CounterButtons_zustand.png)

- 주의 사항
  - 선택자 함수 없이 스토어 훅을 호출하면 개별 상태가 아닌 스토어 객체를 얻을 수 있음.
  - **사용하지 않은 상태가 변경되어도 해당 스토어를 사용하는 모든 컴포넌트가 리렌더링 되기 때문에 주의**

  ![](/assets/images/react/Error_zustand.png)

### ✅ 장점
- redux의 Redux DevTools 로 디버깅 가능.
- 보일러플레이트 감소.
- Provider로 감싸지 않아도 되기 때문에 구조가 단순.
- React Hook을 사용하여 상태를 사용하기 때문에 추가적인 Hook 필요 없음.

### ❌ 단점
- Redux보다 보조 라이브러리나 미들웨어가 부족하기 때문에 이를 보조할 수 있는 다른 수단이 필요 (React Query)

### ⭐️ 사용자 수 증가에 따른 주의사항
사용자 수 가 증가한다고 해서 zustand 자체에 성능적인 문제가 있는 것은 아님.<br/>
하지만 zustand를 단독적으로 사용할 때 사용자 수가 많아질수록 발생할 수 있는 문제로는

- **서버 부하와의 연동 문제**
  - zustand 는 서버 상태 관리 기능이 없기 때문에, 사용자 수가 늘면 API 호출 횟수가 늘어남.
  - 같은 데이터에 대해 여러 사용자가 동시 요청을 보내면 서버 부하가 커지고, 캐싱/동기화 로직을 따로 작성하지 않으면 중복 요청 문제 발생.

→ 트래픽 관련 문제는 **React Query를 사용**해서 문제 해결을 기대해 볼 수 있음.<br/>
캐싱, 중복 요청 제거, 백그라운드 리패칭, 자동 리트라이 같은 기능이 있어 서버 상태 관리 부담이 줄어듬

- 메모리 사용 증가
  - 브라우저 메모리에 zustand store 를 유지하기 때문에 개별 사용자가 사용하는 상태의 크기가 커질수록 브라우저 메모리 부담이 커질 수 있음.

- 빈번한 상태 변경
  - 상태 변경이 자주 일어나면 여러 컴포넌트가 불필요하게 리렌더링 될 수 있음 (클라이언트 성능 저하 초래)

→ 사용자 수와는 무관하게 코드가 커질수록 복잡성이 커지는게 핵심 리스크
**store 구조화** 또는 원활한 협업을 위해 **표준 구조나 패턴 수립 필요.**

> Redux는 구조가 탄탄하지만 복잡한 반면,<br/><br/>
  Recoil은 직관적이고 러닝 커브가 낮아 사용 경험이 좋았음. 그러나 Recoil은 어느 컴포넌트에서든 전역 상태를 직접 변경할 수 있어 상태 변경 추적이 어렵고, 
  atom이 많아지면 유지보수가 어려울 수 있다는 단점이 있다고 생각.<br/><br/>
  따라서, Recoil의 장점과 Redux의 예측 가능성과 구조적 안정성을 결합한 Zustand를 사용해보는 것이 효율적일 것 같음