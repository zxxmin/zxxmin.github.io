---
title: 다중 API 엔드포인트를 위한 Axios 인스턴스 설계
date: 2026-03-18 00:00:01
permalink: /base/axios-instance
categories:
  - 트러블슈팅
  - base-project
tag:
  - 트러블슈팅
  - base-project
---

# 개요
## 문제 상황
서비스가 Web 백엔드, AI 백엔드, Data 백엔드 등 여러 백엔드와 연동되면서 두 가지 문제가 발생했다.

- API 요청마다 `baseURL`을 직접 변경해야 하는 불편함
- 인증/인가 및 `menuId` 헤더 설정이 Web 백엔드 기준으로만 구현되어 있어, AI 백엔드 요청 시 불필요한 헤더가 함께 전송됨

## 해결 방향
백엔드 유형별로 Axios 인스턴스를 분리하고, 공용 API 함수에 `type` 파라미터를 추가하여 호출 시점에 인스턴스를 선택할 수 있도록 설계했다.

---

# Axios 인스턴스 설계
## api.ts
```javascript
import axios, { AxiosError, type AxiosInstance, Method, ResponseType } from 'axios';
import { useAuthStore } from '@/store/common/useAuthStore';

// 환경 변수 타입 정의는 vite-env.d.ts에서 관리

export type ApiType = 'default' | 'ds';

export const API_URL: string = import.meta.env.VITE_API_URL;
export const API_DS_URL: string = import.meta.env.VITE_API_DS_URL;

// todo 토큰 인증 만료 시간이 되면 logout 처리
export const api: AxiosInstance = axios.create({
    baseURL: API_URL,
    withCredentials: true,
    headers: {
        'Content-Type': 'application/json',
        Accept: 'application/json',
    },
});

export const apiDs: AxiosInstance = axios.create({
    baseURL: API_DS_URL,
    withCredentials: true,
    headers: {
        'Content-Type': 'application/json',
        Accept: 'application/json',
    },
});

const applyInterceptors = (instance: AxiosInstance) => {
    // 요청 인터셉트
    instance.interceptors.request.use(
        (config) => {
            const persist = localStorage.getItem('auth');
            if (!persist) return config;

            const { state } = JSON.parse(persist);
            const userMenus = state?.userMenus ?? [];
            if (!userMenus.length) return config;

            const currentPath = window.location.pathname;

            const matchedMenu = userMenus.find((menu: any) => currentPath.startsWith(menu.url));

            if (matchedMenu?.menuId != null) {
                config.headers['menuId'] = String(matchedMenu.menuId);
            }

            return config;
        },
        (error) => Promise.reject(error),
    );
    // 응답 인터셉트
    instance.interceptors.response.use(
        (res) => res,
        (error: AxiosError) => {
            if (error.response?.status === 401 && !error.config?.url?.includes('/login')) {
                const { logout } = useAuthStore.getState();
                logout();
                localStorage.removeItem('auth');
            }
            return Promise.reject(error);
        },
    );
};

applyInterceptors(api);

const getApi = (type: ApiType = 'default') => (type === 'ds' ? apiDs : api);

// GET용 공통 API함수
export const getData = async <T>(
    url: string,
    params?: Record<string, unknown> | null,
    type: ApiType = 'default',
): Promise<T> => {
    const instance = getApi(type);
    const { data } = await instance.get<T>(url, params ? { params } : undefined);
    return data;
};

// POST용 공통 API함수
export const postData = async <T>(
    url: string,
    params?: Record<string, unknown> | FormData,
    type: ApiType = 'default',
): Promise<T> => {
    const instance = getApi(type);

    const config =
        params instanceof FormData ? { headers: { 'Content-Type': 'multipart/form-data' } } : {};

    const { data } = await instance.post<T>(url, params, config);
    return data;
};

// POST, PUT, DELETE용 공통 API함수
export const requestData = async <T>(
    url: string,
    method?: Method,
    params?: Record<string, unknown> | FormData | unknown[],
    responseType?: ResponseType,
    type: ApiType = 'default',
) => {
    const instance = getApi(type);

    const res = await instance({
        url,
        method: method || 'post',
        data: params,
        responseType,
    });
    return res.data as T;
};
```

### 세부 내용
- `api`, `apiDs` 형태 같이 axios 따로 설정
    ```javascript
    export const API_URL: string = import.meta.env.VITE_API_URL;
    export const API_DS_URL: string = import.meta.env.VITE_API_DS_URL;
    // todo 토큰 인증 만료 시간이 되면 logout 처리
    export const api: AxiosInstance = axios.create({
        baseURL: API_URL,
        withCredentials: true,
        headers: {
            'Content-Type': 'application/json',
            Accept: 'application/json',
        },
    });
    export const apiDs: AxiosInstance = axios.create({
        baseURL: API_DS_URL,
        withCredentials: true,
        headers: {
            'Content-Type': 'application/json',
            Accept: 'application/json',
        },
    });
    ```
  
- `getApi` 함수를 설정하여 공용 API 함수의 매개변수에 따라 사용할 axios 변수 설정
    ```javascript
    const getApi = (type: ApiType = 'default') => (type === 'ds' ? apiDs : api);
    export const getData = async <T>(
        url: string,
        params?: Record<string, unknown> | null,
        type: ApiType = 'default',
    ): Promise<T> => {
        const instance = getApi(type);
        const { data } = await instance.get<T>(url, params ? { params } : undefined);
        return data;
    };
    ```
  
## react query 공통 함수 - useCommonHooks.ts
```javascript
export const useGetQuery = <T>(
    url: string,
    queryKey: unknown[],
    params?: Record<string, unknown> | null,
    options?: UseQueryOptions<T, AxiosError>,
    type: ApiType = 'default',
) => {
    return useQuery<T, AxiosError>({
        queryKey: [...queryKey, params],
        queryFn: () => getData<T>(url, params, type),
        ...options,
    });
};
```

### 세부 내용
- `getData`를 사용하는 공통 get query 내부에서 `getData`에 넘겨줄 매개변수인 type 설정

## 🔥 API 호출 Hooks (사용법 확인)
```javascript
export const useAnomalyDetectHooks = (params: Record<string, any>) => {
    return useGetQuery<AnomalyResponse>(
        API.CLIENT.ANO_COST.getAnomalyDetect,
        ['anomaly-detect'],
        params,
        undefined,
        'ds',
    );
};
```

### 세부 내용
- DS 백엔드 호출 일 경우, options -> undefined 설정,
- type -> ‘ds’ 라는 string 설정

---

# API 서버 추가
- 다른 백엔드가 추가 될 경우,<br/>
  api.ts 에서 axios 만든 후 getApi 함수에 type 설정

