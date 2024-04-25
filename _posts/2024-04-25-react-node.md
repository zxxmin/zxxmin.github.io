---
title: Node.js에 대하여
date: 2024-04-25 00:00:01
categories:
- React
---

과연 Node.js는 무엇인지, 그래서 React 공부할 때 왜 필요한지 알아보자<br />
[한입 크기로 잘라먹는 리액트]

<div class="callout">
    <span>:woman_technologist:</span>
    Node.js는 단순 자바스크립트 런타임이며, 게임과 게임기에 비유하자면<br />
    자바스크립트는 게임, Node.js는 게임을 구동하는 게임기에 비유할 수 있다.<br /><br />
    런타임이라는 설명은 구체적으로 나오지 않아 정확하게 이해하고 싶어서 파고파고 들었다.
</div>
<br>

# Node.js???
<div class="callout">
    <span>:desktop_computer:</span>
    Node.js는 Chorme V8 JavaScript 엔진으로 빌드 된 JavaScript 런타임 입니다.
    - Node.js 공식 홈페이지
</div>

## 런타임???
Node.js는 **런타임** 이라고 한다. 자꾸 js 같은 언어로 이해하고 있었던 나 자신 한심해...<br />
런타임은 특정 언어로 만든 **프로그램을 실행할 수 있는 환경**을 뜻한다.<br />
정확하게 런타임을 이해하려면 JavaScript 부터 이해해야한다.

## JavaScript
간단하게 설명하자면,<br />
정적인 언어인 HTML을 조작해서 웹페이지를 동적으로 바꿔주는 기능을 한다.
<div class="callout">
    <span>:woman_technologist:</span>
    HTML은 JavaScript가 조작하는데 JavaSciprt 해석은 누가하나???
</div>

## JavaScript 해석 누가하고 있냐?
바로 **브라우저**이다. 브라우저에는 자바스크립트 해석 엔진이 있다. (브라우저의 종류에 따라 쓰는 엔진이 다름)
따라서 **기존에는 자바스크립트를 인터넷 브라우저에서만 실행** 할 수 있었다.<br />
그러나 2008년에 구글이 V8 엔진을 사용하여 크롬을 출시했고, V8엔진이 너무 뛰어나서 V8 엔진 기반의 노드 프로젝트가 시작했다. Node.js(V8) 두둥 등장.<br />
Node.js는 브라우저 내 말고도 **다른 환경에서 자바스크립트를 사용할 수 있게 해준다.**<br />
따라서 **Node.js는 JavaScript 실행 환경 (=런타임)** 이다.
<div class="callout">
    <span>:woman_technologist:</span>
    책에서 비유처럼 JavaScript는 게임, Node.js는 게임기라는 말을 완벽히 이해했다.
</div>

## Node.js로 JavaScript 실행하기
1. Node.js 홈페이지에 설치 (P.133)
2. cmd 명령 프롬프트 열기
3. `node -v` 버전 확인
4. 버전 확인 후 `node` 입력
5. `console.log( )` 작성 후 자바스크립트 코드가 작동되는지 확인
![](/assets/images/react/node1.png)
6. 종료는 `Ctrl+C` or `Ctrl+D` or `type .exit`
![](/assets/images/react/node2.png)


**파일을 만들어서 실행하는 방법. (P.140)**
1. sample.js 파일 생성
2. sample.js 내부에 `console.log(HELLO)` 작성
3. cmd 명령 프롬프트 열기
4. `node sample.js` 입력 : Node.js 에게 sample.js 를 실행하라는 명령
![](/assets/images/react/node3.png)

# Node.js 패키지
회사 프로젝트들을 보면 수업하는 곳, 과제 제출하는 곳, 독서 기록하는 곳, 독서 찾는 곳 등등 
복잡한 기능들이 필요하다. 이런 복잡한 기능들을 만들 때 하나의 js 파일에 다 때려넣기 보다는 
각각 js 파일을 만들어줬었다.

하나의 프로젝트에서 여러 JavaScript 파일을 Node.js를 이용해 실행할 때는 패키지(Package) 형태로 
구성한다. 패키지는 **Node.js에서 여러 개의 JavaScript 파일을 마치 하나의 파일처럼 실행하고 관리하는 일종의 관리 단위** 이다.

## 패키지 만들기
패키지 단위로 여러 파일을 관리하려면 최상위 폴더인 '루트 폴더'가 필요하다.<br />
패키지를 생성하려면 `npm`을 이용해야한다.<br />
`npm`은 **Node Package Manager**의 약자로 Node.js 패키지를 관리하는 도구.

**패키지 만드는 방법(P.142)**
1. `npm init` 입력<br />
Node.js 패키지를 초기화하는 명령어, Node.js 패키지를 구성하는 데 필요한 최소한의 구성 요소를 자동으로 생성하는 과정.
2. package name : `루트 폴더명` 입력
3. 루트 폴더 내 `package.json` 파일 생성<br />
패키지의 meta 정보를 저장하는 파일. 패키지 정보를 확인하여 적절한 방식으로 프로그램을 가동

# Node.js 모듈 시스템
모듈이란 독립적으로 존재하는 프로그램의 일부로 재사용이 가능하다.<br />
모듈은 마우스나 모니터로 비유할 수 있다. 마우스와 모니터는 독립적으로 존재할 수 있기 때문에
다른 컴퓨터와 연결할 수 있는 것 처럼 모듈도 **독립적으로 존재**하는 것으로,
**다른 프로그램의 부품으로 활용**할 수 있다.<br />
모듈을 사용하는 방법은 '모듈 시스템' 이라고 한다.

## ES 모듈 시스템
ES 모듈 시스템은 ECMAScript 모듈 시스템의 약자로, ESM 이라고 한다.<br />
React, Vue와 같은 최신 프론트 기술은 모두 ESM을 채택하고 있다.<br />
하지만 Node.js는 CJS 모듈 시스템을 사용하고 있어서 ESM 모듈 시스템을 사용하려면
package.json에서 설정을 변경해야한다.

{% highlight javascript linenos %}
{
    ...
    "type": "module"
}
{% endhighlight %}

<div class="callout">
    <span>:woman_technologist:</span>
    모듈의 정의처럼 독립적이고 재사용이 가능한 js 파일은 다른 파일에서 불러와 사용할 수 있다.
</div>

## 독립적이고 재사용이 가능한 JavaScript

### 개별 내보내기
변수, 함수 앞에 하나하나씩 `export` 붙여주기
{% highlight javascript linenos %}
export const PI = 3.141592;

export const function getArea (radius) {
    return PI * radius * radius;
}

export const function getCircumference (radius) {
    return 2 * PI * radius;
}
{% endhighlight %}

### 한번에 여러 값 내보내기
마지막에 `export` 한번에 묶어주기
{% highlight javascript linenos %}
const PI = 3.141592;

const function getArea (radius) {
    return PI * radius * radius;
}

const function getCircumference (radius) {
    return 2 * PI * radius;
}

export { PI, getArea, getCircumference };
{% endhighlight %}

### 개별 불러오기
`import` 안에 하나하나씩 넣어주기
{% highlight javascript linenos %}
import { PI, getArea, getCircumference } from "./circle.js"

console.log(PI, getArea(1), getCircumference(1));
{% endhighlight %}

### 전부 불러오기
1. 모듈 circle.js가 내보낸 값을 모두 불러와 변수 circle 프로퍼티로 저장
2. 점 표기법으로 특정 모듈에 접근
{% highlight javascript linenos %}
import * as circle from "./circle.js"

console.log(circle.PI, circle.getArea(1), circle.getCircumference(1));
{% endhighlight %}

### 기본 값으로 내보내기
`export default` 사용하기
{% highlight javascript linenos %}
const PI = 3.141592;

const function getArea (radius) {
    return PI * radius * radius;
}

const function getCircumference (radius) {
    return 2 * PI * radius;
}

export default {
    PI,
    getArea,
    getCircumference
};
{% endhighlight %}
{% highlight javascript linenos %}
import circle from "./circle.js"

console.log(circle.PI, circle.getArea(1), circle.getCircumference(1));
{% endhighlight %}
* 주의사항 : 기본값으로 내보내지 않은 모듈에서 값을 불러오면 오류 발생

## node_modules
패키지에 라이브러리를 설치하면 자동으로 'node_modules' 폴더 생성.
패키지에 설치된 라이브러리가 실제로 저장되는 곳. 그래서 용량이 가장 크다.

배포할 때 용량이 큰 node_modules는 공유하지 않고 `package.json`과 `package-lock.json`만 공유하면
라이브러리를 다시 설치할 수 있다.