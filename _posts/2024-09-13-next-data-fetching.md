---
title: Data Fetching
date: 2024-09-13 00:00:01
categories:
- Next
tag:
- Next
---

Next Data-Fetching은 사전 렌더링 중 발생<br/>
사전 렌더링이 오래걸릴 것 같은 경우에만 빌드타임에 미리 사전 렌더링을 맞춰둘 수 있는 등 다양한 방법 제공<br/>
[한입 크기로 잘라먹는 Next.js(15+)]

# React Data Fetching

```javascript
export default function Page () {
    // 1. 불러온 데이터를 보관할 State 생성
    const [ state, setState ] = useState();
    
    // 2. 데이터 페칭 함수 생성
    const fetchData = async () => {
        const response = await fetch("...");
        const data = await response.json();
        
        setState(data);
    };
    
    // 3. 컴포넌트 마운트 싲머에 fetchData 호출
    useEffect(() => {
        fetchData();
    }, []);
    
    // 4. 데이터 로딩 중일 때의 예외처리
    if(!state) return "Loading ...";
    
    return <div>...</div>
}
```

1. 불러온 데이터를 보관 할 state 생성
2. 데이터 페칭 함수 생성
3. 컴포넌트 마운트 시점에 fetchData 호출
4. 데이터 로딩 중 일 때의 예외 처리

## 단점
- 초기 접속 요청부터 데이터 로딩까지 오랜 시간이 걸림.<br/>
  → 이유 : 백엔드 서버에게 보내는 요청이 3번 시점(마운트 시점) 이후에 발생하기 때문.<br/>
  → FCP 초기 접속 자체도 느린데 그 후 백엔드 서버에게 데이터를 요청하기 때문에 추가적인 시간이 소요.

# Next Data Fetching
- 사전렌더링이 오래걸릴 것 같은 경우에만 빌드 타임에 미리 사전 렌더링을 맞춰 둘 수 있는 등 다양한 방법 제공
- 빌드 타임에 모든 걸 렌더링하지 않아도 되게끔, **"미리 렌더링할지, 요청 시 렌더링할지, 캐시할지"** 등을 상황에 맞게 **유연하게 선택**할 수 있다

# React VS Next

| React Data-Fetching | Next Data-Fetching |
|---------------------|--------------------|
| 컴포넌트 마운트 이후 발생 | 사전 렌더링 중 발생<br/>→ 당연히 컴포넌트 마운트 이후에도 발생 가능 |
| 데이터 요청 시점이 느려지게 되는 단점 | 데이터 요청 시점이 매우 빨라지는 장점 |

## 사전 렌더링 방법
1. SSR (서버 사이드 렌더링)
   - 가장 기본적인 사전 렌더링 방식
   - 요청이 들어올 때 마다 사전 렌더링을 진행
2. SSG (정적 사이트 생성)
   - 빌드 타임에 미리 페이지를 사전 렌더링
3. ISR (증분 정적 재생성)
   - 향후에 다룰 사전 렌더링 