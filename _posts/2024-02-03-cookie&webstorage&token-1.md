---
title: 🍪 Cookie, Web Storage, Token - Cookie
date: 2024-02-03 00:00:01
categories:
  - CS 지식
tag:
  - CS 지식
---

웹 브라우저에 기본적으로 내장되어있는 데이터 저장소가 있다. 자바스크립트 내장 함수만으로 접근가능한,

> 밀리의 서재 API의 header에 token 을 같이 보내야 결과 값을 받을 수 있다.<br/>
  token 값은 5분간 유효하고 5분이 지나면 사용할 수 없기에 값을 저장해놓고 사용 시 token 유무 값을 확인해서 API 통신을 해야한다.<br/><br/>
  로직을 생각하면서 stroage에 저장하는게 적합한지, cookie에 저장하는게 적합한지 생각을 해보았고 장단점에 대해 공부했다.

# Cookie
- 웹 사이트에 접속할 때 생성되는 정보를 담은 작은 텍스트 파일.
- 약 **4KB의 크기 제한**이 있으며 **만료기간 설정 가능**.
- 별도로 삭제처리하거나 유효시간이 만료되지 않는 이상 삭제되지 않고 서버와 통신할 때 자동으로 주고 받음.
- 클라이언트에서 직접 추가, 수정, 삭제 가능.
- 클라이언트에 저장되기 때문에 **보안에 취약**.

# Cookie 목적
- **세션관리**<br/>
  서버에 저장되는 로그인이나 장바구니 같은 기능에 사용
- **개인화**<br/>
  테마나 사용자가 선호하는 값을 세팅
- **트래킹**<br/>
  사용자의 행동을 기록하고 분석

# Cookie 설정
## 저장된 쿠키 정보 확인
- 개발자 도구 → 애플리케이션 → 저장용량 → 쿠키

```javascript
const toeknAPI = async () => {
    return await fetch('api/.../token.json', {
        method: 'POST'
    }).then(response => response.json())
      .then(result => result.access_token);
}

const saveToken = (token) => {
    const cookieString = `token=${token}; path=/; Max-Age=240`;
    
    return document.cookie = cookieString;
}
```

## Cookie 유효기간
보통 쿠키는 유효기간이 설정되어있지 않으면 브라우저가 닫힐 때 쿠키도 함께 사라진다. 
이런 쿠키를 세션 쿠키라고 함.

처음엔 Expire라는 옵션으로 기간을 설정했는데, 다른 옵션을 찾아보다가 Max-Age라는 옵션으로 변경.

### 1. Expire
쿠키의 **유효 일자**를 설정, **날짜/시간 값으로 설정**. 설정한 해당 날짜의 시간에 쿠키가 만료.<br/>
Expires 는 반드시 GMT(Greenwich Mean Time) 포맷으로 설정.

```javascript
const saveToken = (tokenResponse) => {
		const expiresDate = new Date();
		expiresDate.setMilliseconds(270000); // 4분 30초
		
    const cookieString = `token=${tokenResponse}; expires=${expiresDate.toUTCString()}; path=/;`;

    return document.cookie = cookieString;
}
```

토큰의 유효시간은 5분이였고, 유효시간보다 여유롭게 체크하기 위해 4분 30초로 설정을 하고 싶었다. setMilliseconds 라는 속성을 사용하였다.<br/>
다른 방법으로는 `new Date()` 설정 시 **+ 86400e3**을 해주는 것이다.

```javascript
let date = new Date(Date.now() + 86400e3);
date = date.toUTCString();
document.cookie = `user=John; expires=${date}`;
```

> **86400e3**<br/>
→ ‘86400’은 하루(24시간)의 초 단위 값. (24시간 * 60분 * 60초 = 86400초)
→ ‘e3’은 지수 표기법. ‘10^3’ (1000)을 의미.
→ ‘86400e3’은 ‘86400 * 1000’으로, 86400000 밀리초 (1일)을 의미.

- 설정된 값
```bash
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2023 07:28:00 GMT; Path=/
```

### 2. Max-Age
쿠키의 **만료 시간**을 설정. 현재부터 만료기간까지의 브라우저에서 유지되는 시간을 **초 단위로 설정**.<br/>
Max-Age=3600 이라고 하면 브라우저에 저장 후 1시간 동안 유지.

- 설정된 값
```bash
Set-Cookie: id=a3fWa; Max-Age=3600; Path=/
```

- **0 이나 음수 값을 설정**하면 쿠키는 **바로 삭제**

```javascript
// 1시간 뒤 쿠키 삭제
document.cookie = 'User=John; max-age=3600';

// 만료 기간을 0으로 지정하여 쿠키 바로 삭제
document.cookie = 'User=John; max-age=0';
```

### MaxAge vs Expires 차이
- MaxAge
  - 상대적인 시간을 사용
  - 클라이언트와 서버 사이의 시간 차이에 영향을 받지 않음.
  
- Expires
  - 특정 시간대로 설정되어 있음.
  - 클라리언트와 서버 사이에 시간 차이가 생길 경우 문제가 발생할 수 있음.

> 위의 설명대로 **5분 뒤 만료** 라는 것을 생각해보면 날짜나 시간이 바뀌는게 아니기 때문에, **Max-Age**로 설정하는게 더 적합했던 것 같다.

[참고 블로그](https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-%EC%BF%A0%ED%82%A4Cookie-%EB%8B%A4%EB%A3%A8%EA%B8%B0#%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8_%EC%BF%A0%ED%82%A4_%EB%AC%B8%EB%B2%95)