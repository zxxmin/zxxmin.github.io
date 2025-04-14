---
title: ♾️ 무한 스크롤 (Infinite Scroll)
date: 2024-07-01 00:00:01
categories:
- 트러블슈팅
- 다채움
tag:
- 트러블슈팅
- 다채움
---

# 문제
- 과제방, 커뮤니티 페이지 진입 시 로딩 **속도가 너무 느리다**는 문의사항

# 원인
- 과제방, 커뮤니티 페이지가 **페이징 처리가 되어있지 않아** 입장과 동시에 모든 첨부파일과 댓글들을 불러오는 구조<br/>
  → 데이터가 많으면 페이지 오류가 발생한 것 처럼 **로딩 속도가 너어어어무 느리다.**

# 방법
- 사이트의 다른 페이지에서 페이지네이션을 사용하고 있지않고 **스크롤이 끝까지 내려오면 데이터를 불러오는 무한 스크롤 기능** 사용하기에, 
  같은 기능으로 구현.

## scroll 이벤트 vs intersection observer
> [scroll 이벤트와 intersection observer의 비교](https://velog.io/@khy226/intersection-observer%EB%9E%80-feat-%EB%AC%B4%ED%95%9C-%EC%8A%A4%ED%81%AC%EB%A1%A4-%EB%A7%8C%EB%93%A4%EA%B8%B0)

짧게 정리하면, scroll 이벤트의 경우 단시간에 수백번 호출이되며 동기적으로 실행이 되고,
스크롤할 때마다 이벤트가 끊임없이 호출되기 때문에 몇배로 성능 문제가 발생.<br/>
또한 특정 지점을 관찰하는 `getBoundingClientRect()` 역시 계산을 할 때마다 **리플로우 현상이 일어난다**는 단점이 있다고 한다.

## Reflow 리플로우
- 브라우저가 **DOM 요소의 레이아웃을 다시 계산하고 재배치**하는 과정
- 어떤 요소의 크기나 위치가 바뀌면 브라우저는 전체 또는 일부 레이아웃을 다시 계산

### Reflow가 일어나는 경우
- 페이지 초기 렌더링 시 (최초 레이아웃 과정)
- 브라우저 리사이징 시 (Veiwport 크기 변경)
- 노드 추가 또는 제거
- DOM 노드의 위치 변경
- DOM 노드의 크기 변경
- 요소의 위치, 크기 변경
- 폰트 변경과 이미지 크기 변경

### Reflow가 안좋은 이유
- **성능 저하**<br/>
  → CPU 자원을 많이 사용<br/>
  → 특히 DOM 요소가 많거나 중첩 구조가 깊은 경우, 전체 문서를 다시 그려야 해서 무겁고 느려짐.
- 잦은 Reflow는 브라우저 렉의 원인.
- Repaint까지 발생 시 **더 느려지고 렌더링 비용이 더 커짐.**
- 체인 리액션 발생 가능<br/>
  → 어떠한 요소 하나의 변경이 **연쇄적으로 다른 요소들까지 영향**을 미쳐서 전체 레이아웃을 다시 계산.

#### Reflow는 비싼 작업
브라우저 렌더링 과정
> JS, CSS 파싱 → 렌더 트리 구축 → Layout(Reflow) → Paint(Repaint) → 레이어 업데이트 → Composite

위 브라우저 렌더링 흐름에 따라서 Reflow가 발생하면 Repaint와 Composite 과정을 모두 수행하게 되므로 렌더링 비용이 커진다.<br/>
Reflow는 웹 페이지 렌더링 성능에 **직접적인 영향을 미치는 작업**이기 때문에, **최대한 줄이고, 한 번에 처리하는 방식으로 최적화** 하는 것이 좋음.

## intersection observer
intersection observer를 사용하면 IntersectionObserverEntry의 속성을 활용하여 요소들의 위치를 알 수 있기 때문에 **리플로우 현상을 방지**

# 해결
## 해결 1.
```javascript
let currentPage = 1;
let isLoading = false;
let lastHomwork = null;
const post_list_area = document.querySelector('.post_list_area');

// observer 생성
const infiniteScroll = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting && !isLoading) {
		        // 작성된 내용 불러오는 api + 동적생성
            loadArticle();
        }
    })
})

document.addEventListener('DOMContentLoaded', async function () {
    await loadArticle();

		// 관찰 대상!! 관찰 할지 안할지
    const nextHomework = post_list_area.querySelector('section.cmt-area:last-child');
    if(nextHomework !== null) {
        if (lastHomwork !== null) {
            infiniteScroll.unobserve(lastHomwork);
        }
        lastHomwork = nextHomework;
        infiniteScroll.observe(lastHomwork);
    }

});

const loadArticle = async () => {
    isLoading = true;

    const url = "api address";
    const params = {
        // ...
		    
        "page" : currentPage,
    }
    
    try {
        const apiResponse = await GET_FETCH(url, params);

        if(apiResponse.success){
            if (apiResponse.data.resultList.length > 0) {
                //  제출과제 동적 ui 생성
                createHomeworkPostUI(apiResponse.data.resultList);

                currentPage++;
            }
        }

    } catch (e) {
        console.log(e);
    } finally {
		    // 성공을 했던 안했던 결과는 로딩 풀리게
        isLoading = false;
    }
}
```

위 방식대로 작성했을 때 스크롤 구현은 잘 되었다.<br/>
하지만 데이터를 다 불러왔는데도 **스크롤을 올렸다가 내리면 api를 계속 호출하고 있는 문제 발생**

![](/assets/images/troubleshooting/dcu-1-1.png)

## 해결 2.
더이상 데이터를 호출하지않기 위해 **isEnd 플래그**를 넣었다. 
추가로 DOM 로드 시에만 관찰 대상이 관찰이 되기 때문에 함수로 빼서 **API 호출 시에도 관찰할 수 있게 추가**했다.

```javascript
let currentPage = 1;
let isLoading = false;
let isEnd = false;
let lastHomwork = null;
const post_list_area = document.querySelector('.post_list_area');

const infiniteScroll = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting && !isLoading && !isEnd) {
            loadArticle();
        }
    })
})

document.addEventListener('DOMContentLoaded', async function () {
    await loadArticle();

    setupNextHomeworkObserver();

});

const setupNextHomeworkObserver = () => {
    const nextHomework = post_list_area.querySelector('section.cmt-area:last-child');
    if(nextHomework !== null) {
        if (lastHomwork !== null) {
            infiniteScroll.unobserve(lastHomwork);
        }
        lastHomwork = nextHomework;
        infiniteScroll.observe(lastHomwork);
    }
}

const loadArticle = async () => {
    if (isLoading || isEnd) return;
    isLoading = true;

    const url = "api address";
    const params = {
        //...
		    
        "page" : currentPage,
    }
    
    try {
        const apiResponse = await GET_FETCH(url, params);

        if(apiResponse.success){
            if (apiResponse.data.resultList.length > 0) {
                //  제출과제 동적 ui 생성
                createHomeworkPostUI(apiResponse.data.resultList);

                setupNextHomeworkObserver();

								// 한 번에 로드되는 항목 수 보다 작으면 데이터 끝
                if (apiResponse.data.resultList.length < 5) { 
                    isEnd = true;
                }

                currentPage++;

            } else {
		            // 빈 결과가 반환되면 데이터 끝
                isEnd = true;
            }
        }

    } catch (e) {
        console.log(e);
    } finally {
        isLoading = false;
    }
}
```

# 결과
스크롤 기능 하나로 로딩 속도가 최적화 되었다. 대략 5초가 넘어가는 로딩 속도에서 한번에 5개씩 받아올 수 있어서 약 2초도 안되는 시간으로 **60% 정도 성능을 최적화** 하였다.<br/>
생각보다 scroll 이벤트보다 더 쉽게 구현할 수 있었다.<br/>
뷰포트에 들어오는 요소를 관찰 후 관찰이 되었을 때 무언가를 할 수 있다는 개념이 신기했다.

> MDN 에서 intersection Observer 를 사용할 수 있는 상황들
  - 페이지가 스크롤 되는 도중에 발생하는 이미지나 다른 컨텐츠의 지연 로딩 (lazy loading)
  - 스크롤 시에, 더 많은 컨텐츠가 로드 및 렌더링되어 사용자가 페이지를 이동하지 않아도 되게 하는 infinite-scroll 을 구현
  - 광고 수익을 계산하기 위한 용도로 광고의 가시성 보고
  - 사용자에게 결과가 표시되는 여부에 따라 작업이나 애니메이션을 수행할 지 여부를 결정.

**찾아본 블로그들**
- [실무에서 느낀 점을 곁들인 Intersection Observer API 정리](https://velog.io/@elrion018/%EC%8B%A4%EB%AC%B4%EC%97%90%EC%84%9C-%EB%8A%90%EB%82%80-%EC%A0%90%EC%9D%84-%EA%B3%81%EB%93%A4%EC%9D%B8-Intersection-Observer-API-%EC%A0%95%EB%A6%AC)
- [무한 스크롤(Infinite scroll) 구현하기](https://velog.io/@eunoia/%EB%AC%B4%ED%95%9C-%EC%8A%A4%ED%81%AC%EB%A1%A4Infinite-scroll-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0)
- [intersection observer란? (feat: 무한 스크롤 만들기)](https://velog.io/@khy226/intersection-observer%EB%9E%80-feat-%EB%AC%B4%ED%95%9C-%EC%8A%A4%ED%81%AC%EB%A1%A4-%EB%A7%8C%EB%93%A4%EA%B8%B0#intersection-observer%EB%9E%80)
- [JS로 무한 스크롤 구현하기](https://velog.io/@boseong-choi/JS%EB%A1%9C-%EB%AC%B4%ED%95%9C-%EC%8A%A4%ED%81%AC%EB%A1%A4-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0)
- [[JavaScript] 무한 스크롤 구현하기(Intersection Observer API)](https://leeseong010.tistory.com/145)
- [VanillaJs로 무한 스크롤 구현하기(feat: Intersection Observer)](https://aeunhi99.tistory.com/321)
- [Intersection Observer API로 무한 스크롤 구현하기](https://nohack.tistory.com/124)