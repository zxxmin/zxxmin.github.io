---
title: MobaXterm - 개발서버 배포
date: 2025-06-12 00:00:01
permalink: /mobaxterm
categories:
  - 실무
tag:
  - 실무
---

- https://mobaxterm.mobatek.net/

# MobaXterm 사용 방법
## MobaXterm 한글 인코딩 설정 (초기 설정)
![](/assets/images/etc/mobaxterm_1.png)

- [settings] → [Terminal] → [Default font settings] 한글 인코딩 설정 후 OK 버튼 클릭

## MobaXterm으로 SSH 접속하기 (Ubuntu 서버 접속)
![](/assets/images/etc/mobaxterm_2.png)

- [Session] → [SSH] 클릭 후 접속할 Linux IP 주소 및 계정 등의 정보를 작성

1. **Remote host** : Linux 주소 (도메인 혹은 IP 주소)
2. **Specify username** : 접속할 계정 ID
3. **Port** : SSH 접속 포트 (기본 값이 22로 지정되어 있음)
4. **Execute command** : Session 초기 접속 시 실행할 명령어 (선택 사항)
5. **User private key** : 보안 키 사용 시 입력 (선택 사항 - 보안 키가 있을 경우에 작성)

## MobaXterm으로 배포
![](/assets/images/etc/mobaxterm_3.png)

- login Id를 작성

![](/assets/images/etc/mobaxterm_4.png)

- login Id를 작성하면 위와 같은 화면이 뜸
- 좌측 폴더 영역에서 data > tomcat > work > devfolio 까지 들어간 후 커밋한 내역 경로 따라가기

인텔리제이에서 restart를 눌러 내가 커밋한 내역들을 최신화 시킨 후<br/>
폴더 탐색, target 폴더 안에서 커밋한 내역의 경로 따라가기

그대로 옮김

[참고블로그](https://backendcode.tistory.com/270)