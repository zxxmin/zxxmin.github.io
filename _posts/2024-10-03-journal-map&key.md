---
title: map과 key
date: 2024-10-03 00:00:01
permalink: /toy/journal/map&key
categories:
  - Toy project
  - journal
tag:
  - Toy project
  - journal
---

# key
map에서 컴포넌트를 반복 렌더링할 경우 각 항목에 고유한 key를 부여하는 것이 중요.<br/>
key는 버츄얼 돔에서 리액트가 어떤 항목이 변경되고, 추가되고, 제거 되었는지 식별하는데 도움이 되므로 성능 최적화와 UI 업데이트 효율성에 중요한 역할

# key 역할
1. 성능 최적화<br/>
   리액트가 DOM을 효율적으로 업데이트 할 수 있도록 도움.<br/>
   변동이 있는 항목만 업데이트하게 되어 성능이 향상
2. 중복 방지<br/>
   key가 있어야만 리액트가 항목을 고유하게 식별 가능.<br/>
   동일한 key를 가진 항목이 여러 개 있으면 리액트는 어떤 항목을 업데이트해야 할지 혼란에 빠짐.

# 좋은 key
고유하고 변하지 않는 값. 즉, 배열의 인덱스 대신, 고유한 ID나 변하지 않는 값을 사용하는 것이 좋음

# 문제
input에 onChange 시 한 글자만 써도 foucs가 out이 된다.

```javascript
{editHabitNm.map((name) => (
   <tr key={name}>
      <th>
         {isEdit ? (
            <input
               type='text'
               value={name || ''}
               onChange={(e) => onChangeInput(e, i)}
               title={name}
            />
         ) : (
            name
         )}
      </th>
   </tr>
))}
```

# 원인
위와 같이 key를 name으로 주면 name 값이 입력할 때 마다 변경이 되기 때문에 리액트는 해당 컴포넌트가 새롭게 생성된것으로 인지하고 이전 상태를 잃어버리기 때문에 뚝뚝 끊기는 현상 발생

# 해결
```javascript
{editHabitNm.map((name, i) => (
    <tr key={i}>
        <th>
            {isEdit ? (
                <input
                    type='text'
                    value={name || ''}
                    onChange={(e) => onChangeInput(e, i)}
                    title={name}
                />
            ) : (
                name
            )}
        </th>
    </tr>
))}
```

key 값을 i로 주어 name 보다는 고유하게 변경하니까 안 끊기고 기능 구현이 잘 되었음.

# 평가
위 상황은 변하지 않는 index라서 key 값을 주었고, 변할 우려가 있는 index의 경우에는 data를 받아온다면 고유의 ID 값이나 다른 값을 주어야겠다.