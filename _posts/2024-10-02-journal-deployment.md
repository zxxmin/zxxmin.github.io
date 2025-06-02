---
title: Github Action 배포 후 새로고침 404 오류
date: 2024-10-02 00:00:01
permalink: /toy/journal/gitactions
categories:
  - Toy project
  - journal
tag:
  - Toy project
  - journal
---

# 문제
배포가 끝났고, 페이지에서 새로고침하거나 주소 입력 시 404 페이지가 뜬다.<br/>
BOSE 배포 후 나왔던 문제와 비슷한 문제 발생

![](/assets/images/toy/journal_deployment_1.png)

# HashRouter 사용
```javascript
import { BrowserRouter} from 'react-router-dom'

createRoot(document.getElementById('root')).render(
  <BrowserRouter basename='/sidepj_bose'>
    <App />
  </BrowserRouter>
)
```

기존의 BroswerRouter를 HashRouter로 변경하고 basename 제거

```javascript
import { HashRouter } from 'react-router-dom'

createRoot(document.getElementById('root')).render(
  <HashRouter>
    <App />
  </HashRouter>
)
```

# 특징
- 주소에 #이 붙음<br/>
  ![](/assets/images/toy/journal_deployment_2.png)
- 새로고침을 해도 에러가 발생하지 않음
- 주소에 #이 붙기 때문에 서버가 페이지의 유무를 알지 못해 검색 엔진으로 읽지 못함.

검색 엔진으로 읽지 못한다는 점 때문에 거의 사용하지 않는다고 함. 다른 좋은 방법을 찾아봐야겠음.

# 평가
보스 프로젝트 당시 HashRouter를 사용했는데 카카오 소셜 로그인 연동 시 # 가 Redirect URI로 읽히지 못해서 연동이 잘 되지 않았던 부분이 있음.
이런 부분들을 해결하지 못하는 것을 보면 둘다 방법으 틀렸다는 것 같은데,,, 더 좋은 방법을 찾아봐야겠다.

journal 프로젝트는 개인적으로 사용할 수 있는 사이트이기때문에 검색엔진을 크게 생각하지않아 HashRouter로 변경했음.