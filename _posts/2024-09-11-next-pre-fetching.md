---
title: Pre-Fetching
date: 2024-09-11 00:00:02
categories:
- Next
tag:
- Next
---

사용자가 보고있는 웹 페이지에서 이동할 가능성이 있는 모든 페이지의 JS 코드를 미리 불러오느 과정.<br/>
[한입 크기로 잘라먹는 Next.js(15+)]

# Pre Fetching
- 네비게이팅과 연관
- 현재 사용자가 보고 있는 페이지에서 **이동할 가능성이 있는 모든 페이지들을 사전에** 미리 불러놓는 기능.
- **빠른 페이지 이동**을 위해 제공되는 기능

![](/assets/images/next/next-pre-fetching-1.png)

## Pre Fetching의 필요성

> Next.js 는 사전 렌더링 방식이라 브라우저가 서버에 추가적인 리소스를 요청할 필요가 없음.
  근데 왜 Pre Fetching이 필요한가? 추가적인 데이터를 왜 불러와야 하나??

- Next.js → 컴포넌트들을 자동으로 페이지별로 분리해서 저장.

사전 렌더링 과정에서 JS Bundle을 전달할 때, 모든 페이지에 대한 JS 번들이 전달되는게 아니라 **현재 페이지에 필요한 JS Bundle만 전달**된다.<br/>
ex) '/search' 접속 요청 → Search JS Bundle 전달

만약, 초기 접속 시 모든 페이지의 Bundle 파일을 전달할 경우 용량이 너무 커지게 되며 Hydration이 늦어진다.<br/>
→ 즉, TTI가 최종적으로 늦어짐.

그래서 현재 페이지에 필요한 JS만 보내줌.<br/>
→ 초기 접속 후 페이지 이동 시 JS Bundle을 또 보내면 비효율적인 페이지 이동이 되므로 **Pre Fetching** 필요.

![](/assets/images/next/next-pre-fetching-2.png)

## Pre Fetching 사용방법
- Pre Fetching 기능은 기본적으로 Link 컴포넌트로 연결된 페이지에서만 동작.
- 프로그래매틱한 페이지 이동 방식에서는 prefetch 메소드 사용.

### 프로그래매틱한 페이지에 Pre Fecthing 설정

```javascript
useEffect(() => {
    router.prefetch('/test')
}, [])
```

### Link 컴포넌트에 Pre Fetching 강제 해지

```javascript
import Link from "next/link"

export default function App () {
    return (
        <header>
          <Link href={'/'}>index</Link>
          <Link href={'/search'} prefetch={false}>search</Link>
          <Link href={'/book/1'}>book/1</Link>
        </header>
    )
}
```