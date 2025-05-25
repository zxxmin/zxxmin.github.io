---
title: 성격 유형 검사하기
date: 2024-07-01 00:00:01
permalink: /codingTest/118666
categories:
  - 알고리즘
  - LV.1
tag:
  - 알고리즘
  - LV.1
---

2018 KAKAO TECH INTERNSHIP

# 문제내용
4개 지표로 성격 유형을 구분하며 각 지표에서 두 유형 중 하나로 결정.

4개의 지표가 있으므로 성격 유형은 총 16( 2 x 2 x 2 x 2 ) 가지가 나옴. 예를 들어, “RFMN” 이나 “TCMA” 와 같은 성격 유형이 있다.<br/>
4번 지표로 예시로 아래의 표처럼 점수를 매길 수 있음.

검사 결과는 **모든 질문의 성격 유형 점수를 더하여 각 지표에서 더 높은 점수를 받은 성격 유형이 검사자의 성격 유형이라고 판단**.<br/>
단, 하나의 지표에서 **각 성격 유형 점수가 같으면, 두 성격 유형 중 사전 순으로 빠른 성격 유형을 검사자의 성격 유형이라고 판단**.<br/>
질문마다 판단하는 지표를 담은 1차원 문자열 배열 `survey` 와<br/>
각 질문마다 선택한 선택지를 담은 1차원 정수 배열 `choices` 가 매개변수로 주어진다.<br/>
검사지의 성격 유형 검사 결과를 지표 번호 순서대로 return 하도록 solution 함수를 완성해야한다.

# 제출한 답
```javascript
const solution = (survey, choice) => {
    let newsurvey = { R: 0, T: 0, C: 0, F: 0, J: 0, M: 0, A: 0, N: 0 };
    survey.forEach((el, i) => {
        switch (choice[i]) {
            case 7 :
                newsurvey[survey[i][1]] += 3
                break;
            case 6 :
                newsurvey[survey[i][1]] += 2
                break;
            case 5 :
                newsurvey[survey[i][1]] += 1
                break;
            case 3 :
                newsurvey[survey[i][0]] += 1
                break;
            case 2 :
                newsurvey[survey[i][0]] += 2
                break;
            case 1 :
                newsurvey[survey[i][0]] += 3
                break;
            default :
                break;

        }
    })


    let answer = '';
    answer += newsurvey.R >= newsurvey.T ? "R" : "T";
    answer += newsurvey.C >= newsurvey.F ? "C" : "F";
    answer += newsurvey.J >= newsurvey.M ? "J" : "M";
    answer += newsurvey.A >= newsurvey.N ? "A" : "N";

    return answer
}
```

## 🚩나의 생각
### 내가 하고싶었던 방법
최초 작성 시 newsurvey 를 선언만하고, switch 문 에서는 할당만 해주었다.<br/>

```javascript
const solution = (survey, choice) => {
    let newsurvey = {};
    
   // ...
   
    switch (choice[i]) {
        case 7 :
            newsurvey[survey[i][1]] = 3
            break;

        // ...
    }
		
}
```

이 때 발생한 오류는 입출력 예 #2 의 값을 넣었을 경우 `newsurvey` 에 들어오는 값이 `survey` 의 마지막 “TR”을 비교한 값인 R:3, T:1 이라는 값만 들어왔다.

### 왜 마지막에 들어온 값인 R: 3, T: 1 이라는 값만 설정되었을까?
이유는  `switch 문` 에서 할당만 해주고있어서 마지막에 할당 된 값이 들어가는 것 이였다.<br/>
그래서 코드를 더하기 할당(+=) 으로 변경해주었다.<br/>

```javascript
const solution = (survey, choice) => {
		let newsurvey = {};
	
		// ...
   
    switch (choice[i]) {
        case 7 :
            newsurvey[survey[i][1]] += 3
            break;

        // ...
    }
		
}
```

이제 값들이 모두 더해져서 나올 것으로 기대했는데, 결과 값은 NaN으로 들어왔다.

### 이번엔 왜 NaN으로 설정되는걸까?
이유는 처음에 `newsurvey` 에 값을 할당해주지 않아서 Number가 아닌 빈 값에 더하기 할당을 하려고 하니까 되지 않았던 것이다.<br/>
그래서 `newsurvey` 에 미리 초기값을 할당해주었다.<br/>

```javascript
const solution = (survey, choice) => {
    let newsurvey = { R: 0, T: 0, C: 0, F: 0, J: 0, M: 0, A: 0, N: 0 };

    // ...
   
    switch (choice[i]) {
        case 7 :
            newsurvey[survey[i][1]] += 3
            break;

        // ...
    }
		
}
```
이렇게 변경해주니까 값이 잘 들어왔다.<br/>
`switch문` 내부 코드가 비슷한 코드로 반복되고 있다. 보완하면 충분히 보완할 수 있을 것 같은데…

## 풀이 보완
### 보완 1.
`newsurvey[survey[i][1]] += 3` 이부분이 공통으로 되고있어서 함수로 만들기로 생각함.<br/>
`forEach문` 밖에 **함수를 선언**해주었다.<br/>

```javascript
const solution = (survey, choice) => {
    let newsurvey = { R: 0, T: 0, C: 0, F: 0, J: 0, M: 0, A: 0, N: 0 };
    
    const totalScore = (type, points) => newsurvey[type] += points;
    
    // ...
		
}
```

함수로 변경하면서 반복하는 코드를 제거하기 위해 `switch문` 을 `if문` 으로 변경하였고, survey[i][1] 이렇게 표현했던 부분도 **비구조화 할당**으로 변경하였다.<br/>
**Math.abs() 라는 절대값 구하는 메서드**를 이용해 음수값도 절대값으로 변경하였다.<br/>

```javascript
const solution = (survey, choice) => {
		let newsurvey = { R: 0, T: 0, C: 0, F: 0, J: 0, M: 0, A: 0, N: 0 };
		
		const totalScore = (type, points) => newsurvey[type] += points;
	
		survey.forEach((el, i) => {
        const [firstType, lastType] = el;
        const score = choice[i]
        const points = Math.abs(score - 4);
        
        if (score > 4) totalScore(lastType, points)
        else totalScore(firstType, points);
    })
		
}
```

### 보완 2.
역시 간단좌가 있었다.<br/>

```javascript
function solution(survey, choices) {
    const MBTI = {};
    const types = ["RT","CF","JM","AN"];

    types.forEach((type) =>
        type.split('').forEach((char) => MBTI[char] = 0)
    )

    choices.forEach((choice, index) => {
        const [disagree, agree] = survey[index];

        MBTI[choice > 4 ? agree : disagree] += Math.abs(choice - 4);
    });

    return types.map(([a, b]) => MBTI[b] > MBTI[a] ? b : a).join("");
}
```

위 코드는 비교한 결과 값을 담을 newsurvey 와 같은 역할을 하는 변수 MBTI 를 선언하고,<br/>
types에 비교해야하는 값들을 같이 묶어서 배열에 담아줬다.

```javascript
let newsurvey = { R: 0, T: 0, C: 0, F: 0, J: 0, M: 0, A: 0, N: 0 };
```

내가 위 처럼 하나하나 할당한 값을

```javascript
types.forEach((type) =>
    type.split('').forEach((char) => MBTI[char] = 0)
)
```

이런식으로 반복문을 사용해서 값을 담아줬다. 이렇게 사용해야 **성격 유형 알파벳이 변경되어도 변경되는 값들을 더 쉽게 바꿀 수 있을 것 같아** 좋은 방법인 것 같다.<br/>
나는 survey를 반복문을 돌렸다면 위 코드는 choices를 반복문으로 돌려 MBTI 변수에 값을 할당해 주었고, 변수 types에 map 을 사용해서 조합한 결과를 만들어 주었다.


# 팀 개발자들의 생각

> 👩‍💻 쉰님 :<br/>
할당을 먼저 했음.<br/>
각각 값을 넣어주다가 예외 처리를 해줘야하는 부분이 생겼고 코드를 변경하게됨<br/>
map 메서드 사용 map.put 0으로 설정 0으로 안해주면 연산이 안되어서 초기화 해줌.

> 👨‍💻 탠님 :<br/>
양수, 음수 나눠서 각각 switch 로 만드느라 코드가 길어짐 직관적이지 않고 간결성이 떨어져서 다시 로직을 생각하고 있음.<br/>
실패했던 부분 :<br/>
R따로 T 따로인데 하나로 묶어서 음수는 R 양수는 T하려고 했는데 실패함<br/>
원인을 아직 파악 중.

# 평가
아직 map, split, join에 대해서 잘 모르는 것 같다.<br/>
이 부분만 사용하는 법을 명확히 이해한다면 더 좋은 코드를 만들 수 있을 것 같다.