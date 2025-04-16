---
title: 💬 채팅방의 첫 채팅은 두개씩 보여지는 이슈
date: 2024-06-07 00:00:01
categories:
- 트러블슈팅
- AI 도움쌤
tag:
- 트러블슈팅
- AI 도움쌤
---

# 개요 1.
- 채팅창의 첫 채팅 후 모든 채팅들이 각각 다른 채팅창의 글로 인식하는 오류 발견.

# 분석 1.
- 채팅방을 구분하기 위해 채팅방 ID를 파라미터로 보내고 있는데, 채팅방 첫 채팅 시 채팅방 ID가 없기 때문에 null로 보내고, 
  그 다음 채팅부터는 채팅 목록을 호출 후 같이 넘어오는 채팅방 ID 값을 파라미터에 같이 보내줘야한다.<br/> 
  채팅방 첫 채팅 후 **채팅 목록을 호출하지 않아 채팅방 ID 값이 없었고, 게속 null 로 보내지게 되어** 각각 다른 채팅방으로 인삭하고 있었다.

# 해결 방법 1.
- 채팅방의 첫 **채팅 등록과 동시에 채팅 목록을 호출**하는 로직 추가

```javascript
async drawChat (target) {
    let chatTxtArea = this.cBox.getElementsByClassName(target)[0];
    // chatTxtArea가 있는지 없는지 확인. 즉, 첫채팅인지 아닌지 확인.
    if (!chatTxtArea) {
        // 첫 채팅이면 채팅 목록 호출 및 채팅 목록 그려주기
        await this.getChatList();
        chatTxtArea = this.cBox.getElementsByClassName(target)[0];
    }

    // 시간 계산
    let hours = today.getHours();
    let minutes = today.getMinutes();
    const meridiem = hours < 12 ? '오전' : '오후';

    hours = hours % 12 || 12;
    hours = String(hours).padStart(2, '0');
    minutes = String(minutes).padStart(2, '0');

    const currentTime = `${meridiem} ${hours}:${minutes}`;
    // ----

    // myArea 생성
    const myArea = createEl('div', {className: 'my-area'})
    myArea.innerHTML = `
        <div>
            <div class="chat-txt">
                ${this.cInput.value}
            </div>
            <div class="chat-item">
                <time>${currentTime}</time>
            </div>
        </div>
    `

    chatTxtArea.append(myArea);
    // ----
}
```

# 개요 2.
- 로직 변경 후 채팅창의 첫 채팅 시 두개씩 그려지는 이슈 발견.
![](/assets/images/troubleshooting/aitea-morechat-1.png)

# 분석 2.
- 채팅 작성 후 등록 버튼 클릭 시 동적으로 그려주는 부분과 채팅 목록을 호출하여 그려주는 부분이 각각 따로 있다.  
  첫 채팅일 경우에는 채팅방 ID 값을 받아야하기 때문에 **등록과 동시에 채팅 목록을 호출**하여 두개씩 그려지는 것으로 판단.

# 해결 방법 2.
## 방법 2-1.
- 호출 후 그려지는 부분에 초기화가 되지 않아서라고 판단했고 채팅 목록을 호출하여 그려지는 부분에 `cBox.innerHTML = '';'` 를 작성했지만, 
  그래도 거의 동시에 실행되서 두개씩 그려지고 있다.

## 방법 2-2.
- drawChat 함수에서 **첫 채팅일 때는 채팅을 그려주지 않고 호출만 되게** 로직 변경

```javascript
async drawChat (target) {
	let chatTxtArea = this.cBox.getElementsByClassName(target)[0];
	let myArea;
	// chatTxtArea가 있는지 없는지 확인. 즉, 첫채팅인지 아닌지 확인.
	if (chatTxtArea) {
		// 첫 채팅이 아니면 my-area 그려주기
		myArea = createEl('div', { className: 'my-area', })
	} else {
		// 첫 채팅이면 채팅 목록 호출 및 채팅 목록 그려주기
		await this.getChatList();
		chatTxtArea = this.cBox.getElementsByClassName(target)[0];
	}
	
	if(myArea) {
		myArea.innerHTML = `
      <div>
          <div class="chat-txt">
              ${this.cInput.value}
          </div>
          <div class="chat-item">
              <time>${currentTime}</time>
          </div>
      </div>
	  `
	
	  chatTxtArea.append(myArea);
	}
	
}
```

# 결과
- 로직 수정 후 채팅방을 각각 인식하여, 생성되고 있던 채팅방 ID의 갯수도 확연히 줄었고 서버에 부담을 줄였다.

채팅을 그려주는 부분에서 분기 처리를 하여 작성했다면, 오류가 나지 않았을건데 아쉬웠고 다음에는 컴포넌트화를 잘해야겠다고 생각했다.