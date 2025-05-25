---
title: 신고 결과 받기
date: 2024-08-01 00:00:01
permalink: /codingTest/92334
categories:
  - 알고리즘
  - LV.1
tag:
  - 알고리즘
  - LV.1
---

2022 KAKAO BLIND RECRUITMENT

# 문제내용
- 각 유저는 한 번에 한 명의 유저를 신고
  - 신고 횟수에 제한 없음. 서로 다른 유저를 계속 신고할 수 있음.
  - 한 유저를 여러 번 신고할 수도 있지만, 동일한 유저에 대한 신고 횟수는 1회로 처리
- k 번 이상 신고된 유저는 게시판 이용 정지, 해당 유저를 신고한 모든 유저에게 정지 사실을 메일로 발송.
  - 유저가 신고한 모든 내용을 취합하여 마지막에 한꺼번에 게시판 이용 정지를 시키면서 정지 메일 발송

`id_list` : 전체 유저 목록<br/>
`report` : ["muzi frodo"] “muzi” 가 “frodo” 를 신고. 문자열 중 공백 앞이 신고자, 공백 뒤가 신고당한 자<br/>
`k` : k번 이상 신고를 당하면 이용 정지<br/>
신고당한 자가 k번 이상 신고 당해 이용 정지가 되면 신고자에게 처리 메일을 보낸다.<br/>
`result` : 이용 정지를 당한 사람을 신고한 사람이 받은 처리 결과 메일을 받은 횟수


# 제출한 답
```javascript
const solution = (id_list, report, k) => {
  let idList = id_list.reduce((acc, el) => ({...acc, [el]: 0}), {});

  let reportList = report.reduce((acc, el) => {
    const [reporter, reportee] = el.split(' ');

    if(!acc[reportee]) acc[reportee] = [];
    acc[reportee].push(reporter);

    return acc
  }, {})

  Object.keys(reportList).forEach(key => {
    reportList[key] = [...new Set(reportList[key])]
    if (reportList[key].length >= k) reportList[key].map(el => idList[el] += 1)
  })

  return [...Object.values(idList)]
}
```

## 🚩나의 생각
먼저 처리 결과 메일을 받은 횟수를 담을 수 있는 객체를 만들기 위해 id_list로 들어오는 값을 key 값으로 두고 value 값을 0으로 초기화 시켜 변수 idList 에 할당.

report로 들어오는 값 중에 신고당한 사람이 몇명에게 신고를 당했고, k번 이상인지 또 중복 신고한 사람은 1회로 처리하기 때문에 모든 과정을 쉽게 처리하기 위해 신고당한사람을 또다른 객체의 key로, 
신고한 사람을 value로 담는게 편할 것이라고 판단하였다.

최초로 작성한 코드<br/>
```javascript
let reportList = {};
    
report.forEach(el => {
    const [ reporter, reportee] = el.split(' ');
    
    reportList[reportee]=reporter;
})
```

내가 원한건 frodo를 신고한 사람이 2명이면 2명이 다 value 값에 담겨있는 모습을 원했는데, 2명 중 뒤에 나온 아이만 담겨있었다.<br/>
반복문을 돌면서 같은 key 값이 할당될 때 덮어 씌워지기 때문이다. 그래서 배열에 담기로 생각했다.

```javascript
let reportList = {};
    
report.forEach(el => {
    const [ reporter, reportee] = el.split(' ');
    
    if (!reportList[reportee]) reportList[reportee] = [];
    reportList[reportee].push(reporter);
})
```

reportee에 해당하는 key 가 reportList에 아직 없다면 undefined 상태가 되고 그 상태에서 push를 하면 push가 되지 않아서 reportList[reportee] 값이 중복되지 않을 경우에만 빈배열로 할당했다.<br/>
if (!reportList[reportee]) 이 조건문을 사용하지 않으면 위와 똑같이 덮어씌워지기 때문이다.

let reportList = {} 이렇게 선언만 하는게 싫어서 합칠 수 있는 방법을 찾아봤고, reduce를 사용하면 빈값 선언 없이 바로 변수에 담을 수 있었다.

```javascript
Object.keys(reportList).forEach(key => {
    reportList[key] = [...new Set(reportList[key])]
    if (reportList[key].length >= k) reportList[key].map(el => idList[el] += 1)
})
```

reportList를 돌면서 중복으로 신고한 사람은 1회로 보기 때문에 합치는 작업을 set 메서드를 사용.<br/>
reportList의 value 값인 배열의 길이가 k 보다 많으면 배열안에 있는 값을 idList의 key 값에 1씩 더하게 해줬다.

# 팀 개발자들의 생각

> 👩‍💻 쉰님 :<br/>
내가 했던 방식과는 반대로 신고한사람을 key 값으로 신고당한사람을 value 값을 사용.<br/>
HashMap 사용했는데 순서가 랜덤으로 나와서 LinkedHashMap을 사용.<br/>
HashMap → 랜덤으로 순서 배치<br/>
LInkedHashMap → 순서를 보장하기위해 사용

# 평가
꽤 오래 잡고 있었던 문제였다. 3번 이상은 다 지우고 다시 했던 문제…<br/>
다른 풀이법을 생각하고자 다 지우고 작성하면 결국 비슷한 풀이법으로 접근하게 되어서 어려웠던 문제였다.<br/>
그래도 중복 값을 제거하는 set 객체를 접목 시켜서 잘 풀렸던 것 같다.