---
title: 📁 GNB 메뉴 추가/수정/삭제 기능 추가
date: 2025-03-01 00:00:01
permalink: /gyeonggi/menu
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
- 위 방법대로 app 폴더 하위에 동적 폴더를 생성했더니, **URL을 임의로 작성을 해도 페이지가 노출**되고 있음. 

# 분석 2.
- 동적 폴더가 있으면 프레임워크에서 정적으로 생성된 폴더의 경로를 제외하고 다른 경로는 모두 동적 경로의 페이지를 노출해주고 있기 때문에, 
  임의로 URL을 작성하면 **정적으로 설정된 폴더의 이름과 맞지 않아서** 동적 폴더로 생성된 페이지를 노출.

# 해결 방법
- 동적 폴더의 page.tsx 에서 설정된 메뉴를 확인하는 API 호출 후 **설정된 URL이 없으면 404 페이지**로 보일 수 있게 처리.

```javascript
export default async function page({ params }: { params: PageParams }) {
  const decodedId = `/${decodeURIComponent(params.subId)}`;

  try {
    const { data } = await axios.get(`${process.env.NEXT_PUBLIC_MENU_LIST_URL}/api/v1/common/menu/list`);
    const transformedMenu = transformMenu(data);
    const matchedItem = findMenuItemByHref(transformedMenu, decodedId);

    if (!matchedItem) {
      notFound();
    }

    return <BoardCommonPage url={decodedId} />;
  } catch (error) {
    console.error('Error:', error);
    notFound();
  }
}
```

# 결과
- 사용자가 관리자 페이지에서 메뉴를 추가하면, 이상없이 새로운 메뉴가 생기는 것을 확인.

Dynamic Routes가 이렇게 범용적으로 사용할 수 있는지 몰랐다. 처음엔 Catch-all Segments 방법을 사용해야겠다고 생각을 했는데 
글쓰기 페이지나 상세보기 페이지 같은 폴더들을 같이 사용해야해서 Dynamic Routes를 사용하게 되었다.  
GNB 메뉴 추가로 인해서 Dynamic Routes와 Catch-all Segments를 공부하게 되어 좋았다.