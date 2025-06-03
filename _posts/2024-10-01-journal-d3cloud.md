---
title: d3-cloud 사용하기
date: 2024-10-01 00:00:01
permalink: /toy/journal/d3cloud
categories:
  - Toy project
  - journal
tag:
  - Toy project
  - journal
---

일정관리 및 습관관리를 작성하면서 동기부여를 위해 한달 간 가장 많이 작성한 말을 보여주고 싶었다.

# D3.js
데이터를 기반으로 문서를 조작하기위한 JavaScript 라이브러리.<br/>
HTML, SVG 및 CSS를 사용하여 데이터를 시각화하기 좋음.

chart.js는 사용해봤는데 d3.js를 사용해본 적이 없었다.<br/>
하지만 d3에는 word cloud 기능이 없어서 d3-cloud를 추가로 설치<br/>
[npm: d3-cloud](https://www.npmjs.com/package/d3-cloud)

# D3.js 사용
## 패키지 설치
```bash
npm install d3
npm install d3-cloud
```

## import
```bash
import * as d3 from 'd3'
import cloud from 'd3-cloud'
```

## 보여줄 단어 설정
```javascript
const getGoals = (goalType) => data
		.filter(item => item.year === yearToNum)
		.flatMap(item => item.months
		    .filter(months => months.month === monthToNum)
		    .map(months => months.goals[goalType])
		).flat();

const getHabit = (habitType) => data
    .filter(item => item.year === yearToNum)
    .flatMap(item => item.months
    .filter(months => months.month === monthToNum)
    .flatMap(months => months.habits.map(habit => habit[habitType]))
);

let habitNm = getHabit('name');
let long = getGoals('longTerm')
let short = getGoals('shortTerm')

const sumWords = [...habitNm.join(' ').split(' '), ...long.join(' ').split(' '), ...short.join(' ').split(' ')]
```

일기를 비밀이니까 제외하고 단기 목표, 장기 목표, 습관 Tracker에 있는 단어들만 조합.<br/>
위의 합쳐진 단어들을 중복 단어는 제거하고 표시가 될 수 있게 설정

```javascript
const words = [...new Set(sumWords)]
    .map(function(d) {
        return { text: d, size: 10 + Math.random() * 90, color: randomColor() };
    });
```

## d3-cloud 옵션 활용
```javascript
cloud()
		.size([width, height])
		.words(words)
		.padding(5)
		.rotate(() => Math.floor(Math.random() * 2) * 90)
		.fontSize(d => d.size)
		.on("end", draw)
		.start();

function draw(words) {
		const svg = d3.select("#word-cloud")
		    .append("svg")
		    .attr("width", width)
		    .attr("height", height)
		    .append("g")
		    .attr("transform", "translate(" + width / 2 + "," + height / 2 + ")");
		
		svg.selectAll("text")
		    .data(words)
		    .enter().append("text")
		    .style("font-size", function(d) { return d.size + "px"; })
		    .style("fill", function(d) { return d.color; })  // 랜덤 색상 적용
		    .attr("text-anchor", "middle")
		    .attr("transform", function(d) {
		        return "translate(" + [d.x, d.y] + ")rotate(" + d.rotate + ")";
		    })
		    .text(function(d) { return d.text; });
}
```

## 색상 랜덤 생성
```javascript
const randomColor = () => {
    const letters = '0123456789ABCDEF';
    let color = '#';
    for (let i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
    }
    return color;
};
```

# 문제
## 문제 1-1.
역시나 router로 페이지 이동 시 값이 그대로 멈춰있다. useEffect를 사용하여 페이지 전환 시 렌더링 되게 설정.

```javascript
useEffect(() => {
    const getGoals = (goalType) => data
        .filter(item => item.year === yearToNum)
        .flatMap(item => item.months
        .filter(months => months.month === monthToNum)
        .map(months => months.goals[goalType])
    ).flat();

    const getHabit = (habitType) => data
        .filter(item => item.year === yearToNum)
        .flatMap(item => item.months
        .filter(months => months.month === monthToNum)
        .flatMap(months => months.habits.map(habit => habit[habitType]))
    );

    let habitNm = getHabit('name');
    let long = getGoals('longTerm')
    let short = getGoals('shortTerm')

    const sumWords = [...habitNm.join(' ').split(' '), ...long.join(' ').split(' '), ...short.join(' ').split(' ')]

    const words = [...new Set(sumWords)]
        .map(function(d) {
            return { text: d, size: 10 + Math.random() * 90, color: randomColor() };
        });

        cloud()
            .size([width, height])
            .words(words)
            .padding(5)
            .rotate(() => Math.floor(Math.random() * 2) * 90)
            .fontSize(d => d.size)
            .on("end", draw)
            .start();

    function draw(words) {
        const svg = d3.select("#word-cloud")
            .append("svg")
            .attr("width", width)
            .attr("height", height)
            .append("g")
            .attr("transform", "translate(" + width / 2 + "," + height / 2 + ")");

        svg.selectAll("text")
            .data(words)
            .enter().append("text")
            .style("font-size", function(d) { return d.size + "px"; })
            .style("fill", function(d) { return d.color; })  // 랜덤 색상 적용
            .attr("text-anchor", "middle")
            .attr("transform", function(d) {
                return "translate(" + [d.x, d.y] + ")rotate(" + d.rotate + ")";
            })
            .text(function(d) { return d.text; });
    }
}, [yearToNum, monthToNum])
```

## 문제 1-2.
페이지 이동할 때 값이 변하긴 하는데 기존의 값이 사라지지 않고 계속 추가가 됨.<br/>
useEffect 상단에 remove 추가

```javascript
useEffect(() => {
    d3.select("#word-cloud").selectAll("svg").remove();
    
    // ...
		
})

return (
    <div className="content">
        <h1 className="title-lg">{monthToNum}월의 꿈</h1>
        <div id="word-cloud"></div>
    </div>
)
```

# 평가
글자 크기가 제일 많이 사용한 단어가 크고 제일 적게 나온 단어가 작은 형태가 아니라 그냥 랜덤식으로 구현을 했다. 나중에 갯수도 확인해서 font-size를 다르게 주던지, 
글자 크기도 구현이 가능한 라이브러리가 있는지 확인해서 사용해봐야겠다.

[참고 블로그](https://velog.io/@taeung/React%EC%97%90%EC%84%9C-d3-cloud-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)

