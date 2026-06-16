---
title: 메뉴 drag and drop 시 화면 변경이 되기 전 alert 창이 먼저 띄워지는 이슈
date: 2025-12-22 00:00:01
permalink: /base/request-animation-frame
categories:
  - 트러블슈팅
  - base-project
tag:
  - 트러블슈팅
  - base-project
---

# 개요
- [메뉴 관리 페이지] 메뉴 drag and drop → 메뉴 변경 후 저장 API 호출 → 저장이 완료되었다는 alert을 띄우는 로직 작성
- 메뉴 drag and drop 후 화면이 변경되고 alert을 띄우고 싶었으나, 화면 변경이 되기 전에 alert 창이 먼저 띄워지는 이슈 발견

<video src="/assets/images/baseProject/request-animation-1.mov" controls width="100%"></video>

---

# 분석
- state/setState 상태 업데이트 시 다음 렌더링 시 실행하는 이슈

    ```javascript
    const handleMove: MoveHandler<MenuListData> = ({
        dragIds,
        dragNodes,
        parentId,
        parentNode,
        index,
    }) => {
        setMenuData(prevData => {
            const newData = JSON.parse(JSON.stringify(prevData));
    
            return newData;
        });
        
        const payloade = {
            id: dragIds[0],
            upId: parentId,
            depth: parentNode?.data.depth && parentNode.data.depth + 1,
        }
        alert("저장되었습니다.");
    }
    ```

  - handleMove() 호출 → setMenuData 상태 업데이트 예약 → alert 실행 → 렌더링 → DOM 업데이트 & paint
  - setMenuData 는 상태 변경을 예약할 뿐 즉시 DOM을 갱신하지 않고, alert 는 메인 스레드를 동기적으로 차단해 렌더링과 paint가 진행되지 못함.
    그 결과“상태변경 → alert → 화면 변경” 순서로 보임.]

    ![](/assets/images/baseProject/request-animation-2.png)

- useEffect 활용하여 menuData가 바뀔 때 저장 및 alert을 띄우는 방식으로 했어도, 비동기로 인한 이슈 발생

---

# 해결 방법
## 방법 1. 타이머 함수
- setTimeout을 사용하여 menuData 상태 업데이트 후 DOM 업데이트 & paint 되는 시간을 억지로 늘려서 맞춤
- 주어진 시간 내에 동작을 할 뿐 프레임을 신경쓰지 않고 동작하므로, 프레임 시작 시간에 맞춰 실행되지 않고 정해진 시간 후 이벤트가 발생하므로 지연 등 문제 발생 가능성이 높음

## 방법 2. requestAnimationFrame (rAF)
시스템이 프레임을 그릴 준비가 되면 애니메이션 프레임을 호출하여 애니메이션 웹페이지를 보다 원활하고 효율적으로 생성할 수 있도록 해줌.<br/>
실제 화면이 갱신되어 표시되는 주기에 따라 함수를 호출해주기 때문에 자바스크립트가 프레임 시작 시 실행되도록 보장해주어 밀림 현상을 방지.<br/>
프레임을 페인팅할 준비가 됐을 때 호출.

즉, **브라우저가 웹 페이지의 콘텐츠를 화면에 표시하기 전에 실행하는 함수**

### 장점
**백그라운드 동작 중지**
- 타이머 함수같은 경우 브라우저의 다른 탭 화면을 보거나 브라우저가 최소화되어 있을 때 계속 타이머가 돌아 콜백을 호출하기 때문에 리소스 낭비를 초래하고 불필요 전력을 소모. 반면 rAF는 **페이지가 비활성화 된 상태이면 페이지 화면 그리기 작업도 브라우저에 의해 일시 중지**됨으로 CPU 리소스나 배터리 수명을 낭비하지 않게 됨.

**디스플레이 주사율에 맞게 호출 횟수**
- 브라우저는 디스플레이의 주사율을 따름.
- 타이머 함수는 콜백 함수 호출 간격을 시간을 지정해주고 호출 횟수를 설정. 반면 rAF에서는 **모니터의 주사율을 그대로 따르게 되어 최적화.** 이러한 특성으로 인해 스크롤 이벤트 최적화 기법으로 쓰임

### 사용 방법
콜백 함수 내부에서 재귀 호출하는 식으로 구성.

브라우저는 애니메이션 프레임을 출력할 때마다 **requestAnimationFrame에 등록된 콜백 함수들을 비동기로 호출하여, 애니메이션을 부드럽게 출력**

### 재귀 호출의 이유
- **단일 호출**
```javascript
requestAnimationFrame(() => {
    alert("저장되었습니다.")
})
```

이 코드는 rAF에 전달된 콜백 함수가 **브라우저의 다음 애니메이션 프레임이 시작되기 전 실행.**

일반적으로 현재 프레임이 끝난 후, 다음 프레임이 시작되기 전 시점. 즉, **브라우저가 현재 작업을 완료하고, 렌더링을 준비하기 전에 콜백 호출**

- **재귀적으로 두 번 호출하는 경우**
```javascript
requestAnimationFrame(() => {
    requestAnimationFrame(() => {
        alert("저장되었습니다.")
    }
})
```

이 코드는 첫번째 rAF 콜백이 브라우저의 다음 애니메이션 프레임이 시작되기 전 실행. 그러나 이 콜백 내 또 다른 rAF가 호출되므로, 두 번째 콜백은 그 다음 애니메이션 프레임이 시작되기 전에 실행.

따라서 **두 번째 콜백은 첫 번째 콜백이 실행된 프레임 다음의 프레임에서 실행**

---

# 결론
- 화면 적용 후 alert 창이 띄워지는 것을 확인할 수 있음.

<video src="/assets/images/baseProject/request-animation-3.mov" controls width="100%"></video>