---
title: Local Storage 암호화, 복호화
date: 2026-06-02 00:00:01
permalink: /base/localstorage
categories:
  - 트러블슈팅
  - base-project
tag:
  - 트러블슈팅
  - base-project
---

# 개요
로그인 후 응답으로 전달받은 사용자 이름, 이메일 등의 정보를 여러 화면에서 재사용하기 위해 Store와 Local Storage에 저장하여 활용하고 있음

하지만 Local Storage에 저장된 데이터는 브라우저 개발자 도구를 통해 누구나 확인할 수 있기 때문에 개인정보가 노출될 위험이 존재. 
이에 사용자 정보를 보다 안전하게 보호하기 위해 Local Storage에 저장하기 전 암호화를 적용하고, 필요한 시점에만 복호화하여 사용하는 방식을 도입

이를 통해 사용자 편의성을 유지하면서도 개인정보 노출 위험을 최소화할 수 있으며, 상태 관리 구조는 그대로 유지하면서 저장소에 대한 기본적인 보안 수준을 강화할 수 있음.

---

# React에서 Local Storage 암호화를 위한 라이브러리 비교
React 환경에서 Local Storage 암호화를 구현하는 방법은 크게 다음 세 가지로 나눌 수 있음.

| 항목 | ✅ Web Crypto API | CryptoJS | react-secure-storage |
|----| --- | --- |----------------------|
| 종류 | 브라우저 내장 표준 API | 암호화 라이브러리 | Local Storage 래퍼 라이브러리 |
| 설치 필요 | ❌ | ✅ | ✅ |
| 번들 크기 영향 | ❌ | ✅ | ✅ |
| 성능 | 가장 우수 | 보통 | 내부 구현 의존 |
| 보안성 | 높음 (브라우저 네이티브 구현) | 보통 | 내부 구현 의존 |
| 유지보수 | 브라우저 벤더가 관리 | 라이브러리 의존 | 라이브러리 의존 |
| 표준성 | W3C 표준 | 비표준 라이브러리 | 비표준 라이브러리 |
| React 의존성 | 없음 | 없음 | React 전용 |
| 커스터마이징 | 높음 | 높음 | 낮음 |
| 장기 안정성 | 매우 높음 | 높음 | 상대적으로 낮음 |
| 학습 난이도 | 높음 | 낮음 | 매우 낮음 |

---

# 라이브러리들의 특징
## 1. Web Crypto API
브라우저가 제공하는 공식 암호화 API
```javascript
crypto.subtle.encrypt()
crypto.subtle.decrypt()
```

### 장점
- 별도 패키지 설치 불필요
- 브라우저 네이티브 구현으로 성능 우수
- 국제 표준 기반
- 최신 보안 알고리즘 지원
- 번들 크기 증가 없음
- 장기 유지보수 걱정 없음

### 단점
- 사용법이 상대적으로 복잡함
- Promise 기반 비동기 처리 필요
- 초기 구현 비용 존재

## 2. CryptoJS
가장 널리 알려진 JavaScript 암호화 라이브러리.
```javascript
CryptoJS.AES.encrypt()
CryptoJS.AES.decrypt()
```

### 장점
- 사용법이 매우 간단
- 레퍼런스가 많음
- 빠른 적용 가능
- React, Vue, Angular 등 다양한 프레임워크에서 사용 가능

### 단점
- 외부 라이브러리 의존
- 번들 크기 증가
- 브라우저 내장 기능 대비 성능 불리
- 장기적으로는 Web Crypto API로 이동하는 추세

## 3. react-secure-storage
```javascript
secureLocalStorage.setItem()
secureLocalStorage.getItem()
```

### 장점
- 가장 사용이 쉬움
- 코드량 최소화 가능
- 빠른 적용 가능

### 단점
- 내부 구현을 신뢰해야 함
- 암호화 방식을 직접 통제하기 어려움
- React에 종속적
- 프로젝트 규모가 커질수록 유연성 부족

---

# ✅ Web Crypto API
Web Crypto API를 선택한 이유는 아래와 같음.

## 1. 브라우저 표준 기술
Web Crypto API는 브라우저가 공식적으로 제공하는 암호화 기능.
```javascript
crypto.subtle
```
Chrome, Edge, Safari, Firefox 등 주요 브라우저가 동일한 표준을 지원.

### 장점
- 특정 라이브러리에 종속되지 않음
- 장기적으로 안정적
- 기술 부채 감소
- 표준 기반 아키텍처 구성 가능

## 2. 추가 의존성 제거
CryptoJS를 사용하려면 별도 패키지 설치가 필요.
```terminaloutput
npm install crypto-js
```

반면 Web Crypto API는 브라우저에 내장되어 있음.
```javascript
window.crypto
```

### 얻는 효과
- 패키지 관리 불필요
- 보안 취약점 관리 감소
- 번들 크기 감소
- 의존성 최소화

## 3. 우수한 성능
Web Crypto API는 브라우저의 네이티브 코드 레벨에서 동작.
반면 CryptoJS는 JavaScript로 구현.

따라서
- 암호화
- 복호화
- 해시 계산

과 같은 작업에서 일반적으로 Web Crypto API가 더 우수한 성능을 제공

## 4. 최신 웹 보안 트렌드
최근 신규 프로젝트에서는 다음과 같은 흐름이 나타나고 있음.
```
CryptoJS → Web Crypto API
```

특히
- 대규모 서비스
- 기업 플랫폼
- 금융 서비스
- 개인정보 처리 시스템

에서는 Web Crypto API 채택 비중이 지속적으로 증가.

## 5. 프레임워크 독립성
현재는 React를 사용하고 있지만 향후
- Vue
- Angular
- Next.js
- Electron

등으로 확장될 가능성 존재.

Web Crypto API는 브라우저 표준 API이므로 특정 프레임워크에 종속되지 않으며 재사용성이 높음.

---

# 적용 방향
현재 사용자 이름, 이메일, 권한 정보 등의 데이터를 Local Storage에 저장하고 있으며, 이를 안전하게 보호하기 위해 다음과 같은 구조를 적용.

```
Zustand State (평문)
        │
        ▼
Persist Middleware
        │
        ▼
Encrypted Storage
        │
        ▼
AES-GCM 암호화
        │
        ▼
localStorage (암호문)
```

컴포넌트에서는 기존과 동일하게 Zustand Store를 사용하고, Persist Middleware가 데이터를 저장하거나 복원할 때 자동으로 암호화/복호화를 수행하도록 구성.

---

# React + Zustand Persist + Web Crypto API 적용
## 암호화용 Secret Key 생성
AES-GCM 암호화 키는 환경변수에 저장된 Secret 값을 기반으로 생성.

프로젝트 최초 설정 시 OpenSSL을 이용해 랜덤 문자열을 생성.
```
openssl rand -base64 32
```

**실행 결과 예시**
```
jK5z3Y7cQ8b0R6xG4dYkA9lW1uTnPqM8VbH2sDfE5rQ=
```

생성된 값을 **.env 파일에 저장**
```
VITE_STORAGE_SECRET=jK5z3Y7cQ8b0R6xG4dYkA9lW1uTnPqM8VbH2sDfE5rQ=
```
이 값은 PBKDF2를 통해 AES-GCM 256bit CryptoKey로 변환되어 암호화 및 복호화에 사용.

> Secret 값은 충분히 길고 예측 불가능해야 하며 Git 저장소에 커밋되지 않도록 관리.<br/>
또한 Secret 값이 변경되면 기존에 암호화된 Local Storage 데이터는 복호화할 수 없으므로 신중하게 관리

---

## 1. 키 도출 (앱 최초 실행 시 1회)
```
VITE_STORAGE_SECRET (Passphrase)
            │
            ▼
PBKDF2 (100,000회 반복)
            │
            ▼
AES-GCM 256bit CryptoKey
            │
            ▼
메모리에 캐싱 (derivedKeyPromise)
```

### 동작 과정
1. 환경변수에 저장된 Passphrase 사용
2. PBKDF2 반복 해싱 수행
3. AES-GCM 256bit CryptoKey 생성
4. 생성된 키를 메모리에 캐싱

PBKDF2 연산은 비용이 크기 때문에 애플리케이션 실행 시 한 번만 수행하고 이후에는 재사용한다.

## 2. 로그인 후 Local Storage 저장
```
로그인 성공
    │
    ▼
setUser(name, email)
setUserRole(role)
    │
    ▼
Zustand 상태 업데이트
    │
    ▼
Persist Middleware
    │
    ▼
storage.setItem()
```

### 암호화 과정
```
JSON.stringify()
        │
        ▼
encryptText()
        │
        ├─ 랜덤 IV 생성 (12 bytes)
        ├─ AES-GCM 암호화
        └─ [IV + CipherText]
        │
        ▼
Base64 인코딩
        │
        ▼
localStorage 저장
```

브라우저에는 아래와 같은 암호문만 저장.

```
user-store =
"K3mZ9xQJ0u...암호문..."
```

## 3. 새로고침 시 상태 복원
Persist Middleware는 애플리케이션 시작 시 자동으로 저장된 데이터를 복원.

```
앱 시작
    │
    ▼
storage.getItem()
    │
    ▼
localStorage.getItem("user-store")
    │
    ▼
decryptText()
```

### 복호화 과정
```
Base64 디코딩
        │
        ▼
IV 추출 (앞 12 bytes)
        │
        ▼
AES-GCM 복호화
        │
        ▼
TextDecoder
        │
        ▼
JSON 문자열
        │
        ▼
JSON.parse()
```

복호화가 완료되면 Zustand Store에 다시 주입.

```json
{
    "name": "홍길동",
    "email": "user@example.com",
    "userRole": "ADMIN"
}
```

컴포넌트에서는 기존과 동일하게 사용 가능.
```javascript
const email = useUserStore((state) => state.email);
```
암호화 여부를 컴포넌트가 알 필요가 없음.

## 4. 로그아웃
```
resetUser()
    │
    ├─ Zustand 상태 초기화
    │
    └─ Persist Middleware
            │
            ▼
     storage.removeItem()
            │
            ▼
localStorage.removeItem("user-store")
```
로그아웃 시에는 메모리와 브라우저 저장소의 데이터가 모두 제거.

---

# 핵심 포인트
| 구간 | 상태 |
|----| --- |
| Zustand Store | 평문 |
| 앱 메모리 | 평문 |
| Local Storage | AES-GCM 암호문(Base64) |
| 암호화/복호화 | Persist Middleware가 자동 처리 |
| 컴포넌트 코드 | 변경 없음 |

---

# 결과
이번 적용을 통해 Zustand Persist를 유지하면서도 Local Storage에 저장되는 사용자 정보를 암호화하여 관리.

```
Zustand State
      │
      ▼
Persist Middleware
      │
      ▼
AES-GCM Encryption
      │
      ▼
localStorage
```

결과적으로 기존 상태 관리 코드를 거의 변경하지 않으면서도 브라우저 저장소에 대한 기본적인 보안 수준을 확보.

다만 클라이언트 측 암호화는 저장 데이터 노출을 어렵게 만드는 보안 강화 수단일 뿐 완전한 보안 대책은 아님. 
Secret 값은 최종적으로 브라우저 환경에서 사용되므로 역공학을 통해 노출될 가능성이 존재.

따라서 비밀번호, Access Token 원문, 금융 정보와 같은 고도의 보안이 필요한 데이터는 Local Storage에 저장하지 않는 것이 바람직하며, 민감 정보는 서버 측에서 관리 필요.