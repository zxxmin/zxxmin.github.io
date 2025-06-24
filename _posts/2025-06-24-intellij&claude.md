---
title: IntelliJ에 Claude MCP 연결하기
date: 2025-06-24 00:00:01
permalink: /intellij_claude
categories:
  - 실무
tag:
  - 실무
---

Cursor는 Visual Studio Code를 포크해서 만들어진 통합 개발 환경으로, 개발 작업을 AI의 도움으로 효율화할 수 있는 툴이라고 하여 
요즘 개발자들 사이에서 핫하다는 Cursor를 다운받아 사용해보았다.

실무에서 VSCode를 사용하다가 JAVA 기반의 프로젝트를 하다보니, 서버 연결이나 Git 관련된 것들을 조금 더 쉽게 사용할 수 있는, 
그리고 무엇보다 회사에서 결제해줘서 사용하는 IntelliJ를 주로 사용한다.

그래서 IntelliJ에서 Cursor 같이 사용할 수 있는 방법이 없나 MCP server를 찾아보게 되었다.

# MCP
MCP는 **&quot;Model-Context-Protocol&quot;**의 약자로, AI 모델이 외부 데이터 소스 및 도구와 상호작용할 수 있도록 하는 표준화된 프로토콜을 의미

JetBrains IDE에서 사용할 프로토콜은 **외부 애플리케이션과 JetBrains IDE 간의 통신을 가능하게 해주는 중간 계층 역할**을 한다.

## IntelliJ와 Claude 사이에서 MCP server 가 하는 일 
위에서 말하는 **외부 애플리케이션은** Claude에 연결할 예정이기 때문에 **Claude Desktop**이 된다.

- **중개자 역할**<br/>
  MCP server는 Claude와 IntelliJ 사이에서 통신을 중개. Claude 에서 보내는 요청을 IntelliJ로 전달하고 intelliJ에서 오는 응답을 Claude 로 전달.
- **프로토콜 변환**<br/>
  Claude와 IntelliJ의 서로 다른 통신 방식을 사용할 수 있기 때문에 이 두 시스템 간의 프로토콜을 변환하여 호환성 문제를 해결
- **API 접근 제공**<br/>
  - IntelliJ에 열려 있는 프로젝트 파일 읽기
  - 코드 분석 및 검색
  - 코드 수정 제안
  - 프로젝트 구조 탐색
- **상태관리**<br/>
  연결 상태를 유지하고 필요애 따라 재연결 처리.
- **데이터 가공**

등 여러 이점들이 발생한다.

# 1. IntelliJ 에서 "MCP Server" 플로그인 다운로드
[MCP Server - IntelliJ IDEs Plugin](https://plugins.jetbrains.com/plugin/26071-mcp-server)

File → Settings (Ctrl + Alt + S / Cmd + ,) → Plugins 에서 "mcp server" 검색 후 install

![](/assets/images/etc/mcp_1.png)

# 2. Claude Desktop 다운로드
[Claude 홈페이지](https://claude.ai/download?ref=maginative.com)

Claude 홈페이지에서 다운로드

# 3. Claude에 IntelliJ MCP 설정
[GitHub - JetBrains/mcp-jetbranis](https://github.com/JetBrains/mcp-jetbrains)

Claude Desktop 실행 → 설정 (Ctrl + , / Cmd + ,) → 설정 편집 → Claude 설정 파일 (claude_desktop_config.json) 열기

```json
{
  "mcpServers": {
    "jetbrains": {
      "command": "npx",
      "args": ["-y", "@jetbrains/mcp-proxy"]
    }
  }
}
```
위와 같이 설정해주고 저장 및 닫기

Claude Desktop 재실행 → 설정 (Ctrl + , / Cmd + ,) 을 열면... **아무일도 일어나지 않았다...**<br/>
그래서 여러번 Claude 프로그램을 닫고 재실행을 했는데,

![](/assets/images/etc/mcp_2.png)

윈도우 기준 이런식으로 아예 앱 자체를 종료 시켜야한다.

그리고 다시 실행을 했을 때,

![](/assets/images/etc/mcp_3.png)

위와 같이 `jetbrains running` 을 확인할 수 있었다.

# 4. 확인
![](/assets/images/etc/mcp_4.png)
