---
title: 다국어 번역기 - 구글 번역 API
date: 2025-04-05 00:00:01
categories:
  - React
  - 실무
tag:
  - React
  - 실무
---

# 다국어 번역기 사용
클라이언트 측에서 6가지 언어가 번역되기를 원했다. 무료로.<br/>
그래서 선택한 번역 API가 구글 번역 API, translate 이다. (현재는 중단 되었음)<br/>

## 1) 구글 api 사용하는 방법
```javascript
useEffect(() => {
    if (isInitialRender.current) {
        const addGoogleTranslateScript = document.createElement('script');
        addGoogleTranslateScript.src =
            'https://translate.google.com/translate_a/element.js?cb=googleTranslateElementInit';
        document.body.appendChild(addGoogleTranslateScript);

        window.googleTranslateElementInit = () => {
            new window.google.translate.TranslateElement(
                { pageLanguage: 'ko', autoDisplay: true },
                'google_translate_element',
            );
        };

        return () => {
            document.body.removeChild(addGoogleTranslateScript);
        };
    }
    isInitialRender.current = false;
}, []);
```

## 2) 원하는 언어만 사용하는 방법
```javascript
new google.translate.TranslateElement({pageLanguage: 'ko' , includedLanguages : 'ko,en,jp'}, 'google_translate_element');
```
- includedLanguages 를 설정해주면 된다.

## 3) 번역을 원하지 않는 곳
```javascript
<div className="notranslate"></div>
```
- notranslate 라는 class를 주면 된다.