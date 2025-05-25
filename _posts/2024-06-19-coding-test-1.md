---
title: 숫자 문자열과 단어
date: 2024-06-19 00:00:01
permalink: /codingTest/81301
categories:
  - Algorithm
  - LV.1
tag:
  - Algorithm
  - LV.1
---

2021 카카오 채용연계형 인턴십

# 문제내용
네오가 프로도에게 숫자를 건넬 때 일부 자릿수를 영단어로 바꾼 카드를 건네주면 프로도는 원래 숫자를 찾는 게임

숫자의 일부 자릿수를 영단어로 바꾸는 예시
> • 1478 → “one4seveneight”<br/>
• 234567 → “23four5six7”<br/>
• 10203 → “1zerotwozero3”<br/>

이렇게 숫자의 일부 자릿수가 영단어로 바뀌어졌거나, 혹은 바뀌지 않고 그대로인 문자열 `s`가 매개변수로 주어진다.<br/>
`s`가 의미하는 원래 숫자를 return 하도록 solution 함수를 완성해야한다.

# 제출한 답
```javascript
const solution = (s) => {
    const eng = ["zero", "one", "two", "three", "four", "five", "six", "seven", "eight", "nine"];
    
    eng.forEach((item, idx) => {
        for(let i = 0; i < s.length; i++) {
            if(s.indexOf(item) !== -1) s = s.replace(item, idx);
        }
    })
    
    return Number(s)
}
```

## 🚩나의 생각
대응하는 숫자의 값이 “zero” 부터 시작한다? index도 0부터 시작하는데? 이 문제는 배열과 index로 문제를 접근해야한다고 생각함.<br/>
`s`값이 `eng` 배열을 돌면서 배열의 값과 `s`의 문자열 중에 동일한 값이 있다면 그 문자열을 index 값으로 치환해야겠다고 생각함.<br/>
**`indexOf` 메서드를 사용**하여 `eng` 배열의 값을 넣어 확인했고, **값이 없으면 -1을 반환하는 특징**을 가지고 문제에 접근.

처음엔 return 값에 `s`만 넣었더니 `s`의 값이 String 이여서 코드 실행에서 실패하였고, Number로 타입을 변환해주었음.

```javascript
const solution = (s) => {
    const eng = ["zero", "one", "two", "three", "four", "five", "six", "seven", "eight", "nine"];
    
    eng.forEach((item, idx) => {
        if(s.indexOf(item) !== -1) s = s.replace(item, idx);
    })
    
    return Number(s)
}
```

최초 작성은 위와 같은 코드였고, 테스트 코드에서 이상이 없어서 문제를 제출을 하였더니 10개의 테스트 중에 3개가 실패하였고,<br/>
“oneoneoneone” 이라는 테스트 케이스를 추가하였더니 “1oneoneone” 이라는 값이 return 되었다.


### 왜 한 개만 변환되었을까???
**`replace` 메서드는 기본적으로 문자열에서 처음 일치하는 것만 바꿔**주기 때문에 모든 문자열을 바꾸기 위해 정규표현식을 사용 하였고 `s = s.replace(/item/g, idx)`를 했으나,<br/>
동작을 하지 않아 `s`의 length 만큼 돌려서 `s`를 `replace` 시키겠다는 발상을 함.<br/>
그래서 for 문을 추가하였고, 제출할 수 있었다.

## 풀이 보완
### 보완 1.
```javascript
const solution = (s) => {
    // ...
    
    eng.forEach((item, idx) => {
        s = s.replace(item, idx);
    })
    
    // ...
}
```

풀이를 보완하기 위해 확인 중 위와 같이 `indexOf`메서드를 사용하지 않아도 문자열이 잘 치환되는 걸 확인. 불필요 코드를 줄일 수 있었다.<br/>
동일한 문제점으로는 **여전히 같은 문자열은 치환되지 않는다**.

그래서 다시 한번 정규표현식에 대해 검색을 해봤다. 역시나 정규표현식을 생성하는 과정에서 방법을 잘 몰라서 안되었던 것이다.<br/>
**정규 표현식 리터럴은 고정된 문자열과 일치**해야하고, item은 고정값이 아니기 때문에 **동적으로 생성된 정규 표현식인 `new RegExp` 를 사용**해야 했다.

```javascript
const solution = (s) => {
    // ...
    
    eng.forEach((item, idx) => {
        const regex = new RegExp(item, 'g');
        s = s.replace(regex, idx);
    })
    
    // 또는
    
    eng.forEach((item, idx) => {
        s = s.replace(new RegExp(item, 'g'), idx);
    })
    
    // ...
}
```

#### replace의 사용법
```bash
.replace("origin_str", "change_str") : 첫 "origin_str"을 "change_str"로 바꾼 문자열

g → .replace(/origin_str/g, "change_str") : 모든 "origin_str"을 "change_str"로 바꾼 문자열

i → .replace(/origin_str/i, "change_str") : 대소문자 구분 없이, 첫 "origin_str"을 "change_str"로 바꾼 문자열

gi → .replace(/origin_str/gi, "change_str") : 대소문자 구분 없이, 모든 "origin_str"을 "change_str"로 바꾼 문자열
```

### 보완 2.
이건 ‘다른 사람 풀이’ 를 보면서 감탄한 코드
```javascript
function solution(s) {
    let numbers = ["zero", "one", "two", "three", "four", "five", "six", "seven", "eight", "nine"];
    var answer = s;
    
    for(let i=0; i< numbers.length; i++) {
        let arr = answer.split(numbers[i]);
        answer = arr.join(i);
    }
    
    return Number(answer);
}
```

`split`과 `join` 메서드를 이용해서 푼 풀이법.<br/>
문자열을 변환하는 `split` 과 배열을 변환하는 `join` 을 이렇게 사용할 수 있고, 원리를 잘 알고 있어야한다고 느낌.

원리를 설명하자면,<br/>
`s` 로 "one4seveneight” 라는 문자열이 들어왔을 때, “one” 기준으로 `split` 메서드를 사용하여 분할하면 [”” , “4seveneight”] 라는 배열이 생성.<br/>
“one”을 기준으로 분할하기 때문에 첫 번째 요소는 “one” 앞에 아무것도 없어서 빈 문자열을 반환, 두 번째 요소는 “one” 이후의 나머지 문자열을 반환.

`join` 메서드는 인자를 구분자로 하여 배열의 각 요소 사이에 인자를 삽입하므로<br/>
첫 번째 요소는 빈 문자열이기 때문에 첫 번째 요소와 두 번째 요소 사이에 “one”의 인덱스 값이 삽입되어 “14seveneight”을 반환한다.

# 팀 개발자들의 생각

> 👩‍💻 쉰님 :<br/>
처음엔 s 를 반복문 하려고 했으나, “zero” 부터 “nine”을 배열에 담아놓고 s 문자열에서 찾기를 생각했고 형변환 시켜서 answer 구했음

> 👨‍💻 탠님 :<br/>
동일한 방법으로 푸는 도중 궁금한 점을 검색하다 문제풀이를 봐버려서 이번 문제는 PASS 하였다.

나는 여기서 궁금함이 생겼다.<br/>
replace를 사용했냐고 물었더니 replace를 사용했다고 했다.<br/>
**replace는 분명 1개만 변환시켜주는 것**으로 아는데, 정규 표현식 사용하지 않았다고 한다.<br/>
그럼 어떻게 문제를 해결했냐고 했을 때 java에는 **replaceAll 메서드**가 있다고 했다.<br/>
JavaScript도 확인 결과 [replaceAll 메서드](https://velog.io/@bami/Javascript-String.replaceAll)를 사용할 수 있었다.

단순 치환 → replace / 복잡 치환 → replaceAll

# 평가
개발자분들과 같이 공부하면서 새로운 메서드도 알게되었고,<br/>
무엇보다 split, join 메서드를 사용하는 방법을 좀 더 깊게 알게 되었다.