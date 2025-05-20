---
title: Application error a client-side exception has occurred (see the browser console for more information) 오류
date: 2025-05-20 00:00:01
categories:
  - 트러블슈팅
  - 꿈it(잇)다
tag:
  - 트러블슈팅
  - 꿈it(잇)다
---

# 개요
- 운영 중 사용자가 로그인 클릭 시 **Application error a client-side exception has occurred (see the browser console for more information)** 라는 오류 발생

# 분석
- 해석 : 클라이언트(사용자 브라우저) 쪽에서 오류가 발생했다.

현재 오류가 발생하는 상황은 로그인이 필요한 특정 페이지에서 위 같은 에러가 발생.<br/>
90% 이상은 서버로 부터 클라이언트 측 렌더링에 **필요한 데이터를 제대로 받아오지 못해서 생기는 문제.**<br/>
에러의 근본적인 이유는 **클라이언트 측에서 예외처리를 제대로 하지 않아서** 생긴 것.<br/>
해당 데이터를 사용하는 로직에서 데이터가 정상적으로 서버로부터 받아온 직후 데이터를 사용할 수 있도록 예외처리 필요.

[참고 블로그](https://duklook.tistory.com/410)


# 해결 방법 1
처음에는 로그인 후 로그인 token을 검사하는 것을 **공통 API 안에 작성**을 하였는데, **API 호출 시 마다 session (로그인 확인)을 여러번 호출**하고 있었음.<br/>
이 부분에서 클라이언트로 잘 전달이 되지 않은 것으로 판단하고, 서버사이드에서 session에 로그인 token 값이 있는지 확인하는 것으로 수정.

# 결과
session API 호출이 여러번 호출 되었던 건은 수정되었지만, 위의 오류는 수정되지 않음

# 해결 방법 2
과거 쿠키가 남아 있어서 생기는 오류로 쿠키를 삭제하는 것도 방법이라는 글을 읽음. (이 블로그를 작성한 분은 결국 예외처리로 해결했지만...)<br/>
[참고 블로그](!https://velog.io/@owlsuri/Application-error-a-client-side-exception-has-occurred-see-the-browser-console-for-more-information)

- 에러 상황 추측
1) 사용자가 전에 로그인을 한적이 있음.
2) 개발자들이 유지보수를 하고 배포를 함. 
3) 사용자는 배포된 후 다시 로그인을 시도를 함.
4) 과거의 캐시가 남아서 위같은 오류가 발생함.

으로 추측을 하였고, 로그인이 필요한 특정 페이지마다 모든 곳에 예외처리를 해주는 것이 방법이지만,<br/>
사용자를 위해 당장 해결할 수 있는 방법이 **빌드 시 쿠키를 삭제하는 방법**이 좋다고 생각. 즉, 버전을 업데이트 해주는 것과 동일한 방법을 찾음.

next.config.mjs 파일에 config 안에 아래와 같이 작성.
```javascript
generateBuildId: async () => {
    return `build-${Date.now()}`;
}
```
[참고 블로그](https://seo-tory.tistory.com/90)

1차적 해결 후 로그인과 관련된 페이지에 API 호출 후 data가 없을 경우에 대한 예외처리를 확인해서 처리 중.

# 결과
- 일시적인 조치 후 해당 계정에서 위와 같은 오류는 해결 되었다.

Application error a client-side exception 오류는 gpt도 그렇고 티빙도 그렇고 빈번하게 일어나는 오류같다.<br/>
로그인을 위한 NextUs 라이브러리 사용 시 발생하는 오류라는 이야기도 있던데, 꼭 NextUs 라이브러리 때문인지는 확인이 필요할 것 같다.

다음 개발 시에는 조금 더 꼼꼼하게 모든 API 호출하는 부분에서 예외처리를 해주던가,
저런 영어가 있는 화면을 그대로 띄워주는 것 보다 UI 적으로 꾸며진 페이지를 띄워줄 수 있게 신경을 더 써야겠다고 생각했다.<br/>
좋은 오류였다.

- [gpt 오류 참고 블로그](https://dung-beetle.tistory.com/189)
- [티빙 오류 참고 블로그](https://allis.tistory.com/26)
