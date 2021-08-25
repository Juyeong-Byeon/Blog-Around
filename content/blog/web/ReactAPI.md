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


## 참고 자료
[리액트 공식 문서](https://ko.reactjs.org/docs/jsx-in-depth.html)



