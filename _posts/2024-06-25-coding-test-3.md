---
title: 비밀지도
date: 2024-06-25 00:00:01
permalink: /codingTest/17681
categories:
  - 알고리즘
  - LV.1
tag:
  - 알고리즘
  - LV.1
---

2018 KAKAO BLIND RECRUITMENT

# 문제내용
네오가 프로도의 비상금 장소를 찾는 지도와 지도 암호를 해독할 방법이 적힌 메모지를 습득하였고 암호 해독을 하여 비상금을 훔쳐라

> • 지도는 한 변의 길이가 n 인 정사각형 배열 형태로, 각 칸은 “공백”(” “) 또는 “벽”(”#”) 두 종류로 이루어져있다.<br/>
• 전체 지도는 두 장의 지도를 겹쳐서 얻을 수 있다.<br/>
각각 “지도1”과 “지도2” 라고 하면 지도1 또는 지도2 중 어느 하나라도 벽인 부분은 전체 지도에서도 벽이다. 지도 1과 지도2에서 모두 공백인 부분은 전체 지도에서도 공백이다.<br/>
• “지도1”과 “지도2”는 각각 정수 배열로 암호화 되어있다.<br/>
• 암호화된 배열은 지도의 각 가로줄에서 벽 부분을 1 , 공백 부분을 0 으로 부호화 했을 때 얻어지는 이진수에 해당하는 값의 배열이다.<br/>

# 제출한 답
```javascript
const solution = (n,arr1,arr2) => {
    arr1 = arr1.map(arr => arr.toString(2).padStart(n, '0');
    arr2 = arr2.map(arr => arr.toString(2).padStart(n, '0');

    return arr1.map((el, idx) => String(Number(el)+Number(arr2[idx])).padStart(n, '0').replaceAll(2, 1).replaceAll(1, '#').replaceAll(0, ' '))
}
```

## 🚩나의 생각
### 내가 하고싶었던 방법
arr1과 arr2의 배열을 2진법으로 바꾼 후 둘을 비교해서 둘 다 0일 경우 0을 반환하고 아니면 1을 반환한 후 1 이면 # 아니면 0 이면 ‘ ‘을 반환 하고 싶었다.

arr1 배열과 arr2 배열은 만들었는데 두개의 값을 비교해서 원하는 값을 만들게 되면 코드가 길어졌고 이 방법이 아니라고 생각이 들어서 생각을 바꿨다.

그래서 생각해 낸 방법이 두개의 값을 합친 후 최대 수는 2가 나올거라고 예상을 했고,<br/>
2 → 1로 1 → ‘#’으로 0 → ‘ ‘ 로 `repalceAll` 을 해주었다.

저렇게 제출하면서도<br/>
```javascript
const arr1 = arr1.map(arr => arr.toString(2).padStart(n, '0');
const arr2 = arr2.map(arr => arr.toString(2).padStart(n, '0');
```
이 두 부분을 합치거나, 공통으로 사용할 수 있는데 내가 잘못하고 있구나 생각은 했다. 하지만 더 기가막힌 방법은 아직까지는 무리….


## 풀이 보완
역시… arr1, arr2 부분을 줄일 수 있는 방법이 있었다.<br/>
```javascript
const solution = (n,arr1,arr2) => arr1.map((a,idx) => (a | arr2[idx]).toString(2).padStart(n,0).replaceAll(0,' ').replaceAll(1,'#'))
```
기가막힌 코드에서 내 방식대로 손 좀 봤다.

||은 사용해봤는데 | 이건 처음 본 것 같다.<br/>
[비트 연산자](https://velog.io/@jiyaho/JS-%EB%B9%84%ED%8A%B8%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A2%85%EB%A5%98-%EC%84%A4%EB%AA%85-%EB%B0%8F-%EC%98%88%EC%A0%9C)라고 하는데, 
2진수를 연산할 때 사용하는 연산자로 10진수인 두 정수를 2진수로 변환 했을 때, 두 수가 1이 일치하는 자리를 찾아 1을 반환, 나머지는 0으로 반환하고 이후 2진수 → 10진수로 변환한 값을 리턴 해준다.

# 팀 개발자들의 생각

> 👩‍💻 쉰님 :<br/>
매개변수로 들어오는 배열 2개를 리스트2개로 만든 다음 n만큼 반복문 돌려줌.<br/>
첫번째 배열 2진법 바꾼후 만들어 놓은 리스트 안에 넣었다.<br/>
java에 padStart가 있는지 몰라서 반복문을 돌려서 앞에 0을 붙여줬다.<br/>
이중포문을 사용하였고 첫번째 리스트에 담긴 첫번째 값 중 첫번째 글자 이런식으로 하나하나 가져와서 1이면 # 아니면 공백으로 값을 도출했다.<br/>

> 👨‍💻 탠님 :<br/>
format 메서드와 stringBullder 메서드 사용했다.<br/>
string 과 string 합치면 메모리 할당과 메모리 해제시키는데 연산이 많아져서 성능이 좋지않다<br/>
긴 문자열은 stringBullder사용했다. → js로 찾아보기