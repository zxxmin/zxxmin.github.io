---
title: 🍎 맥OS의 Keyboard Event
date: 2024-06-05 00:00:01
categories:
- 트러블슈팅
- AI 도움쌤
tag:
- 트러블슈팅
- AI 도움쌤
---

# 문제
- 특정 컴퓨터에서 채팅 작성 후 전송 버튼을 클릭 시에는 이상이 없지만, **엔터키를 눌렀을 때 채팅이 2개씩 보내지고 있는 이슈** 발견.

# 원인
- 윈도우 체제의 환경에서는 발생하지 않았고, 맥OS 체제의 환경에서만 이슈 발견.
- 클릭 이벤트에는 이상이 없으므로, 키보드 이벤트에 문제가 있다고 판단.

```javascript
this.cBtn.addEventListener('click', () => {
    this.getInsertChat();
})

this.cInput.addEventListener('keydown', (e) => {
    if(e.key === 'Enter') this.cBtn.click();
})
```

## 키보드 이벤트 확인
- IME 의 keycode가 299로 나오는 이슈

> **IME (Input Method Editor)**<br/>
IME (Input Method Editor)는 한글, 일본어, 중국어와 같이 여러 글자를 조합해서 만들어진 글자들을 입력하는 시스템을 의미.<br/>

> **keycode 299**는 IME에서 'composing' 상태. 즉, **조합 중인 상태**로 나타낼 때 주로 사용.<br/>
composing을 나타낼 수 있는 속성은 isComposing. **isComposing이 true 이면 조합 중, false 이면 입력이 완료**되었다는 의미.

영어를 작성하고 엔터를 쳤을 때는 이상이 없었다.
![](/assets/images/troubleshooting/aitea-chat-1.png)

## 결론
input 창에 한글을 입력하고 keydown 이벤트가 일어났을 때 버튼 클릭 이벤트가 일어나고, isComposing 값은 true가 된다.<br/>
Mac OS 에서는 특정 상황에서 composing 상태가 지속되는 동안에도 이벤트가 발생할 수 있음.

그래서 버튼 클릭 이벤트가 두 번 발생했을거라 생각한다.

**keycode 및 isComposing 확인할 수 있는 사이트**
- [[Web] IME keyCode 229 Issue](https://minjung-jeon.github.io/IME-keyCode-229-issue/)
- [[javascript] keycode, code 값 찾기](https://korearaogong.tistory.com/32)

# 방법
방법은 크게 2가지
- keydown 이벤트를 변경
- isComposing 속성에 대한 조건문을 추가

# 해결
## 해결 1.
- keydown 이벤트를 **keypress 이벤트로 변경**

```javascript
this.cInput.addEventListener('keypress', (e) => {
    if(e.key === 'Enter') this.cBtn.click();
})
```

keydown 이벤트는 키가 눌렸을 때, keyup 이벤트는 키가 올라올 때, keypress 이벤트는 키가 눌렸다가 올라올 때 이벤트가 작용한다고 배웠던 
기억이 있다. 다른 블로그를 확인했을 때 keyup 으로 많이 변경하던데, 안전하게 keypress 이벤트로 변경하였다. 

## 해결 2.
- isComposing 속성이나 keyCode의 code 값을 사용

```javascript
this.cInput.addEventListener('keydwon', (e) => {
    if(e.key === 'Enter' && !e.isComposing) this.cBtn.click();
})

this.cInput.addEventListener('keydwon', (e) => {
    if(e.isComposing || e.keyCode === 229) return;
    if(e.key === 'Enter') this.cBtn.click();
})
```

# 결과
keypress 이벤트로 변경하였고, 변경한 결과 이슈가 사라졌다. IME Keycode에 대해서 인지하게 되었고, 한글과 영어의 차이점도 
인지하게 되어서 이벤트를 줄 때 생각을 해보고 이벤트를 사용해야겠다고 생각했다.