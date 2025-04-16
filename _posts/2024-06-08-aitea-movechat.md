---
title: 🔘 페이지 이동 수 만큼 채팅이 보내지는 이슈.
date: 2024-06-08 00:00:01
categories:
- 트러블슈팅
- AI 도움쌤
tag:
- 트러블슈팅
- AI 도움쌤
---

# 문제
- '선생님께 질문하기', '챗봇에게 질문하기' 버튼을 여러 번 클릭 후 채팅을 작성 시 채팅이 여러개 보내지는 이슈 발견.

# 원인
- 입장과 동시에 `chatBox()` 함수를 호출하여 `this.cBtn` 클릭 이벤트가 발생하고 있는데, 페이지 이동 버튼 클릭 시 또 `chatBox()`를 호출하여 
  **클릭 이벤트가 연속으로 발생**

```javascript
chatBox() {
  // 페이지 진입 시 채팅 목록 호출
  this.getChatList();

  '페이지 진입 시 추천 콘텐츠 목록 호출 함수'

  'input의 입력값 초기화 이벤트'

  '전송 버튼 클릭 시 채팅 등록 API 호출 함수 이벤트'

  'input에서 엔터 클릭 시 전송 버튼 클릭 이벤트'
}

init() {	
  // 페이지 처음 로드 => 입장과 동시에 채팅 api 호출
  this.chatBox();
  
  // 1번째 페이지 이동
  this.pageLeftBtn.addEventListener('click', () => {
    this.chatBox();
  })
	
}
```

처음에는 이벤트가 전달되는 이벤트 버블링 현상인줄 알고 클릭 이벤트마다 `e.stopPropagation()`을 추가했지만, 
변동은 일어나지 않았다.

> stopPropagation() → 이벤트가 부모 요소로 전파되는 것을 막는다.<br/>
> preventDefault() → 이벤트의 기본 동작 (ex. 폼 제출, 링크 클릭 등)을 막는다.

# 방법
- 페이지 이동 버튼 클릭 시 일어나는 이벤트에 **체팅 목록만 호출**할 수 있게 로직 변경

# 해결
```javascript
init() {
  // 페이지 처음 로드 => 입장과 동시에 채팅 api 호출
  this.chatBox();
  
  // 1번째 페이지 이동
  this.pageLeftBtn.addEventListener('click', () => {
    // 채팅 목록만 호출
    this.getChatList();
  })
}
```

# 결과
- 페이지 이동할 때 마다 불필요한 호출을 줄일 수 있었고, 서버의 부담도 확연히 줄었다.

내가 작성한 instance 안에 어떠한 이벤트나 호출들이 있는지 잘 확인하고 이벤트 발생 시 어떤 부분까지 영향을 미치는지 
확인을 잘 해야겠다. 그리고 한 instance 안에는 꼭 필요한 로직들만 넣어야겠다.