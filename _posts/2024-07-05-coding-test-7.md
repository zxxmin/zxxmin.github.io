---
title: 신규 아이디 추천
date: 2024-07-05 00:00:01
permalink: /codingTest/72410
categories:
  - 알고리즘
  - LV.1
tag:
  - 알고리즘
  - LV.1
---

2021 KAKAO BLIND RECRUITMENT

# 문제내용
새로 가입하는 유저들이 카카오 아이디 규칙에 맞지 않는 아이디를 입력했을 때, 입력된 아이디와 유사하면서 규칙에 맞는 아이디를 추천해주는 프로그램을 개발하는 것.

카카오 아이디의 규칙<br/>
- 아이디의 길이는 3자 이상 15자 이하
- 아이디는 알파벳 소문자, 숫자, 빼기(-), 밑줄(_), 마침표(.) 문자만 사용할 수 있다.
- 단, 마침표(.)는 처음과 끝에 사용할 수 없으며 또한 연속으로 사용할 수 없다.

7단계의 순차적인 처리 과정<br/>
> 1단계 new_id 의 모든 대문자를 대응되는 소문자로 치환<br/>
2단계 new_id 에서 알파벳 소문자, 숫자, 빼기(-), 밑줄( _ ), 마침표(.)를 제외한 모든 문자 제거<br/>
3단계 new_id 에서 마침표(.)가 2번 이상 연속된 부분을 하나의 마침표(.)로 치환<br/>
4단계 new_id 에서 마침표(.)가 처음이나 끝에 위치한다면 제거<br/>
5단계 new_id 가 빈 문자열이라면, new_id에 “a”를 대입<br/>
6단계 new_id 의 길이가 16자 이상이면, new_id의 첫 15개의 문자를 제외한 나머지 문자들을 모두 제거.<br/>
만약 제거 후 마침표(.)가 new_id 끝에 위치한다면 끝에 위치한 마침표(.) 문자 제거<br/>
7단계 new_id 의 길이가 2자 이하라면, new_id의 마지막 문자를 new_id의 길이가 3이 될 때까지 반복해서 끝에 붙인다.


# 제출한 답
```javascript
const solution = (new_id) => {
  let answer = new_id.toLowerCase().replaceAll(/[^a-z0-9-_.]+/g, '').replaceAll(/(?<=\.)\./g, '').replaceAll(/(^\.|\.$)/g, '');
  answer = answer === '' ? 'a' : answer
  answer = answer.slice(0,15).replaceAll(/(^\.|\.$)/g, '');

  while(answer.length < 3) answer += answer.slice(-1);

  return answer;
}
```

## 🚩나의 생각
7단계의 순차적인 처리 과정을 그대로 적용했다.

1. 1단계 대문자를 소문자로 치환 → toLowerCase()<br/>
```javascript
new_id.toLowerCase()
```
**toLowerCase()**<br/>
- 문자열의 모든 대문자를 새로운 **소문자로 변환**해서 반환
- 반환만 하기 때문에 원본 문자열은 바뀌지 않는다.

**toUpperCase()**<br/>
- 문자열의 모든 소문자를 새로운 **대문자로 변환**해서 반환합니다.

2. 2단계 알파벳 소문자, 숫자, 빼기(-), 밑줄 ( _ ), 마침표(.)를 제외한 모든 문자 제거<br/>
```javascript
new_id.toLowerCase().replaceAll(/[^a-z0-9-_.]+/g, '')
```

**replaceAll(pattern, replacement)**<br/>
- pattern 의 모든 일치 항목이 **replacement로 대체된** 새 문자열을 반환

**/정규식 플래그/g**<br/>
- 정규식 플래그 g : Global → 문자열 내의 모든 패턴을 검색

**/정규식 갯수 반복 패턴+/**<br/>
- 최소 한개 or 여러개

**/[  ]/**<br/>
- 괄호안의 문자들 중 하나.
- /abc/ : “abc”를 포함하는
- **/[abc]/ : “a” 또는 “b” 또는 “c”를 포함하는**
- [다-바] : “다” 또는 “라” 또는 “마” 또는 “바”

**[^문자]**<br/>
- 괄호안의 문자를 제외한 것
- 대괄호 안에서 쓰면 제외, 대괄호 밖에서 쓰면 시작점

3. 3단계 마침표(.)가 2번 이상 연속된 부분을 하나의 마침표(.)로 치환<br/>
```javascript
new_id.toLowerCase().replaceAll(/[^a-z0-9-_.]+/g, '').replaceAll(/(?<=\.)\./g, '')
```

**/(?≤)/**<br/>
- 뒤쪽 일치
- /(?≤ab)c/ : ab 뒤에 c 가 인

4. 4단계 마침표(.)가 처음이나 끝에 위치한다면 제거<br/>
```javascript
new_id.toLowerCase().replaceAll(/[^a-z0-9-_.]+/g, '').replaceAll(/(?<=\.)\./g, '')
	.replaceAll(/(^\.|\.$)/g, '');
```

**/^문자열/**<br/>
- 특정 문자열로 시작 (시작점)

**/문자열$/**<br/>
- 특정 문자열로 끝남 (종착점)

**/[ a | b ]/**<br/>
- OR

5. 5단계 빈 문자열이라면, new_id에 “a”를 대입<br/>
```javascript
let answer = new_id.toLowerCase().replaceAll(/[^a-z0-9-_.]+/g, '')
	.replaceAll(/(?<=\.)\./g, '').replaceAll(/(^\.|\.$)/g, '');

answer = answer === '' ? 'a' : answer
```

6단계 길이가 16자 이상이면, new_id의 첫 15개의 문자를 제외한 나머지 문자들을 모두 제거.<br/>
만약 제거 후 마침표(.)가 new_id 끝에 위치한다면 끝에 위치한 마침표(.) 문자 제거

7단계 길이가 2자 이하라면, new_id의 마지막 문자를 new_id의 길이가 3이 될 때까지 반복해서 끝에 붙인다.

처음 생각으로는 문자열의 길이가 3 이상 15 이하인 문자열은 제거하거나 추가하는 연산을 처리하지 않아도 되기때문에 조건문으로 처리하려고 했다.

```javascript
if(answer.length > 15) {
    return answer.slice(0,15).replaceAll(/(^\.|\.$)/g, '');
} else if (answer.length < 3) {
    while(answer.length < 3) answer += answer.slice(-1);
}
```

- 아래는 조건문이 추가된 처리 속도 이다.
  ![](/assets/images/codingtest/coding_test_7_1.png)

  조건문을 빼고 작성해봤다.

```javascript
answer = answer.slice(0,15).replaceAll(/(^\.|\.$)/g, '');
while(answer.length < 3) answer += answer.slice(-1);
```

- 아래는 조건문을 뺀 처리 속도이다.
  ![](/assets/images/codingtest/coding_test_7_2.png)

실제로 두개의 처리 속도는 0.5ms 차이가 났다.
![](/assets/images/codingtest/coding_test_7_3.png)

조건을 줘서 맞는 조건만 타게 해야 더 빠른 처리를 할 거라는 지금까지 갖고 있던 나의 생각은 오류였다…… 진짜 꽤나 충격적이였다.

### 조건문이 없는 코드가 더 효율적인 이유

1. **조건문 평가의 오버헤드**<br/>
   **조건문을 평가하는 데에도 시간이 걸린다**. 문자열 길이가 15이상일 때, 3 이하일 때의 조건문 평가가 필요하고 있다.
2. **일관된 실행 흐름**<br/>
   조건문이 없는 코드는 항상 동일한 작업 흐름을 따른다. 이는 **CPU의 분기 예측의 실패가 적어진다**. 분기 예측 실패가 적을수록 더 빠르게 실행된다.

따라서, 분기 처리 없이 항상 동일한 작업을 수행하는 두 번째 코드가 더 빠를 수 있다.

[참고 블로그](https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-%EC%A0%95%EA%B7%9C%EC%8B%9D-RegExp-%EB%88%84%EA%B5%AC%EB%82%98-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EC%89%BD%EA%B2%8C-%EC%A0%95%EB%A6%AC)