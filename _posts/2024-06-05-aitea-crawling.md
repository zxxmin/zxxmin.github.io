---
title: 🔗 다른 페이지에 접근하여 title 가져오기
date: 2024-06-05 00:00:02
permalink: /aitea/crawling
categories:
- 트러블슈팅
- AI도움쌤
tag:
- 트러블슈팅
- AI도움쌤
---

# 개요
URL 링크 등록 시 URL 표시하는 앞쪽에 해당 URL의 &lt;title&gt; 태그의 글자가 들어갔으면 좋겠다.

# 방법 1.
URL 등록 버튼 클릭 시 작성된 URL의 html에 접근해서 &lt;title&gt; 태그의 innerText를 가져오면 되겠다고 생각.

# 해결 1.
```javascript
async function getPageTitle(url) {
    try {
        const response = await fetch(url);

        if (!response.ok) {
            throw new Error(`HTTP error! Status: ${response.status}`);
        }

        const html = await response.text();
        const parser = new DOMParser();
        const doc = parser.parseFromString(html, 'text/html');
        const title = doc.querySelector('title').innerText;

        return title;
    } catch (error) {
        console.error('Error fetching the page title:', error);
    }
}

getPageTitle('https://www.youtube.com/watch?v=N0GCIhtWHQw').then(title => {
    console.log('Page title:', title);

    const h1 = document.createElement('h1');
    h1.innerHTML = `${title}`
    document.body.appendChild(h1);
});
```

방법을 찾는 도중 실험을 위해 테스트를 해봤다.<br/>
잘 나오는 것을 봐서 한번에 해결할 수 있겠다 싶었다.

![](/assets/images/troubleshooting/aitea-crawling-1.png)

프로젝트에 옮겨서 코드를 작성하고 테스트를 했다.<br/>
역시… **한번에 문제가 해결이 될 리가 없다**. 어쩐지 술술 풀린다고 생각했는데, 접근 제한 문제가 있었다.

![](/assets/images/troubleshooting/aitea-crawling-2.png)

# 원인
(blocked:mixed-content)의 오류는 웹사이트에서 보안되지 않은 콘텐츠를 로드하려고 할 때 발생.<br/>
주로 보안 프로토콜을 사용하고 있는 웹 페이지(HTTPS)가 보안이 되지 않은 프로토콜(HTTP)을 사용하는 리소스를 요청할 때 발생<br/>
https를 작성해봐도 콘솔창에 아래와 같은 에러들이 떴다.

```bash
Mixed Content: The page at 'https://devtai.itt.link/index.do' was loaded over HTTPS, but requested an insecure resource 'http://www.naver.com/'. This request has been blocked; the content must be served over HTTPS. 
```

```bash
 Access to fetch at 'https://www.naver.com/' from origin 'https://devtai.itt.link' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.
```

1번 오류는 네트워크 오류와 같은 오류이고, 2번 오류는 CORS 정책과 관련된 에러이다.

CORS는 웹 페이지가 다른 도메인, 프로토콜, 또는 포트에서 호스트된 리소스에 접근할 때 
웹 브라우저에서 적용되는 보안 기능으로 브라우저가 잠재적으로 위험한 웹 페이지 간 요청을 차단하여 보호하는데 목적이 있다.

결론은 **보안의 문제로 인해 프론트에서 사용자가 작성하는 다양한 url에 접근하지 못할거라고 판단**하였고, 
**백엔드 개발자에게 프록시 서버를 사용해 서비스에 간접적으로 접속하여 title을 보내는 API를 만들어달라고 부탁**하였다.

[CORS 정리](/cs/csp&sop&cors)

# 해결 2.
백엔드 개발자님이 커밋을 해주었고, 프록시 서버를 이용하는건 어떻게 하는건지 궁금했다.<br/>
그래서 커밋한 코드를 분석해봤다.

1. 먼저 pom.xml에 아래와 같은 문장을 추가했다.<br/>
    ```xml
    <dependency>
        <groupId>org.jsoup</groupId>
        <artifactId>jsoup</artifactId>
        <version>1.13.1</version>
    </dependency>
    ```
   
전에 한번 팀원들이 모두 모여서 SSO 설정으로 인해 pom.xml에 코드를 추가하는 일이 있었고, 그 때 듣기로는 pom.xml은 프로젝트 내 필요한걸 설정하는 파일이고 
dependency는 사용할 라이브러리를 추가한다고 간단하게 설명을 들었던 기억이 있다.<br/>
[pom.xml과 dependency](https://coding-hyeok.tistory.com/47)

![](/assets/images/troubleshooting/aitea-crawling-3.png)

저 코드를 보면 jsoup이라는 라이브러리를 사용한다고 설정을 하였는데, jsoup 라이브러리는 HTML을 파싱하고 웹 페이지에서 데이터를 추춘할 때 사용하는 라이브러리이다.

2. 자바 파일에 import<br/>
   ```java
   import org.jsoup.Jsoup;
   import org.jsoup.nodes.Document;
   ```

jsoup 라이브러리의 특정 클래스와 메서드를 사용하게 위해 해당 java 파일에 import

![](/assets/images/troubleshooting/aitea-crawling-4.png)
![](/assets/images/troubleshooting/aitea-crawling-5.png)

# 평가
회사 프로젝트 특성 상 CORS 오류를 많이 마주칠 일이 없었다. 
어떻게 보면 흔한 오류일텐데 처음 마주하니까 당황스럽고 어떻게 해결해야할지 몰랐다. 프론트에서 할 수 있는 일과 할 수 없는 일을 구별하기엔 아직은 어려운 것 같다.

백엔드 개발자에게 일을 넘겨도 충분히 내용을 인지하고 해결 방법에 대한 이해도 있어야한다고 생각한다.<br/>
그 때 부랴부랴 찾아보고 하니까 더 정신이 없었던 것 같다. 
CORS 오류 같이 남들이 흔히 겪고 있고 알고 있는 오류들에 대한 대처 방법도 공부를 해놔야겠다고 생각했다.

