---
title: 🔛 JSON 직렬화
date: 2024-07-01 00:00:02
categories:
- 트러블슈팅
- 다채움
tag:
- 트러블슈팅
- 다채움
---

무한 스크롤 기능을 위해 JSP 파일로 되어있던 내용들을 동적으로 생성하기 위해 JS로 마이그레이션 중 API를 만들지 않고 무한 스크롤 기능을 구현할 수 있을까? 라는 의문에서 시작한 글입니다.

# 문제
- 커뮤니티 페이지는 **페이징 처리가 되어있지 않아** 입장과 동시에 모든 첨부파일과 댓글들을 불러오는 구조라 데이터가 많으면 **로딩 속도가 굉장히 느리다.**

해결 방법으로 **무한 스크롤 기능 구현**으로 결정하였고, 코드를 확인했다.<br/>
JSP 파일에서 EL 표현식으로 작성되어있는 코드들을 모두 동적 생성을 위해 마이그레이션을 해야헀지만 코드를 모두 바꾸기엔 리스크가 클 것 같아 
백단에서 무한 스크롤 처리가 가능한지 생각해보았다.

JSP 내용들을 다시 불러오려면 **SSR 방식**이라 무조건 **서버에 요청**을 해야했고 화면이 깜박임이 있을 수 밖에 없다.<br/>
꼼수로 스크롤이 뷰포트 하단에 닿았을 때 렌더링을 요청하고 렌더링이 되면 스크롤을 강제로 아래로 조정하는 방법...이 있곘지만, 
말이 안되는 방법이기도 하고 화면 깜박임은 피할 수 없다.<br/>
일반적인 페이지네이션이였다면 상관이 없는데 무한 스크롤 기능은 JS로 변경하는건 피할 수 없었다.

JS로 동적 생성 중 API를 만들지 않고 (백단 작업 인원의 부족함도 있음...) **기존 JSP 파일에 불러오고 있는 값들을 써먹을 수 있지 않을까? 라는 생각**에 방법들을 찾아보았고, 
Java 객체를 JSON 문자열과 같은 데이터 형식으로 변환하는 **직렬화**를 해보았다.

# 방법
직렬화를 하기 위헤 **jackson 라이브러리**를 사용해야했다.<br/>
회사 프로젝트는 Maven 사옹 중으로 pom.xml 에 jackson 라이브러리 의존성을 추가해줬다.<br/>
의존성 추가 후 **Maven → Update Project** 실행을 해야 사용 가능하다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.2</version>
</dependency>
```

controller.java 파일 위쪽에 import 작성하고, bbsList를 JSON 으로 변환하고 이를 model에 추가하여 JSP에 전달하였다.
```java
import com.fasterxml.jackson.databind.ObjectMapper;
```

```java
// ObjectMapper를 사용하여 bbsList를 JSON 문자열로 변환
ObjectMapper objectMapper = new ObjectMapper();
String bbsListJson = "";
try {
    bbsListJson = objectMapper.writeValueAsString(response.getBbsList());
} catch (Exception e) {
    e.printStackTrace();
}

// 모델에 JSON 문자열 추가
model.addAttribute("bbsListJson", bbsListJson);
```

이때 계속 오류가 났었는데, Maven → Update Project 를 실행하지 않아서 오류가 났던 것.

JSP에서 &lt;script&gt; 태그 안에 값을 받을 수 있었음.
```javascript
const res = JSON.parse('${bbsListJson}');
```

# 하지만
이렇게 해도 페이지를 보내려면, 서버에 요청할 수 밖에 없고, 깜박일 수 밖에 없고,, **결국은 API 가 필요했다.**<br/>
결론은 좋은 삽질이였다!

**직렬화, 역직렬화 설명**
- [Json과 직렬화 및 역직렬화](https://velog.io/@songyuheon/Json%EA%B3%BC-%EC%A7%81%EB%A0%AC%ED%99%94-%EB%B0%8F-%EC%97%AD%EC%A7%81%EB%A0%AC%ED%99%94)
- [JSON 직렬화, 역직렬화](https://velog.io/@jy3026/JSON-%EC%A7%81%EB%A0%AC%ED%99%94-%EC%97%AD%EC%A7%81%EB%A0%AC%ED%99%94)