---
title: Global Layout & 페이지별 Layout
date: 2024-09-12 00:00:01
categories:
- Next
tag:
- Next
---

각 페이지마다 공통적인 레이아웃을 반복해서 정의할 필요가 없으며, 레이아웃 변경 시 하나의 컴포넌트만 수정하면 전체 페이지에 적용.<br/>
[한입 크기로 잘라먹는 Next.js(15+)]

# Global Layout

```javascript
export default function App ({ Component, pageProps }: AppProps) {
    return (
        <GlobalLayout>
          <Component {...pageProps} />
        </GlobalLayout>
    )
} 
```

```javascript
export default function GlobalLayout ({
    children
}: {
    children: ReactNode;
}) {
    return (
        <div className={style.container}>
          <header>
            <Link href={'/'}>ONEBITE BOOKS</Link>
          </header>
          <main className={style.main}>{children}</main>
          <footer className={style.footer}>제작 @zxxmin</footer>
        </div>
    )
}
```

**App 컴포넌트**는 Next.js의 커스텀 App 컴포넌트로, Next.js에서 각 페이지를 렌더링할 때 공통으로 사용되는 엔트리 포인트.<br/>
여기서 `Component` → 현재 렌더링 될 페이지 컴포넌트,<br/>
`pageProps` → 해당 페이지에 전달되는 props

즉, App 컴포넌트는 모든 페이지에 대해 공통적으로 레이아웃을 설정하고 페이지 전환 시에도 유지.<br/>
- **GlobalLayout 컴포넌트**는 모든 페이지에 **공통적으로 사용되는 레이아웃을 정의**
- `Component`가 실제 렌더링 될 페이지, 이를 **GlobalLayout 컴포넌트**의 `children`으로 전달하여 해당 페이지의 콘텐츠를 전역 레이아웃 안에 표시.

## Global Layout 적용의 원리
- _app.tsx에서 전역 레이아웃을 적용하는 것은 모든 페이지가 전역적으로 같은 레이아웃을 사용하도록 보장하기 위한 방법.
- GlobalLayout으로 감싸진 페이지 콘텐츠가 **모든 페이지에서 동일한 스타일과 구조를 유지**할 수 있게 하며,<br/>
  header와 footer 같은 공통된 UI 요소를 재사용.
- 이를 통해, 각 페이지마다 공통적인 레이아웃을 반복해서 정의할 필요가 없으며, **레이아웃 변경 시 하나의 컴포넌틍만 수정**하면 전체 페이지에 적용

<br/>

# 페이지별 Layout
- 어떠한 특정 페이지에서만 보여야한다면, 그 페이지에 들어가서 **getLayout** 메서드 호출

```javascript
import SearchableLayout from '@/components/searchable-layout'
import { ReactNode } from 'react';

export default function Home () {
    // ...
}

Home.getLayout = (page: ReactNode) => {
    return <SearchableLayout>{page}</SearchableLayout>
}
```

## getLayout 메서드
- `page`라는 매개변수로 현재의 페이지 역할을 할 컴포넌트를 받아와서 **SearchableLayout** 이라는 별도의 레이아웃으로 감싸진 형태의 페이지로 리턴해주는 함수
- 해당 페이지 컴포넌트를 SeachableLayout으로 묶어서 리턴.
- [참고자료](https://reactjs.winterlood.com/0f33b159-6b19-433b-8db4-68d6b4a122e0)

```javascript
export default function App({ Component, pageProps }: AppProps) {
  const getLayout = Component.getLayout;

  return (
      <GloabalLayout>
        {getLayout(<Component {...pageProps} />)}
      </GloabalLayout>
  )
}
```

App 컴포넌트에서는 위와 같이 설정.
> 위처럼 설정하면 getLayout 설정을 안해준 페이지는 오류 발생<br/>
  → 병합연산자 사용

```javascript
export default function App({ Component, pageProps }: AppProps) {
  const getLayout = Component.getLayout ?? ((page: ReactNode)=>page);

  return (
      <GloabalLayout>
        {getLayout(<Component {...pageProps} />)}
      </GloabalLayout>
  )
}
```

[다른 사람의 블로그 참고 글](https://velog.io/@skyoffly/%ED%95%99%EC%8A%B5-Next.js-Day-04)