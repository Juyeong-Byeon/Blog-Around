---
title: "custom hooks를 적용해보며 느낀점"
date: "20210910"
description: "custom hooks를 적용해보며 느낀점."
---

# Custom hooks

리액트 공식 문서에서는 커스텀 훅스의 이점을 이렇게 설명하고 있다. *"자신만의 Hook을 만들면 컴포넌트 로직을 함수로 뽑아내어 재사용할 수 있습니다."* 
이번에 react hook와 custom hook을 회사에 직접 적용해보면서 위의 장점을 정말 잘 느낄 수 있었던 것 같다.
처음엔 컴포넌트를 class 형태로 짰었는데, 이를 논의를 통해서 function으로 바꾸면서 hooks를 적용해보고, 이를 다시 묶어서 custom hooks를 적용해봤다.

이 과정에서 느낀점을 몇가지 적어보려고 한다. 

## 기본 hooks를 적용

기본 hooks를 적용해보면서는 상태를 관리하기 위해서 class의 형태를 위해 기본적으로 작성되어야 하는 보일러 플레이트 들을 사용하지 않아도 되어서 코드가 간결해진다는 느낌을 받았다. 
하지만 오히려 여러 상태를 관리하다 보니까 한 함수에 상태관리 로직과 render를 위한 함수가 모두 같이 있다는 것이 가독성을 떨어트리는 것 같다는 생각을 했다.
내가 개발한 컴포넌트는 페이지네이션을 통해 각 페이지를 가져오는 로직이있는 리스트 컴포넌트였는데, 리스트 호출을 위한 로직과 리스트를 이용해 그려주는 로직, 컴포넌트가 unmount되었을 때 api 호출을 취소해주는 로직, 그리고 다음 리스트를 요청하는 로직 등 모든 부분을 한 함수에 넣게 되니 가독성이 떨어질 수 밖에 없었던 것 같다. 

## custom hooks를 적용

custom hooks를 적용하면서 접근한 방식을 위에서 말한 상태들을 하나하나 "순수히 데이터를 위한 로직" 인지 "view를 위한 로직"인지 파악해서 분리를 해보았다.
처음에는 가장 먼저 앞선 글에서 설명한 promise 취소 유틸을 사용하기 쉽게 하는 hooks를 정의해보았다.
기존엔 리스트 형태의 컴포넌트 였기때문에 여러 콜백을 관리해줘야 했고, 이를 필드로 관리하다가 useEffect에서 cancel해줘야 하는 귀찮음이 있었다.
이 패턴을 파악해서 hooks로 만들게 되었다. 


```tsx
...
//..someCode

// 사용하는 컴포넌트에서 해주기. 
promiseList.push(makeCancelable(promise));
useEffect(()=>{
    return ()=>promiseList.forEach(promise.cancel())// 관리하기 귀찮음!
},[])

...
```
[비슷한 코드](https://rajeshnaroth.medium.com/writing-a-react-hook-to-cancel-promises-when-a-component-unmounts-526efabf251f)
```tsx
//..someCode

function useCancelable<T>(promise:Promise<T>){

  //..somecode

    useEffect(()=>{
        promiseList.forEach(promise.cancel())
    },[])

  //..내부에서 관리
    const cancelablePromise=makeCancelable<T>(promise);
    promiseList.push(cancelablePromise);

    return [cancelablePromise.promise];
}

```



그리고 페이지네이션, fetch 하는 로직도 hooks로 뽑아내서 만들어보았는데, class 형보다도, function형 컴포넌트에 기본 hooks를 사용한 것 보다 훨씬 view쪽의 코드가 깔끔해지고
view와 데이터를 위한 로직이 확실히 분리되는 것 같았다.
구체적으로는 view 컴포넌트에서는 데이터를 fetch 하고 관리하는 로직은 전혀 모르고 custom hooks에서 반환하는 list 데이터와 다음 페이지를 읽어오는 getNextPage()만 알면 됐기 때문에 훨씬 간결해졌고,
데이터를 fetch 해주고 관리해주는 hooks는 view와 관련 없이 데이터만을 신경 쓸 수 있어서 더 간결해졌던 것 같다. 그리고 hooks가 반환하는 인터페이스가 정해져 있고 잘 캡슐화 되어있기 때문에 이를 다른 view에서도 충분히 이용할 수 있다는 장점이 있는 것 같다. 
기존 class 형 컴포넌트에서는 HOC를 이용하지 않는 이상 같은 데이터이고, 같은 방식(스크롤 페이지네이션)을 가지는 다른 컴포넌트에서 로직을 사용하고 싶으면 다시 로직을 작성해줘야했는데 이런 반복작업을 할 필요가 없을 것 같다는 생각이 들었다. 


## 고민

hooks를 작성 하면서 callback처럼 특정 이벤트가 발생하고 fetch 를 하는 경우가 있었는데 이럴 경우 fetch 함수를 해당 callback에서 실행해야하나, useEffect안에서 렌더링시에 자연스럽게 처리되도록 해야 하나 고민이 있었다. 그런데, 
공식 문서를 다시 읽어보니 이름 처럼 sideEffect는 useEffect안에서 하는것이 맞는것 같다.
