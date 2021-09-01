---
title: "React cheat sheet"
date: "20210824"
description: "React document 공식문서를 읽으며 기억하고 싶은 내용들을 정리."
---

# React cheat sheet

## Fragments

key가 있는 fragment를 사용하고 싶을때는 축약형을 사용할 수 없다. 
대신 명시적으로 fragment를 선언해줘야 한다. 

```tsx
//if i want to use key on fragment
function FooComponent({}:Props){
    return (
        <React.Fragment key={key}>
            <li>mellon</li>
            <li>mellon</li>
        </React.Fragment>
    )
}

```

## JSX


jsx 는 babel-loader에서 제공하는 문법적 설탕일뿐!
```tsx

function FooComponent({}:Props){
    return (
       <MyButton color="blue" shadowSize={2}>
            Click Me
        </MyButton>
    )
}

//same as 
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)
```
React 를 import 하는 이유?

"JSX는 React.createElement를 호출하는 코드로 컴파일 되기 때문에 React 라이브러리 역시 JSX 코드와 같은 스코프 내에 존재해야만 합니다."
```tsx
import React from 'react';// 필요! (혹은 전역 script일경우 불필요.)

//이 코드만 작성해도
function FooComponent({}:Props){
    return (
       <MyButton color="blue" shadowSize={2}>
            Click Me
        </MyButton>
    )
}

//여기서 React를 참조하기 때문에 
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)

```

### jsx도 . 표기법 사용이 가능하다?!?


이전에 react-native를 해보면서 react-navigation을 사용해볼 일이 있었다. 
그 때 처음 jsx에서 .표기법을 본 것 같은데 공식 문서를 보다보니 설명이 있었다.

Stack형 네비게이터 사용 코드. (react-router와 유사)
```tsx

 <Stack.Navigator>
      <Stack.Screen name="Home" component={Home} />
      <Stack.Screen name="Notifications" component={Notifications} />
      <Stack.Screen name="Profile" component={Profile} />
      <Stack.Screen name="Settings" component={Settings} />
</Stack.Navigator>
```

이런 방법은 하나의 모듈로 여러 컴포넌트를 관리할때 좋다고 한다. 비슷한 용도의 유틸 모듈에 메서드들을 모아두고 사용하는 것과 같은 이치인듯 하다.  

```tsx
import React from 'react';

const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>;
  }
}

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />;
}
```

## 컴포넌트 이름이 대문자로 시작해야 하는 이유?

생각보다 이유가 단순하다.. >> 컴파일시 일반적인 HTMLElement와 구분해주려고..



## 동적으로 컴포넌트를 선택해서 이용하는 패턴

``` tsx

import React from 'react';
import { PhotoStory, VideoStory } from './stories';


//ex 여러 리스트 아이템을 그리는 중에 리스트가 타입에 따라 다를 때 이용하면 좋을 듯. 


const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  const SpecificStory = components[props.storyType];
  return <SpecificStory story={props.story} />;
}

```

# [render props](https://ko.reactjs.org/docs/render-props.html)

아래와 같이 데이터를 DataProvider에서 받아와서 callback에 전달하는 방식이다. 
이렇게 될 경우 HOC 와 같이 로직을 재사용 할 수 있게 된다. 

```tsx
<DataProvider render={data => (
  <h1>Hello {data.target}</h1>
)}/>
```


이 render props는 children로도 정의가 가능하다. 실제로 Context api의 Consumer도 이런식으로 callback을 통해 랜더링 한다.
```TSX
<MyContext.Consumer>
  {value => /* context 값을 이용한 렌더링 */}
</MyContext.Consumer>
```

내부에서는 아래와 같이 데이터를 전달할 수 있다. 
```TSX
lass Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    //데이터의 변화를 구독한다. 
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>

        {/*
          <Mouse>가 무엇을 렌더링하는지에 대해 명확히 코드로 표기하는 대신,
          `render` prop을 사용하여 무엇을 렌더링할지 동적으로 결정할 수 있습니다.
        */}
        {this.props.render(this.state)}
      </div>
    );
  }
}
```

혹은 아래처럼 HOC를 통해 구현도 가능하다. 컴포넌트를 받고 직접, 데이터를 주입하는 패턴으로도 사용이 가능하다.

```TSX
function withMouse(Component) {
  return class extends React.Component {
    render() {
      return (
        <Mouse render={mouse => (
          <Component {...this.props} mouse={mouse} />
        )}/>
      );
    }
  }
}
```

## Error boundary

리액트에서 렌더중 에러가 발생하면 해당 에러가 발생한 트리를 언마운트 해버린다 그래서 이때문에 해당 에러를 처리해주지 않으면 [WSOD](https://medium.com/wix-engineering/white-screen-of-death-how-to-handle-errors-in-react-native-4d57ac82b6d8) 가 발생하는데 이는 일부분의 에러가 앱 전체를 죽게 만들기 때문에 사용자의 UX에 굉장히 좋지 못한 경험을 준다. 
하지만 왜 이렇게 리액트는 트리를 때어 버릴까? 알아보니 잘못된 정보를 보여주는 것 보다 랜더링을 하지 않는 것이 더 낫다고 판단했기 때문이라고 한다. 때문에 이를 처리해줄 수 있는 fallback UI 를 제공할 수 있는 방법이 Error boundary 이다. 
아래 코드를 참고하자.

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 다음 렌더링에서 폴백 UI가 보이도록 상태를 업데이트 합니다.
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 에러 리포팅 서비스에 에러를 기록할 수도 있습니다.
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 폴백 UI를 커스텀하여 렌더링할 수 있습니다.
      return this.props.renderErrorView
        ?this.props.renderErrorView() //커스텀뷰 렌더링
        :<h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}
```
## Error boundary with HOC

ErrorBoundaryView를 적용하면서 조금 더 편하게 사용하기 위해서 컴포넌트를 정의하고 export 할때 ErrorBoundaryView를 감싸서 사용할 수 있도록 withErrorBoundary hoc 를만들어 제공하기로 했다.
이를 위한 방법을 찾아보았는데 아래와 같은 방법을 적용하게 되었다.

```tsx
function withErrorBoundaryView<Props>(
  WrappedComponent:Component<Props>
  ,renderErrorView:()=>ReactNode):Component<Props>{

  return(props:Props)=>{
    <ErrorBoundary renderErrorView={renderErrorView}>
      <WrappedComponent {...props}/>
    </ErrorBoundary>
  }
}
```

[errorboundary with HOC](https://stackoverflow.com/questions/65567374/building-a-higher-order-component-error-boundary)


# HOOK
hook을 사용하게된 이유는 무었일까? react공식 문서에 따르면 class 컴포넌트내에서 사용하던 방식으로는 state관리 로직이 view와 온전히 분리 시킬 수 없었기 때문이라고 한다. 
나또한 이 내용에 대해서 공감을 한다. class component에서는 view와 로직을 분리하고, 로직들을 재사용하기 위해서 문서의 윗부분에 설명한 HOC, renderProps를 이용할 수 있지만 이를 이용해도 여전히 render와 같은 view함수 등을 이용해야한다.
또한 class 기반 컴포넌트는 this bind등을 계속해서 해줘야 한다는 귀찮음이 있었다. 때문에 function component + hook도 괜찮은 방법 같다는 생각이 든다. 

HOOk을 사용하면 컴포넌트의 생명주기와 관련 없이 로직의 관계에 따라 코드의 위치를 둘 수 있다는 장점도 있는 것 같다.

## [custom hook](https://ko.reactjs.org/docs/hooks-overview.html) 
useReducer, useState 등 react hook api에서 기본적으로 제공하는 hook들은 이전에 개인 프로젝트를 할때 몇번 이용해 봤었다. 하지만 custom hook은 정의해서 사용해본 경험이 없었다. 이번에 공식 문서를 통해서 custom hook을 처음 접했는데, 내부에서 구독을 통해 데이터들을 업데이트 하고, 이 데이터를 state를 이용해 관리하고 이를 외부에 내보낸다는 점에서 
Class Component 에서 사용하는 HOC 패턴과 매우 유사하다는 생각을 했다. 다만 HOC는 내부에 컴포넌트를 받아서 이를 주입해주는 번거로운 작업이 있다는 것과 View 인 컴포넌트를(추상화 되어있긴 하지만) hoc 내부에서 알아야 한다는 점이 다른 것 같다. hook을 사용하면 view와 state관리 로직이 잘 분리가 되는 것 같다는 생각이 들었다. 



``` TSX

import React, { useState, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}

```

## hook을 function component 내부에서만 사용할 수 있는 이유
hook을 function 컴포넌트 내부에서만 사용할 수 있도록 react에서 제한 한 이유는 closure을 이용해 컴포넌트 내부의 state와 props에 접근하기 편하도록 하기 위해서이다. 

## useEffect 
* side effect 처리를 위한 hooks로써 ** 컴포넌트가 업데이트 된 뒤에** callback을 실행한다. callback 실행은 비동기적 (동기는 useLayoutEffect 이용)
* cleanup 함수 실행 시점은 useEffect가 사용된 컴포넌트가 업데이트 될때 (구독을 계속 업데이트)








## 참고 자료
[리액트 공식 문서](https://ko.reactjs.org/docs/jsx-in-depth.html)



