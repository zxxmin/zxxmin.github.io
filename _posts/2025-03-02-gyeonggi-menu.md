---
title: 📁 GNB 메뉴 추가/수정/삭제 기능 추가
date: 2025-03-01 00:00:01
categories:
- 트러블슈팅
- 꿈it(잇)다
tag:
- 트러블슈팅
- 꿈it(잇)다
---

# 개요 1.
- 사용자가 관리자 페이지에서 GNB 메뉴를 추가, 수정, 삭제하는 기능이 되기를 원하는 상황.
- 메뉴 추가에 따른 하위 페이지(게시판 형태의 페이지)들도 생성이 되고, 기능들이 되어야 함.

# 분석 1.
- 프로젝트가 Next.js의 App Router를 사용 중으로 앱 폴더 밑에 구조를 기반으로 페이지 라우팅이 자동 설정이 되는 구조로 
  폴더가 경로가 되고, 'page'라는 이름의 파일이 페이지 역할을 하는 파일로 자동 설정.

# 해결 방법
- Dynamic Routes 사용

미리 세그먼트의 정확한 이름을 알지 못하거나 다이나믹 데이터를 바탕으로 라우트를 만들고 싶다면 리퀘스트 타임이나 빌드 타임 때 
프리렌더링 과정에서 생성 되는 다이나믹 세그먼트를 사용하면 좋음.

다이나믹 세그먼트는 폴더 이름을 대괄호로 감싸 구현할 수 있다. `[폴더 이름]`

[nextjs.org-Dynamic Rotues](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes)

- app 폴더 하위에 [] 를 넣은 폴더 이름을 생성 후 하위에 게시판 컴포넌트를 추가한 페이지들 생성.

```bash
└─ app
    ├─ (메뉴)
    │  └─ board
    │     ├─ [id]
    │     │  └─ page.tsx
    │     ├─ write
    │     │  └─ page.tsx
    │     └─ page.tsx
    └─ [submenu]
       ├─ [id]
       │  └─ page.tsx
       ├─ write
       │  └─ page.tsx
       └─ page.tsx
```

## Dynamic Routes vs Catch-all Segments
- Dynamic Routes : [ 폴더이름 ]

  | Rotues | Example URL | `params`      |
  | --- |-------------|---------------|
  | app/blog/[slug]/page.js | /blog/a | { slug: 'a' } |
  | app/blog/[slug]/page.js | /blog/b | { slug: 'b' } |
  | app/blog/[slug]/page.js | /blog/c | { slug: 'c' } |

- Catch-all Segments : [ ...폴더이름 ]

  | Rotues | Example URL | `params`      |
  | --- |-------------|---------------|
  | app/shop/[...slug]/page.js | /shop/a     | 	{ slug: ['a'] } |
  | app/shop/[...slug]/page.js | /shop/a/b     | 	{ slug: ['a', 'b'] } |
  | app/shop/[...slug]/page.js | /shop/a/b/c     | { slug: ['a', 'b', 'c'] } |

쉽게 표현하자면 [폴더이름] 으로 사용하면 하위 1개의 세그먼트에만 [폴더이름] 하위의 page.tsx가 관여할 수 있다.  
app/blog/a 또는 app/blog/b 처럼 [폴더이름] 경로의 위치 **세그먼트가 1개 일 때 [폴더이름] 하위의 page.tsx 가 보이고,**  
app/blog/a/b 처럼 경로에 **세그먼트가 2개 일 때 [폴더이름] 하위의 page.tsx가 보이지 않는다.**

Catch-all Segments [...폴더이름] 으로 사용하면 하위의 모든 세그먼트에 [폴더이름] 하위의 page.tsx가 관여할 수 있다.  
app/shop/a 이거나 app/shop/a/b 처럼 경로에 **세그먼트가 여러개 일 때 [폴더이름] 하위의 page.tsx가 보여진다.**

# 개요 2.
- 

# 분석 2.
-

# 해결 방법
-

# 결과
- 