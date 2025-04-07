---
title: Life Cycle
date: 2024-04-26 00:00:01
categories:
- React
tags:
- React
---

라이프사이클(Life Cycle) :) 사람의 인생처럼 리액트 컴포넌트도 태어나고 사라지는 생애주기가 있다.<br/>
[한입 크기로 잘라먹는 리액트]

# 라이프 사이클
컴포넌트의 라이프사이클은 **마운트(Mount, 탄생)**, **업데이트(Update, 갱신)**, **언마운트(Unmount, 사망)** 으로 구분한다.

* 마운트(Mount) : 컴포넌트를 페이지에 처음 렌더링 할 때
* 업데이트(Update) : State나 Props의 값이 바뀌거나, 부모 컴포넌트가 리렌더해 자신도 리렌더 될 때
* 언마운트(Unmount) : 더이상 페이지에 컴포넌트를 렌더링하지 않을 때

# useEffect
함수 useEffect는 어떤 값이 변경될 때마다 특정 코드를 실행하는 리액트 훅.<br/>
함수 useEffect를 사용하면 라이프 사이클 제어(Lifecycle Control)를 쉽게 할 수 있다.

{% highlight javascript linenos %}
import { useEffect } from "react"

useEffect(callback, [deps])
{% endhighlight %}

첫 번째 인수 : 콜백함수(callback), 두 번째 인수 : 의존성 배열(Dependency Array)<br/>
두 번째 인수로 전달된 **의존성 배열의 값이 바뀌면 콜백함수가 실행**.

## 업데이트(Update)
두 번째 요소인 의존성 배열에 아무것도 전달하지 않으면, useEffect는 컴포넌트를 **렌더링할 때 마다** 콜백 함수를 실행.

{% highlight javascript linenos %}
import { useEffect } from "react"

useEffect(() => {
    console.log('컴포넌트 업데이트')
})
{% endhighlight %}

### 마운트 시점 제외
의존성 배열을 인수로 전달하지 않으면 마운트, 업데이트 시점 모두 콜백 함수 호출.

{% highlight javascript linenos %}
import { useEffect, useRef } from "react"

const didMountRef = useRef(false);

useEffect(() => {
    if(!didMountRef.current) {
        didMountRef.current = true;
        return;
    } else {
        console.log('컴포넌트 업데이트')
    }
})
{% endhighlight %}

## 마운트(Mount)
"컴포넌트의 마운트를 제어한다"<br/>
의존성 배열에 빈 배열을 전달하면 컴포넌트의 마운트 시점에만 콜백 함수를 실행.

{% highlight javascript linenos %}
import { useEffect } from "react"

useEffect(() => {
    console.log('컴포넌트 업데이트')
}, [])
{% endhighlight %}

## 언마운트(Unmount)
언마운트는 컴포넌트가 페이지에서 제거될 때<br/>
useEffect의 콜백함수가 반환하는 함수를 **클린업 함수** 라고 한다.
컴포넌트를 렌더링할 때마다 새 인터벌을 생성하고 기존 인터벌은 삭제<br/>
의존성 배열에 빈 배열을 전달하고 콜백 함수가 함수를 반환하면 언마운트 시점

{% highlight javascript linenos %}
import { useEffect } from "react"

useEffect(() => {
    return () => {
        console.log('컴포넌트 언마운트');
    }
}, [])
{% endhighlight %}