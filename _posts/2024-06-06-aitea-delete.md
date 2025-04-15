---
title: 🗑️삭제가 안되고 있는 이슈.
date: 2024-06-06 00:00:01
categories:
- 트러블슈팅
- AI 도움쌤
tag:
- 트러블슈팅
- AI 도움쌤
---

# 문제
- 삭제하기 버튼 클릭 시 "삭제에 실패하였습니다" 라는 alert 창이 뜨는 이슈.

# 원인
- 비동기 처리를 하지 않았던 것. 즉, fetch 앞에 await을 붙이지 않았던 것.

API 호출 방식의 문제인가 싶어서 백엔드 개발자분과 java 파일부터 디버깅을 했으나, 문제가 없었다. 
fetch 부분을 console.log로 찍은 결과 **PromiseStatus가 pending 상태**였다.

```javascript
const POST_FETCH = async (url, data) => {
    const param = {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(data)
    }

    return await fetch(url, param)
        .then(response => response.json())
        .catch(error => {
            throw error;
        })
}

btn.addEventListener('click', async () => {
    const apiResponse = POST_FECH(url, param);
}
```

# 방법
- fetch 호출하는 부분 앞에 await를 추가하기.
- POST_FETCH 함수 앞에 await 키워드를 작성

## 왜 await을 안붙이면 삭제 처리가 되지 않을까?
**async** : 함수 앞에 async를 붙이면 그 함수는 **항상 Promise를 반환**한다. 함수 내부에서 반환되는 값은 자동으로 Promise로 감싸진다.

**await** : await는 **Promise가 완료될 때까지 함수 실행을 일지 중지**한다. Promise가 완료되면 await은 해당 Promise의 결과 값을 반환한다.

즉, await 없이 fetch를 사용하면 fetch 함수가 반환하는 **Promise가 즉시 반환**하게 되어서 PromiseStatus가 pending 값이 나오게 되었고,<br/>
**네트워크 요청 결과를 제대로 처리할 수 없어서** apiResponse.code의 값을 받을 수 없어 else 문을 타게 된 것 이다.<br/>
위와 같은 이유로 await을 사용하여 Promise가 완료 될 때 까지 기다려야한다.

[async/await 문법 정복](https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-%EB%B9%84%EB%8F%99%EA%B8%B0%EC%B2%98%EB%A6%AC-async-await)

# 결과
- 삭제를 실패하지 않고 이상없이 되는 것을 확인. 
 
너무 어이없는 실수였지만, 너무 당연한 부분이라고 생각해서 오류 찾는데 오랜 시간이 걸렸다. 
당연한 것에 대한 실수를 줄일 수 있도록 해야겠다.