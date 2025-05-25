---
title: 문자열 바꿔서 찾기
date: 2024-06-19 00:00:02
permalink: /codingTest/181864
categories:
  - 알고리즘
  - LV.0
tag:
  - 알고리즘
  - LV.0
---

코딩 기초 트레이닝

# 문제내용
문자 “A”와 “B”로 이루어진 문자열 `myString`과 `pat`이 주어짐.<br/>
`myString`의 “A”를 “B”로, “B”를 “A”로 바꾼 문자열의 연속하는 부분 문자열 중 `pat`이 있으면 1을 아니면 0을 
return하는 solution 함수를 완성해야 한다.

# 제출한 답
```javascript
function solution(myString, pat) {
    let answer=[];
    myString.split('').forEach(el => {
        el = el === 'A' ? 'B' : 'A'

        answer = answer.concat(el);
    })
    return answer.join('').includes(pat) ? 1 : 0;
}
```

## 🚩나의 생각
**`myString`에 들어오는 값을 배열**로 만든 다음 배열을 돌면서 **A는 B로 B는 A로 치환한 후**에
다시 **문자열로 합치고** `pat`의 값이 있는지 찾는 방법으로 접근함.

`map`이 배열로 값을 반환을 하기때문에 `map`을 사용할 수 있을것같았는데, 아직까지는 정확하게 사용법을 모르겠어서 
`forEach`문을 사용해 answer에 `concat`을 해서 배열로 만들어 줬다.

## 풀이 보완
### 보완 1.
역시나 ‘다른 사람의 풀이’를 보면 미친놈이 여럿있다. 이 중에 내가 사용하고 싶었던 map으로 푼 코드 발견
```javascript
const solution = (myString, pat) => [...myString].map(v => v === 'A' ? 'B' : 'A').join('').includes(pat) ? 1 : 0;
```
원리를 설명하자면,<br/>
[문자열을 배열로 만드는 여러가지 방법](https://velog.io/@sunohvoiin/JavaScript-문자열을-배열로-변환하는-3가지-방법-convert-string-to-array) 중 하나인 **스프레드 연산자를 사용**한 것이다.<br/>
[…myString]으로 만든 배열에 **`map`을 사용하여 똑같이 배열을 반환한 후 바로 `join`하여 비교하는 원리**이다.

# 팀 개발자들의 생각

> 👩‍💻 쉰님 :<br/>
0을 변수에 담아놓고 myString도 배열로 변경해서 담아놓은 다음 비교를 하는데 한글자씩 잘라서 
pat과 맞는지 비교하고 맞으면 1을 return, 틀리면 변수로 담아둔 0을 return 했더니 70%만 정답이다.

> 👨‍💻 탠님 :<br/>
myString에 들어오는 값에서 A를 B로 바로 바꾸면 B를 A로 바꿀 때 섞이기 때문에 A를 C로 바꾸고 B를 A로 바꾼 다음 C를 B로 바꿨다.

태인님의 코드를 JavaScript로 변환해서 간략하게 설명하자면<br/>
```javascript
function solution(str, pat) {
    pat = pat.replaceAll('A', 'C').replaceAll('B', 'A').replaceAll('C', 'B')

    return str.indexOf(pat) === -1 ? 0 : 1
}
```
이런느낌이였다.

수인님의 70% 정답인 이유는 아마 하나씩 자르면서 비교하기 때문에 문자열의 시작점과 끝점이 아닌,<br/>
중간에 껴있거나 하는 값들을 제대로 못찾는 것 같아서 JavaScript의 includes와 비슷한 역할을 하는
java의 contains를 사용했더니 100% 정답이 되었다.

# 평가
문제만 무작정 풀 것이 아니라 자바스크립트의 메서드들을 열심히 공부하고, 알고리즘적인 사고 방식을 가져야겠다.