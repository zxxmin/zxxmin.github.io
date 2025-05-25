---
title: 로또의 최고 순위와 최저 순위
date: 2024-08-09 00:00:01
permalink: /codingTest/77484
categories:
  - Algorithm
  - LV.1
tag:
  - Algorithm
  - LV.1
---

2021 Dev-Matching: 웹 백엔드 개발자 (상반기)

# 문제내용
실생활에서 로또와 당첨 방법이 다르지만 아래와 같이 로또 순위를 정한다고 가정.

민우의 동생이 로또에 낙서를 하여, 일부 번호를 알아볼 수 없게 되었음.<br/>
당첨 번호 발표 후 로또로 당첨이 가능했던 최고 순위와 최저 순위가 궁금해서 알아볼 수 없는 번호를 0으로 표기하고, 당첨 번호 6개와 비교.

- 순서와 상관없이, 구매한 로또에 당첨 번호와 일치하는 번호가 있으면 맞힌걸로 인정
- 알아볼 수 없는 (= 0) 두 개의 번호를 각각 10, 6이라고 가정하면 3등 당첨
- 알아볼 수 없는 (= 0) 두 개의 번호를 각각 11, 7이라고 가정하면 5등에 당첨


# 제출한 답
```javascript
const solution = (lottos, win_nums) => {
  const level = [6, 5, 4, 3, 2, 1];

  let highest = 0;
  let lowest = 0;

  lottos.forEach(lotto => {
    if(lotto === 0) highest++
    else if (win_nums.includes(lotto)) lowest++
  })

  highest = highest + lowest || 1
  lowest = lowest || 1

  const answer = [highest, lowest].reduce((acc, el) => {
    level.forEach((ele, i) => {
      if(ele === el) acc.push(i + 1)}
    );

    return acc
  }, [])

  return answer
}
```

## 🚩나의 생각
이 문제는 풀어야할 로직 생각은 쉬웠다. 막상 코드를 짜면서 생각보다 늪으로 빠졌던 것 같다.<br/>
최저 → 0을 제외한 숫자에서 맞은 갯수<br/>
최고 → 최저의 갯수 + 0의 갯수<br/>
최고, 최저를 0으로 선언 및 할당을 해주고 lottos에 0이 있으면 최고에 +1을 해주고 당첨 번호에 lottos 번호가 있으면 최저에 +1을 해주고 최고는 나온 값에서 +최저 값을 해줬다.

처음에 level에 값은 등수를 배열에 담았다. 1개 이하로 맞으면 6등이기때문에 배열에 중복값을 안주고 계산에서 해결하려고 했다.<br/>
그래서 아래와 같은 계산식이 탄생했다.

```javascript
highest = highest + lowest || 1
lowest = lowest || 1
```

|| 논리 연산자는 좌항이 falsy 한 값이면 우항의 값이 반환된다.<br/>
즉, 0 일때는 1로 계산이 되어 다른 값들과 같이 인덱스에 맞는 등수를 계산하게 만들었다.<br/>

위에 제출한 코드에서는 level 이라는 배열안에 [ 6, 5, 4, 3, 2, 1] 로 1~6개를 맞췄을 때를 생각한 6개의 숫자만 담았지만, 
0~6개를 맞췄을 때를 생각하고 7개의 숫자를 담는다면 더 간단하게 풀 수 있었다.


# 풀이 보완
## 보완 1.
```javascript
const solution = (lottos, win_nums) => {
    const level = [6, 6, 5, 4, 3, 2, 1];

    let zeroCount = 0;
    let matchCount = 0;

    lottos.forEach(lotto => {
        if (lotto === 0) {
            zeroCount++;
        } else if (win_nums.includes(lotto)) {
            matchCount++;
        }
    });

    const highestRank = level[matchCount + zeroCount];
    const lowestRank = level[matchCount];

    return [highestRank, lowestRank];
};
```
0개를 맞췄을 때에 대한 등수도 배열에 포함하니 인덱스에 맞는 등수가 되었고, 논리연산자도 필요 없어졌으며 각각의 등수를 구하기도 더 간단하게 계산이 되었다.

## 보완 2.
```javascript
const solution = (lottos, win_nums) => {
    const rank = [6, 6, 5, 4, 3, 2, 1];
    
    const matched = lottos.filter(lotto => win_nums.includes(lotto)).length;
    const zeros = lottos.filter(lotto => lotto === 0).length;
    
    const highest = rank[matched + zeros];
    const lowest = rank[matched];
    
    return [highest, lowest];
}
```
forEach 문을 filter로 사용하면 더 간단하게 사용할 수 있었다.


# 평가
메서드를 다양한 곳에서 다양하게 사용해야하는데, 한가지의 메서드에 꽂히면 다른 메서들이 잘 안떠올릴 때가 있다.<br/>
아마 최초 작성 시 reduce에 꽂혔던 것같다.<br/>
문제를 그대로 읽고 풀어쓸 수 있어야하는데, 꽂힌 메서드때문에 더 꼬이는 부분도 있는 것 같고 위치도 고려를 해봐야하는 것 같다.