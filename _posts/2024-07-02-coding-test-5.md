---
title: 이진 변환 반복하기
date: 2024-07-02 00:00:01
permalink: /codingTest/70129
categories:
  - 알고리즘
  - LV.2
tag:
  - 알고리즘
  - LV.2
---

월간 코드 챌린지 시즌1

# 문제내용
0과 1로 이루어진 어떤 문자열 x에 대한 이진 변환을 다음과 같이 정의

1. x의 모든 0을 제거
2. x의 길이를 c 라고 하면, x를 “c를 2진법으로 표현한 문자열”로 바꾼다.

예를 들어, `x = “0111010”` 이라면, x에 이진 변환을 가하면 `x = “0111010” → “1111” → “100”` 이된다.<br/>
0과 1로 이루어진 문자열 `s` 가 매개변수로 주어지고, `s` 가 “1”이 될 때까지 계속해서 `s` 에 이진 변환을 가했을 떄, 
이진 변환의 횟수와 변환 과정에서 제거된 모든 0의 개수를 각각 배열에 담아 return 하도록 solution 함수를 완성해야한다.

# 제출한 답
```javascript
const solution = (s) => {
    let zero = 0;
    let loop = 0;

    while (s.length > 1) {
        s = s.split('').map(el => el === '1' ? '1' : (++zero, '')).join('').length.toString(2)
        loop++
    }

    return [loop,zero]
}
```

## 🚩나의 생각
최초 작성한 코드는 아래와 같다.<br/>

```javascript
const solution = (s) => {
    let zero = 0;
    let loop = 0;

    for(let i = 0; i <= s.length; i++) {
        s = s.split('').map(el => el === '1' ? '1' : (++zero, '')).join('').length.toString(2)
        loop = i+1
    }

    return [loop,zero]
}
```

이중 for 문으로 돌려야하나 고민을 했지만<br/>

```javascript
s = s.split('').map(el => el === '1' ? '1' : (++zero, '')).join('').length.toString(2)
```

기존 값에서 0을 빼고 1만 있는 상태의 길이를 구해 이진법으로 바꾸는 방법을 한줄로 작성을 했는데 이중 for 문을 돌리면 이 줄을 한줄로 사용할 수 없고 중간중간 잘라야하는 일이 생긴다.<br/>
그래서 `while문` 으로 변경할까 고민했는데 무한 루프의 늪에 몇번 빠지고 고심 끝에 평범하게 `for문`을 선택했다.

그런데 위 코드가 마지막 케이스인 “1111111” 이 되지 않았다. 기대값은 [4 , 1 ] 인데 내가 만든 코드에서 값은 [ 3, 0 ] 이 되었다. 
“10”으로 만들어 진 뒤 “1”로 변환하는 부분이 되지 않아서 “1”로 변환값이 적용되지 않았고, “10”에서 제거된 “0” 값이 적용되지 않았다.

### 왜 “10” 다음에 “1”로 변환되지않고 멈췄을까???
이유는 i 때문이였다. i는 s의 length 값이 변하여도 초기값으로 가지않고 계속 추가 되었고, i가 s의 length 값과 동일해 질 때 멈췄기 때문이다. 그래서 i를 0으로 초기화 시켜줬다.<br/>
i를 0으로 초기화 하면서 loop = i+1 값이 마지막 length의 값만 적용 되어서 loop++로 해서 적용했다.

그렇게 변경된 코드는 아래와 같다.<br/>

```javascript
const solution = (s) => {
    let zero = 0;
    let loop = 0;

    for(let i = 0; i <= s.length; i++) {
        s = s.split('').map(el => el === '1' ? '1' : (++zero, '')).join('').length.toString(2)
        loop++;
        if(s.length === 1 && s === '1') break;
        else i=0;
    }

    return [loop,zero]
}
```

위 코드에서도<br/>

```javascript
if(s.length === 1 && s === '1') break;
else i=0;
```

이 부분이 꼭 필요한 구문이 아닌 것 같았다. i를 포기못한 이유가 loop 값 때문이였는데 loop++로 변경하고, 
s의 length가 계속 변경되기 때문에 while 의 조건문에서도 i가 필요없이 s.length > 1 로 조건을 주어도 된다는 생각이 들었고 마침내 최종 코드를 작성할 수 있었다.

## 풀이 보완
### 보완 1.
위 코드가 생각보다 높은 점수를 받지 못했다. 그래서 코드 내에서 좀 더 변경할 수 있는게 있을지 찾아보았다. 
코드를 좀 더 **가독성 있고 명확하게 작성**해야하는 것 같아 `map , join` 메서드를 `reduce`로 변경했다.<br/>
`reduce` 는 배열의 각 요소에 대해 콜백함수를 실행하고 **누적 결괏값을 반환하는 메서드**이다.<br/>

```javascript
function solution(s) {
    let zero = 0;
    let loop = 0;

    while (s.length > 1) {
        const onesCount = s.split('').reduce((count, el) => {
            if (el === '1') {
                return count + 1;
            } else {
                zero++;
                return count;
            }
        }, 0);

        s = onesCount.toString(2);
        loop++;
    }

    return [loop, zero];
}
```

`reduce` 를 사용하면서 ‘1’일 때 값을 합산해서 그 합산 값을 이진법으로 변경하는 코드로 변경했다.<br/>
정확성 테스트를 통해 빨라진걸 확인할 수 있었다.

- 초기 코드의 정확성 테스트
  ![](/assets/images/codingtest/coding_test_5_1.png)

- reduce 메서드를 사용하고 정확성 테스트
  ![](/assets/images/codingtest/coding_test_5_2.png)



# 팀 개발자들의 생각

> 👩‍💻 쉰님 :<br/>
이중반복문 사용<br/>
s가 1이아닐때까지 돌리는 반복문 크게 하나<br/>
변수를 answer 배열로 담아서 answer[0], answer [1]로 구현

# 평가
코드를 간단하고 짧게 써야지만 좋은 코드이고 빠른 코드인 줄 알았다.<br/>
하지만 보완1. 코드처럼 좀 길어져도 사용해야하는 코드에 명확하게 사용할 수 있어야 더 가독성이 높고 속도가 빨라진다는 것을 알게되었다.