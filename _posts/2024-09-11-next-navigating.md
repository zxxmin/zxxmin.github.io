---
title: Navigating
date: 2024-09-11 00:00:01
categories:
- Next
tag:
- Next
---

페이지 이동 방법<br/>
[한입 크기로 잘라먹는 Next.js(15+)]

# 링크로 페이지 이동
- SSR → a 태그
- CSR → Link 컴포넌트

a 태그 보다는 Link 컴포넌트 사용
```javascript
import Link from "next/link"

export default function App () {
    return (
        <header>
          <Link href={'/'}>index</Link>
          <Link href={'/search'}>search</Link>
          <Link href={'/book/1'}>book/1</Link>
        </header>
    );
}
```

<br/>

# 프로그래매틱한 페이지 이동
- 사용자가 링크를 직접 클릭하는 방식이 아닌, **특정 버튼이 클릭 되었거나 특정 조건이 만족했을 경우** 함수 내부에서 페이지 이동

```javascript
import { useRouter } from "next/router"; 

export default function App () {
    const router = useRouter();
    
    const onClickBtn = () => {
        rotuer.push('/test');
    }
    
    return (
        <div>
            <button onClick={onClickBtn}>/test 페이지로 이동</button>
        </div>
    )
}
```

리액트의 **useNavigation**과 같은 역할

## useRouter의 라우팅 메서드
- push() : 새로운 페이지로 이동, 히스토리에 추가.
- replace() : 현재 페이지를 새로운 페이지로 교체, 히스토리에 기록되지 않기 때문에 **뒤로가기 방지** 효과.
- back() : 브라우저 히스토리 스택을 기반으로 **이전 페이지로 이동**