---
title: "isMounted is an Antipattern"
date: "20210909"
description: "마운트가 해제 되었을때 비동기 setState를 취소하기 위한 방법을알아보던 중 읽게된 문서를 정리해보고, 비동기 작업을 취소하는 유틸을 정의하고 사용해보자."
---

팝업이나, 특정 페이지를 열 때 api fetch를 하는 경우가 많다. 이럴 경우 보통 class 형 컴포넌트의 경우 fetch의 등록을 componentDidMounted에서 (함수형의 경우 useEffect) 하게된다. 
이럴 경우 흔하게 생기는 문제는 컴포넌트가 unmounted된 후 fetch가 완료 될경우 fetch callback 내부의 this.setState가 실행되면서 memory leak이 발생할 수 있다는 것이다. 
이런 문제를 해결하기 위해 처음 생각한 방법은 아래와 같은 방법이었다.



** 아래코드는 좋지 못한 코드입니다. code 1**
```tsx
//좋지 않은 코드
class SomeComponent extends React.Component{
    private isMounted:boolean=false;

    constructor(){
        super()
    }

    public componentDidMount(){
        this.isMounted=true;
        this.fetchSomeApi();
    }

    public componentWillUnMount(){
        this.isMounted=false;
    }

    private async fetchSomeApi(){
        const res=await APi.fetch();
        if(this.isMounted)
        this.setState({...res})
    }
}
```

잠시 찾아보니 react에서 원래 this.isMounted()라는 메서드를 지원했었는데, 이를 deprecating 시켰고 이 차선으로 제시한게 위 방법이었다. 
하지만 이 패턴마저도 좋지 못하다는 말들이 많았다. 때문에 그 이유를 알아보기 위해 조사를 하던 중 [isMounted is an Antipattern](https://ko.reactjs.org/blog/2015/12/16/ismounted-antipattern.html)글을 발견해서 읽게 되었고 이 글에서 해결책도 제시해주고 있어(역시 공식 블로그) 이에 대해 정리해보려고 한다. 


## isMounted 가 안좋은 이유

1. this.isMounted()는 보통 위에서 설명한 것 과 같이 unMounted된 후 this.setState()를 막기 위해 사용되었었는데, 이는 cleanup을 실패했다는 말이다. 
    이때 this.setState()전에 아래와 같이 this.isMounted()를 체크하면 에러가 사라지지만 필요할때도 에러를 확인 하지 못하게 된다. 

사실 이건 위의 (code 1)에 제시한 코드도 마찬가지다

2. 그리고 callback 내부에서 isMounted 체크를 하면, 이는 해당 callback이 언제 실행될지 확실하지가 않기 때문에 코드를 예측하기 힘들게 만든다고 생각한다.

```tsx
if (this.isMounted()) { // This is bad.
  this.setState({...});
}
```

# 최선
최선은 this.setState를 컴포넌트가 unmounted 된 후에 실행될 일이 없도록 배치하는 것이다. 하지만 데이터를 받아야하는 경우등에서는 Callback을 이용하기 때문에 이를 제어하기가 힘들다. 그래서 이를 componentWillUnmount에서 잘 cleanup을 진행해줘야 한다. 
아래와 같이 리스닝을 끊어줘야 한다. 
```tsx
class MyComponent extends React.Component {
  componentDidMount() {
    mydatastore.subscribe(this);
  }
  render() {
    ...
  }
  componentWillUnmount() {
    mydatastore.unsubscribe(this);
  }
}
```
하지만 Promise를 사용하는 경우는? 
이를 취소시키면 된다. 하지만 매번 이 경우 사실 promise 내부에서 reject를 해주면 되지만 이 경우 위의 isMounted를 사용하는 것과 같은 격이니 제어의 흐름을 외부로 빼야 한다. 
이를 쉽게 하기 위한게 아래의 유틸함수이다. 

```tsx
function makeCancelable<T>(promise:Promise<T>){
  let _hasCanceled:boolean = false;

  const wrappedPromise:Promise<T> = new Promise((resolve, reject) => {
    promise.then(
      val => _hasCanceled ? reject({isCanceled: true}) : resolve(val), // on resolve 함수 resolve가 된 이후에도 한번 더 체크를 해서 최종 resolve callback을 실행시킬지 체크한다. 
      error => _hasCanceled ? reject({isCanceled: true}) : reject(error)
    );
  });

  return {
    promise: wrappedPromise,
    cancel() {
      _hasCanceled = true;
    },
  };
};
```

## 결론

isMounted를 사용하지 않아야 하는 이유는 cleanUp을 명시적으로 componentWillUnmount에서 진행해줘 코드의 호출 시점을 예상하기 쉽게 하고, cleanUp 로직을 몰아두기 위해서인 것 같다. 
위 코드를 프로젝트에 적용해봐야겠다.






[isMounted is an Antipattern](https://ko.reactjs.org/blog/2015/12/16/ismounted-antipattern.html)