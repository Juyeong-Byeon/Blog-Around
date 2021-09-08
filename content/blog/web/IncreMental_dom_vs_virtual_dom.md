---
title: "Incremental DOM vs virtual DOM"
date: "20210908"
description: "Incremental DOM vs virtual DOM에 대한 글을 읽으며 생각한 점을 정리해보자."
---

## Virtual DOM

virtual DOM 은 DOM을 메모리상에 JS Object로 모델링 해두고 이를 이용해 변화가 생겼을 때 재조정 과정을 거치고 실제 DOM과 재조정 하는 과정을 거친다. 

    1. UI 가 변경 되면 전체 UI를 Virtual DOM으로 렌더링 한다. 
    2. 현재 Virtual DOM과 이전 Virtual DOM을 비교해 차이를 계산한다. 
    3. 변경된 부분을 실제 DOM에 반영한다. 

[재조정 Reconciliation](https://ko.reactjs.org/docs/reconciliation.html)

## Incremental DOM

Incremental DOM은 메모리 상에 실제 DOM에 대한 가상 표현이 없고 상태 변화가 있을 때 메모리상에 새로 변화된 DOM을 그리고 이를 실제 DOM과 비교해서 변경점을 찾아낸다. 

## 공통점

둘 다 변경점을 찾아 내기 위해 새롭게 생긴 렌더 결과와 이전의 모습을 비교한다는 공통점이 있는 것 같다. 이 작업의 목적은 변경점을 찾아 최소한의 실제 DOM업데이트를 해서 [Reflow](https://developers.google.com/speed/docs/insights/browser-reflow?hl=ko)를 최소화 시켜 [Critical rendering Path](https://developer.mozilla.org/ko/docs/Web/Performance/Critical_rendering_path)를 다시 실행 하지 않도록 하는 것이다.
즉 이 두 방식 모두 변경점을 최소화해 비용이 큰 화면에 새로운 요소를 그리는 작업을 최적화 하는 것이 공통 점인 것 같다. 

## 차이점

### Incremental DOM

* Incremental DOM은 tree shaking이 가능하다고 한다.
컴파일 타임에 각 컴포넌트 들의 관계를 알아내고 불필요한 것들을 떨어 버릴 수 있어 메모리를 최적화 할 수 있다. 

* Incremental DOM은 앞서 말했듯이 이전 상태의 DOM을 메모리에 가지고 있지 않고 실제 DOM과 새로운 DOM을 비교한다. 때문에 메모리 상의 이점이 있다. 

* 하지만 Virtual DOM 과 다르게 실제 DOM에 접근하기 때문에 시간적 비용이 더 든다. 

###  결론
Virtual DOM은 js object 기반으로 모델링된 DOM 전체를 상태변화가 있을때 마다 생성해서 비교하기 때문에 빠르지만 메모리를 많이 사용하고 Incremental DOM은 실제 DOM에 접근해 Virtual DOM은 보다는 느리지만 메모리를 효율적으로 사용한다.





