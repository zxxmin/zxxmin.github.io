---
title: Page Router
date: 2024-09-10 00:00:01
categories:
- Next
tag:
- Next
---

Pages 폴더의 구조를 기반으로 페이지 라우팅 기능<br/>
[한입 크기로 잘라먹는 Next.js(15+)]

#  Page Router
- React Router 처럼 페이지 라우팅 기능을 제공
- Pages 폴더의 구조를 기반으로 페이지를 라우팅
- Pages 폴더 아래에 JS 파일들을 두게되면 **자동으로 파일들이 경로와 이름으로 페이지 라우팅**

## Page 설정 방법
### 1. **파일명 기반**의 페이지 라우팅
![](/assets/images/next/next-page-router-1.png)

### 2. **폴더명 기준**의 페이지 라우팅
![](/assets/images/next/next-page-router-2.png)

### 3. **동적 경로**를 갖는 페이지 라우팅
   **동적 경로** → 경로상의 변할 수 있는 가변적인 값을 포함 : 블로그의 게시물 등
![](/assets/images/next/next-page-router-3.png)

<br/>

# Query String & Url Param
- useRouter 훅 사용

## Query String
- 경로 끝에 **?** 로 이어짐

```javascript
import { useRouter } from "next/rotuer"

export default function Page () {
    const router = useRouter();
    const { q } = router.query;
    
    return <h1>Search {q}</h1>
}
```

![](/assets/images/next/next-page-router-5.png)

> but 콘솔에 값이 두번 출력되고 있다.
  → 이유 : next에서 Query String을 읽는 과정에서 컴포넌트를 한번 더 렌더링
![](/assets/images/next/next-page-router-4.png)

## Url Parameter
- 동적 경로. 경로 끝에 **/** 로 이어짐
- 폴더 밑에 [] 대괄호 페이지 생성.

```javascript
import { useRouter } from "next/router"

export default function Page () {
    const router = useRouter();
    const { id } = router.query;
    
    return <h1>Book {id}</h1>
}
```

![](/assets/images/next/next-page-router-6.png)

#### 범용적인 페이지 : CAS (Catch All Segment)
- [...id] : ... 을 붙임
- 배열 형태로 값 전달

#### 더 범용적인 페이지 : OCAS (Optional Catch All Segment)
- [[...id]] : 대괄호를 한번 더 사용
- 폴더 밑에 index.tsx가 없을 경우 다른 설정을 하지 않고 사용.
  경로 뒤에 어떤 경로가 오든 안오든 다 대응 가능.

<br/>

# Not Found 페이지
- 404.tsx 로 페이지를 만들면 끝.