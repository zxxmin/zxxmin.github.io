---
title: 옹알이(1)
date: 2024-07-03 00:00:01
permalink: /codingTest/120956
categories:
  - 알고리즘
  - LV.0
tag:
  - 알고리즘
  - LV.0
---

코딩테스트 입문

# 문제내용
조카는 아직 “aya”,”ye”,”woo”,”ma” 네가지 발음을 최대 한번씩 사용해 조합한(이어붙인) 발음 밖에 못한다.<br/>
문자열 배열 babbling 이 매개변수로 주어질 떄, 조카가 발음할 수 있는 단어의 개수를 return 하도록 solution 함수를 완성해라.

# 제출한 답
```javascript
const solution = (babbling) => {
  const arr = ["aya", "ye", "woo", "ma"]
  let answer=0;
  babbling.forEach(el => {
    for(let i = 0; i < arr.length; i++) el = el.split(arr[i]).join('.');
    if(Array.from(el).filter((item, pos) => el.indexOf(item) === pos).join('') === '.') answer++
  })
  return answer
}
```

## 🚩나의 생각
`babbling` 으로 들어오는 문자열 배열을 하나하나 돌면서 `arr 배열`과 비교를 해야한다고 생각.<br/>
`babbling` 배열의 원소를 `arr 배열`의 원소를 대조해서 있으면 ‘.’ 으로 변경했다.<br/>
모두가 있으면 ‘.’만 남고 글자가 하나라도 있으면 안되는 단어라고 생각한 전제 하에 중복된 값을 합치고 합친 후 그 값이 ‘.’일 경우 answer 값에 +1을 해주었다.

## 풀이 보완
### 보완 1.
```javascript
function solution(babbling) {
  var answer = 0;
  const regex = /^(aya|ye|woo|ma)+$/;

  babbling.forEach(word => {
    if (regex.test(word)) answer++;
  })

  return answer;
}
```

다른 사람 풀이 방법들을 확인하는 과정에 정규표현식을 사용한 사람의 코드를 확인할 수 있었다.<br/>
/^(aya|ye|woo|ma)+$/ 은<br/>
- ‘^’ : 문자열의 시작을 의미
- ‘(aya|ye|woo|ma)’ : ‘aya’, ‘ye’, ‘woo’, ‘ma’ 중 하나의 일치하는 부분
  - ‘|’ : OR 연산자로 그룹 중 하나와 일치하면 됨
- ‘+’ : 그룹이 하나 이상 반복됨
- ‘$’ : 문자열의 끝을 의미
- test(el) : 문자열 ‘el’에 대해 참인지 거짓인지 반환하는 메서드

즉, babbling의 원소인 문자열 전체가 ‘aya’, ‘ye’, ‘woo’, ‘ma’로 되어있으면 true 아니면 false 로 반환.<br/>
true이면 answer에 +1을 해주어 값을 파악할 수 있다.

# 평가
한가지 메서드에 꽂히면 그것만 사용하려고 고집하는 경우가 있는 것 같다.<br/>
그러면서 시야가 좁아지게 되고 문제 접근하는 방법이 다양하지 못하게 된다.<br/>
이게 메서드들을 완벽히 이해해야지 나오는 방법인지, 잘 모르겠다.<br/>
한 메서드에 꽂히려고 하지말고 다양하게 접근하는 방법을 알아야겠다.