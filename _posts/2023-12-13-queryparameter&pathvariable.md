---
title: 🔗 Query Parameter, Path Variable
date: 2023-12-13 00:00:01
permalink: /cs/query&path
categories:
- CS 지식
- ETC
tag:
- CS 지식
- ETC
---

URI, URL, URN에 이어서 URL의 Query Parameter과 Path Variabled를 사용하는 방법을 작성.

# Query Parameter
![](/assets/images/cs/query_1.png)

위의 사진에서 **? 를 기점**으로 이후부터 시작되는 부분이 바로 **Query Parameter**<br/>
흔히 Query String 이라고 부리기도 함.

key 와 value 로 쌍을 이룬 문자 형태이며 그 뒤는 **& 로 연결**<br/>
이렇게 담겨진 Query Parameter는 웹서버로 전달.<br/>
검색어 등의 자주 변경되는 값을 주소로 명시하기 위해 사용

# Path Variable
경로를 변수로 사용<br/>
게시글을 조회한다고 하면, 해당 게시글의 id를 서버로 넘겨주어야 조회 가능<br/>
변경되지 않는 값을 주소로 명시하기 위해 사용

# 사용 방법
- Path Variable → 어떤 자원의 위치를 특정해서 보여줘야하는 경우<br/>
  ```bash
  /users     // 모든 사용자 목록 가져오기
  /users/123 // 회원 번호 혹은 id가 123인 사용자 정보 가져오기
  ```

- Query Parameter → 정렬하거나 필터해서 보여줘야하는 경<br/>
  ```bash
  /posts          // 모든 게시글 목록 가져오기
  /posts?user=abc // 작성자가 abc인 게시글만 가져오기
  ```
  
# 프로젝트 적용 사례
![](/assets/images/cs/query_2.png)

위와 같이 메인에서 아이콘 클릭 시 즐겨찾기 페이지로 이동 후 클릭한 값에 맞는 스타일 줘야한다.

```javascript
// 공통 환경설정하는 부분

export const data = {
    // ...
    
    metaFavor: {
        default: [
            {
                link: 'link 위치?kind=cl',
                target: '_self', //링크 이동 시 target 속성 -> 페이지 이동 없이 변경 요청
                icon: 'icon 위치',
                name: '클래스 운영',
                linkparam: {
                    usertype: 'DATA.profile.userType'
                }
            },
            // ...
        ],
        // ...
    }
}
```

```javascript
// 메인 페이지

const checkParam = el => {
    // ...
    
    let paramStr;
    for (let i in el.linkparam) {
        const param = '지금 로그인된 DATA.profile.userType 값 가져오기 ex)tea, stu, par 등등'
        paramStr += paramStr === '' ? `${i}=${param}` : `&${i}=${param}`;
    }
    let href = el.link
    const url = new URL(href);
    href = url.search ? `${href}&${paramStr}` : `${href}?${paramStr}`
    // 현재 링크에 Query Parameter가 있으면 &, 없으면 ?
		
}
```

```javascript
// 즐겨찾기 페이지

const links = [
    {
        title: '클래스 운영',
        key: 'cl',
        link: [
            {
                name: '학급 운영',
                link: 'link 이름',
                icon: 'class'
            },
            // ...
        ]
    },
    // ...
]

const urlParams = new URLSearchParams(window.location.search);

let kindLinks;
if(urlParams.get('kind')) {
    kindLinks = links.filter(link => link.key === urlParams.get('kind'))
    
    // url의 Query Parameter 중 kind의 값과 같은 곳에 background 색상 주기
    if(kindLinks) {
        const linksKey = document.querySelector("." + kindLinks[0].key + " ul");
    linksKey.classList.add('clickbg');
    }
}

// usertype에 따라 display 설정
if(urlParams.get('usertype') !== 'par') {
    iconUn.style.display = "block";
    iconIn.style.display = "none";
} else {
    iconUn.style.display = "none";
    iconIn.style.display = "block";
}
```

# 평가
당시 완벽하게 이해하지 못하고 일단 진행했던 기억이 있다. 
당연히 오류도 있었고, 중복되는 코드들도 있고, 지금 와서 보면 고쳐야할 코드가 천지에 있다. 
회사나 프로젝트는 내가 이해하기를 기다리지 않는다. 그래서 미리미리 여러가지 공부를 해놓고, 많은걸 알고 있어야 어떠한 업무가 왔어도 바로 이해하고 해결할 수 있을 것같다.

그리고 또 작성만 하지말고 자주자주 들어와서 공부해놨던 것을 복기해봐야 될 것 같다.