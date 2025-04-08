---
title: Styling
date: 2024-09-11 00:00:03
categories:
- Next
tag:
- Next
---

# Styling
- 넥스트에서는 App 컴포넌트가 아닌 파일에서 import 문으로 css 파일을 그대로 불러오는 것을 제한.<br/>
  → 다른 페이지에 작성된 css 코드와 충돌이 일어날 수 있으므로

```javascript
import './index.css';
```

![](/assets/images/next/next-styling-1.png)

## CSS Module
- 클래스명을 자동으로 변환시켜주는 CSS Module 사용
- css 파일명을 `index.module.css` 와 같이 설정

```javascript
import style from './index.module.css';

export default function Home() {
	  return (
		    <>
		    <h1 style={{color: 'red'}}>인라인 스타일</h1>
		    <h1 className={style.h1}>CSS 모듈</h1>
		    </>
	  );
}
```