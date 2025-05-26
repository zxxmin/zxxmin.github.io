---
title: Github Actions 배포 (삽질의 끝판왕)
date: 2024-09-08 00:00:01
permalink: /toy/bose/gitactions
categories:
  - Toy project
  - BOSE
tag:
  - Toy project
  - BOSE
---

# Github Pages 설정
레파지토리 → Settings → Pages → Branch: 처음엔 main으로 → Save
![](/assets/images/toy/bose_deployment_1.png)

# 리액트 페이지 배포
## gh-pages 패키지
Github Pages로 배포하기 위해서는 Github Pages에서 지원하는 패키지인 gh-pages 설치
```bash
npm install gh-pages
```

## package.json 수정
“script” 파일에 내용 추가
```bash
"script": {
    // ...
    
    "predeploy": "npm run build",
    "deploy": "gh-pages -d build"
},
```

아래 쪽에 “homepage” 항목 추가하고 호스팅 될 주소 설정
```bash
"homepage": "https://zxxmin.github.io/sidepj_bose"
```

## 빌드와 배포
배포 전 리액트 프로젝트 build 작업을 거쳐야함.<br/>
수정한 파일들을 add 와 commit 으로 변경내용 저장 후 git push 로 업로드.

![](/assets/images/toy/bose_deployment_2.png)

```bash
npm run build
npm run deploy
```

## gh-pages로 page 경로 변경
레파지토리 → Settings → Pages → Branch: gh-pages

# 문제
## 문제 1.
모듈을 찾을 수 없다는 오류 발생…

![](/assets/images/toy/bose_deployment_3.png)

package.json과 node_modules 삭제하고 다시 npm i 하고 진행.

## 문제 2.
파일을 찾을 수 없다는 오류. 일단 전과 다른 오류면 해결되고 있다.

```bash
Error: ENOENT: no such file or directory, stat 'D:\dev\sidepj_bose\build' 
```

혹시 다시 install 하면서 문제가 생긴건가 생각하고 node_modules를 삭제하고 다시 install → 안됨.<br/>
왜 파일이 없다고 나올까라는 생각으로 접근해보니, Vercel로 배포한 경험이 있어서 build 폴더가 아닌 dist 폴더가 생성되어있었음.

deploy를 gh-page -d dist 로 변경

```bash
"script": {
    // ...
    
    "predeploy": "npm run build",
    "deploy": "gh-pages -d dist"
},
```

git push → npm run build → npm run deploy 까지 하고 Actions 탭에서 Action이 다 되기를 기다리면 된다.
![](/assets/images/toy/bose_deployment_4.png)

## 문제 3.
url로 사이트 로드했는데 빈화면이 나오고 콘솔에는 아래와 같은 오류 발생
```bash
GET https://zxxmin.github.io/src/main.jsx net::ERR_ABORTED 404 (Not Found)
```

오류나는 경로를 보면 나의 레파지토리 이름이 빠져있는 것을 발견.<br/>
설정하는 방법을 찾아봄.<br/>
vite.config.js 파일에 export default 설정을 싹 지우고 아래와 같이 변경했음.

```js
export default {
    base: '/your-repo-name/',
    build: {
        outDir: 'dist',
    },
}
```

하지만 계속 같은 오류가 발생.

## 문제 4.
이 문제는 방법을 몰라서 이것저것 하면서 잡다한 오류들이 많이 났다.<br/>
뭔가 다 안되니까 gh-page 브랜치로 checkout 후 다 삭제하고 다시 브랜치를 설치하고 싶은 마음에 
gh-page 브랜치로 checkout하고 손댔더니 더 이상해지고 돌이킬 수 없어질 것 같아서 main으로 다시 checkout.

이때, 포기할까 생각을 100번 넘게 했다.<br/>
하지만 무조건 해결해야 잠을 잘 것 같았고, 진정하고 차근차근 해보았음.

gh-page 를 재설치하고 처음부터 다시 해보니까 전의 오류는 넘어갔고 다음 오류가 뜸.<br/>
오케이. 한 단계씩 해결되고 있다는 뜻.

## 문제 5.
이번에는 리액트를 찾을 수 없다고 한다.

```bash
index-C5jVIIo5.js:59 Uncaught ReferenceError: React is not defined
at index-C5jVIIo5.js:59:116682
```

React in not defined 오류는 React가 현재 스코프에서 정의되지 않아서 발생한다고 함. 
일반적으로  React 가 제대로 import 되지 않거나 빌드 과정에서 문제가 생긴 경우에 발생한다는데,

문제 3. 에서 vite.config.js 설정하면서
```js
export default defineConfig({
    plugins: [ react() ],
});
```

이렇게 되어있던 부분도 다 지우고 설정해줬던 기억이 있다… 저기 설정하면서 react 플러그인을 삭제해버린 것.<br/>
다시 추가해줬음.

```js
export default defineConfig({
    base: '/sidepj_bose/',
    build: {
        outDir: 'dist',
    },
    plugins: [ react() ], // React 플러그인 추가
});
```

혹시 몰라서 

```bash
npm install @vitejs/plugin-react react react-dom
```
React 관련 플러그인과 패키지 다시 설치하고

```bash
npm run build
npm run deploy
```

## 문제 6.
이제 오류없이 빈화면이 나오기 시작.<br/>
오히려 오류가 안나오고 빈화면 나오니까 더 미쳐 버릴 것 같음.

다시 브랜치를 삭제하고 다시 설치하려고 했으나, 실패… 그래서 다시 검색을 더 해보기 시작했음.<br/>
마침 빈화면 나온 사람 발견!!! 마치 구세주 같았다.<br/>
`<BrowserRouter basename={process.env.PUBLIC_URL}>`<br/>
이렇게 설정하라는데 .env 파일도 못찾겠고 그냥 때려 박았다.

```javascript
<BrowserRouter basename="/sidepj_bose">
    <App />
</BrowserRouter>
```

드디어 화면이 나옴!!!

## 문제 7.
하지만 이미지가 안나옴.
![](/assets/images/toy/bose_deployment_5.png)

딱 보니까 public 파일에 있던 이미지만 안나오고 있음. 콘솔 오류 보면 경로 문제인것같아서 img도 경로를 일단 강제로 때려 넣음.<br/>
이렇게 강제로 설정해주면 안좋다… 나중에 경로가 바뀌면 다 찾아서 일일히 바꿔줘야하니까.

.env 파일 만드는거 어렵지 않더만… 그냥 .env 파일 하나 만들고 설정해주면 되더만…
```env
PUBLIC_URL=/sidepj_bose
```
```env
{process.env.PUBLIC_URL}
```

무튼,, 드디어 화면이 나왔다!!!! 배포 해냈다!<br/>
아래는 배포 테스트의 결말…!
![](/assets/images/toy/bose_deployment_6.png)

# 평가
쉽지않았다. 제일 쉽지 않았던 것 같다. 하지만 포기할 수 없었다… 포기해서 찝찝하게 잠을 못자나 늦게까지 해서 잠을 못자나 똑같을 것 같아서 무조건 끝을 봤다.<br/>
그래도 이제 해결할 수 있다는 것을 알게 되었고, Github Actions 도 사용하는 방법을 알 수 있어서 뿌듯했다.<br/>
역시 힘든 삽질 후에 먹는 밥이 꿀맛

[참고 블로그 - React GitHub Page에 배포하기](https://velog.io/@bami/React-GitHub-Pages에-배포하기)<br/>
[참고 블로그 - GitHub Pages 배포하기 with React](https://velog.io/@leehy0782/GitHub-Pages-배포하기-with-React)<br/>
[참고 블로그 - React Github page 배포 내가 해냄](https://i-did-it.tistory.com/75)