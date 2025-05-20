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

해석 : 클라이언트(사용자 브라우저) 쪽에서 오류가 발생했다

운영 중 B사용자가 pc 화면에서 로그인 클릭 시 오류 발생.

즉, 로그인이 필요한 특정 페이지에서 위같은 에러 발생.

90% 이상은 서버로 부터 클라이언트 측 렌더링에 필요한 데이터를 제대로 받아오지 못해서 생기는 문제.
에러의 근본적인 이유는 클라이언트 측에서 예외처리를 제대로 안해서 생긴 것.
해당 데이터를 사용하는 로직에서 데이터가 정상적으로 서버로부터 받아온 직후 데이터를 사용할 수 있도록 예외처리가 필요. - https://duklook.tistory.com/410


처음에는 로그인 후 로그인 token을 검사하는 것을 공통 api 안에 작성을 하였는데, api 호출 시 마다 session (로그인 값)을 찾아 여러번을 호출하고 있었고, 이부분에서 클라이언트로 잘 전달이 되지 않은 것으로 판단하고, 서버사이드에서 session에 로그인 token 값이 있는지 확인 하는것으로 수정.


session api 호출이 여러번 호출 되었던건 수정되었지만, 오류 수정되지 않음


과거 쿠키가 남아있어서 생기는 오류로 쿠키를 삭제하는 것도 방법이라고 하던데, (이 블로그를 작성한 분은 결국 예외처리로 해결)
https://velog.io/@owlsuri/Application-error-a-client-side-exception-has-occurred-see-the-browser-console-for-more-information


추측으로는 사용자가 로그인을 한적이 있고, 개발자들이 유지보수를 하고 다시 배포를 하고 사용자는 배포 후 다시 로그인을 하게 되면 과거의 캐시가 남아서 위같은 오류가 발생한다고 추측.

로그인이 필요한 특정 페이지마다 모든 곳에 예외처리를 해주는 것보다 빌드 시 쿠키를 삭제하는 방법을 고안. 즉 버전을 업데이트 해주는 것과 동일.

generateBuildId: async () => {
return `build-${Date.now()}`;
},


https://seo-tory.tistory.com/90


위 오류는 gpt도 그렇고 티빙도 그렇고 빈번하게 일어나는 오류같다.
다음 개발 시에는 조금 더 꼼꼼하게 모든 api를 호출하는 부분에서 예외처리를 해주던가, 저런 영어가 있는 화면을 띄워주는것보다 ui적으로 다른 페이지를 띄워줄수잇게 신경써야겠다


https://dung-beetle.tistory.com/189
https://allis.tistory.com/26
