---
title: "js 함수, execution context"
date: "20210921"
description: "js의 함수, execution context,클로저, 고차함수 등을 복습해보자."
---



흠,, 이렇게 함수를 정의하는건 못봤지만 이렇게도 가능하다고 한다. 

```js
var square=new Function("x","return x*x")
```
## 중첩 함수

특정 함수의 내부에 선언된 함수 inner function이라고도 하는데 nested function이 맞는 표현이다.

```js
function foo(){
    function foo2(){

    }
}
```
중첩 함수는 밖에서 사용할 필요없는 함수를 숨겨 내부 적으로만 사용하도록 하거나, closure, 커링 등의 함수특성을 이용할 때 유용하게 사용한다.

## 즉시 실행 함수

일반적으로 즉시 실행 함수는 함수를 정의해서 사용하되, 함수를 재사용할 필요가 없을 때 많이 사용하는 것 같다.
불필요한 네이밍을 통해 네임스페이스를 더럽히지 않을 수 있고, 함수 내부의 실행 컨텍스트를 분리해 실행할 수 있다. 
본인은 주로 아래의 상황에 사용한다. 아래 처럼 함수 전체를 async로 만들지 않고 일부만을 비동기적으로 처리하고싶을 때 아래의 방식을 이용한다.

```js
(async ()=>{
    const asyncRes=await asyncFunction();
    //..some code;
    return res;
})()
```
아래처럼 즉시실행 함수를 사용할 수도 있는건 이번에 처음알게 되었다

```js
+function foo(x){
    console.log(x);
}(1)//1
```
위 처럼 "+"를 함수 선언문 앞에 붙여도 즉시실행 함수를 사용할 수 있다. 


## 함수 인수 생략

```js
function foo(x,y){
    y=y||0;
    return x+y;
}
```
요즘엔 이런 문법도 가능
```js
function foo(x,y){
    y||=0;
    return x+y;
}
```

## 가변 길이 인수 목록

es6에서 아래의 방법을 추천하지는 않지만 아래의 방식으로 arguments가 모든 함수의 지역 변수로 있어 명시되지 않은 파라메터를 참조할 수 있다.
```js
function foo(x,y){
    arguments[3]||=0;
    return x+y+arguments[3];
}

foo(10,20,30)//60;
```

Rest파라메터를 이용하는 방식도 있다. 이 방식이 나온 이유는 arguments 방식은 실제 배열이 아니라서 Array 프로토타입 메소드를 이용하기 위해서는 split() 등을 이용해서 array로 만들어 줘야 하는데, 이 과정이 귀찮아서이다. 

```js
function foo(x,y,...theArgs){
    theArgs[1]||=0;
    return x+y+theArgs[1];
}

foo(10,20,30)//60;
```
## 재귀 함수

재귀함수는 함수 내부에서 함수 자신을 호출하는 방식을 말한다. 
보통 아래처럼 이용한다.

```js

function fact(n){
    if(m<=1)return 1;
    return n*fact(n-1);
}
fact(5);
```

그런데 이를 함수의 이름을 명시하지 않고도 가능함은 이번에 처음알게 되었다.
아래처럼 즉시실행함수로도 구현 할 수 있다.

```js
+function(n){
    if(m<=1)return 1;
    return n*arguments.callee(n-1);
}(5);
```

## 프로그램의 평가(evaluation)와 실행(execution) 과정

자바스크립트 엔진은 실행가능한 코드를 만나면 그 코드를 평가(evaluation)해서 실행문맥(execution context)를 만든 다음 실행합니다.
실행 가능한 코드의 종류에 따라서 실행 문맥이 달라지는데 3가지가 있습니다.

* 전역 코드 (window)
* 함수 코드 (function)
* eval 코드 (eval("function(){}"))

실행 문맥에는 코드가 실행되고 관리되는 영역으로 실행에 필요한 정보를 관리한다. 대표적으로 아래처럼 
lexical환경 , 변수환경, this바인딩 환경 등을 관리합니다.

```js
ExecutionContext={
    LexicalEnvironment:{
        EnvironmentRecord:{
            //환경 레코드
            //참조 유효범위 안에 포함된 identifier를기록하고 identifier와 값을 바인딩함.
            DeclarativeEnvironmentRecord:{
                //선언적 환경 레코드
                //함수, 변수, catch문의 identifier와 실행 결과가 저장되는 곳.
            },
            ObjectEnvironmentRecord:{
                //객체 환경 레코드
                //실행 문맥 외부의 객체를 참조해서 데이터를 읽거나 쓴다.
            }
        },
        OuterLexicalEnvironmentRef:{
            //nested 함수 밖의 범위를 검색할 수 있도록 바깥 함수의 LexicalEnvironment의 참조를 저장한다.
        }
    },//LexicalEnvironment same type
    VariableEnvironment:{
         EnvironmentRecord:{},
        OuterLexicalEnvironmentRef:{}
    },//LexicalEnvironment same type
    ThisBinding:null// 호출한 방법에 따라서 this 바인딩이 달라진다. 
}
```

LexicalEnvironment 는 자바스크립트 엔진이 자바스크립트 코드를 실행하기 위해 자원을 모아둔 곳으로 함수 또는 블록의 유효 범위 안에있는 identifier와 evaluation결과 값이 저장되는 곳이다. 
렉시컬 환경은  identifier가 참조할 수 있는 범위 안의 값을 바인드 해서 기록합니다.

## 전역 환경

js 엔진은 실행과 동시에 LexicalEnvironment 객체의 Global Environment를 생성합니다.

```js
GlobalEnvironment={
    ObjectEnvironmentRecord:{
        bindObject:window //변수 검색시 window에서 하게된다. 
    },
    OuterLexicalEnvironmentRef:null //GlobalEnvironment는 최상위기 때문에 outer 환경이 없다.
}

ExecutionContext={
    LexicalEnvironment:GlobalEnvironment,
    ThisBinding:window,//전역은 이래서 this가 window 이다.
}
```


## 프로그램의 평가와 전역 변수 

전역 환경과 전역 객체를 생성 한 후 자바스크립트는 프로그램을 읽어 드립니다. 자바 스크립트 프로그램을 다 읽어들인 후 프로그램을 평가하고, 
최상위 레벨에 var문즈오 작성한 전역 변수는 전역 환경의 레코드의 프로퍼티로 추가된다. 

```js
GlobalEnvironment={
    ObjectEnvironmentRecord:{
        bindObject:window //변수 검색시 window에서 하게된다. 
        //이곳에 var로 정의한 변수,함수들이 추가된다. //변수의 경우 값은 undefined (선언문만 호이스팅됨.)
    },
    OuterLexicalEnvironmentRef:null //GlobalEnvironment는 최상위기 때문에 outer 환경이 없다.
}
```
var,let 문으로 선언할 경우 실행 전에 전역으로 등록되기 때문에 ```[delete]``` 키워드로 삭제 할 수 없으나,키워드를 작성하지 않으면 삭제 할 수 있다. 

## 프로그램 실행, 실행 문맥

프로그램이 평가된 후에는 프로그램이 실행되는데, 이 실행은 프로그램 실행 문맥(execution context)안에서 실행된다. 
위에서 정리한 실행 문맥은 stack 구조로 관리 된다. 앞서 말한대로 실행 문맥은 3가지로 관리된다. 전역 실행문맥은 가장 먼저 실행돼서
call stack가장 하위에 항상 상주하고 있다. 그리고 실행 중 함수를 만나면 함수를 stack에 push 해서 제어권을 넘겨주고, 함수 실행이 끝나면 pop을 통해 제어권을 넘겨 받ㄱ는다. 
그리고 중첩 함수가 있다면 새로운 실행 문맥을 만들어 push 한다. 위에서 즉시 실행 함수를 설명할 때 언급 했듯이 즉시실행 함수를 내가 자주 사용하는 이유이다. 


```js
function foo(){
    (async ()=>{//foo와 별도로 구분된 실행 문맥 문맥을 분리했기 때문에 해당 함수 부분만 분리해 task queue에 넣을 수 있다.
        const asyncRes=await asyncFunction();
        //..some code;
        return res;
    })()
}

```

함수를 호출 하면 실행중인 코드의 작업을 멈추고 새로운 실행문맥을 생성해 스위칭을 한다. 그리고 이 함수의 실행 문맥을 호출 스택에 push 하고 ```LexicalEnvironment```를 생성 한다.
이 컴포넌트 안에는 아까 살펴 봤듯이 EnvironmentRecord(함수 참조, 함수 인자, 중첩 함수의 참조, 지역변수 )와 OuterLexicalEnvironmentRef(함수 밖을 감싸고 있는 lexical scope를 기록) 존제하고  EnvironmentRecord 속에는 함 DeclarativeEnvironmentRecord(함수 인수 기록) 가 존제 한다.
함수 내부에는 지역변수, 인수 목록, 객체 참조 등이 전달 된다. 이후 함수의 실행 문맥, 렉시컬 환경, 환경 레코드가 생성되면 실행문맥의 this 바인딩을 통해 그 함수를 호출한 객체의 참조를 저장한다. 

```js
ExecutionContext={
    LexicalEnvironment:{
        EnvironmentRecord:{
            //환경 레코드
            //참조 유효범위 안에 포함된 identifier를기록하고 identifier와 값을 바인딩함.
            DeclarativeEnvironmentRecord:{
                //선언적 환경 레코드
                //함수, 변수, catch문의 identifier와 실행 결과가 저장되는 곳.
            },
            ObjectEnvironmentRecord:{
                //객체 환경 레코드
                //실행 문맥 외부의 객체를 참조해서 데이터를 읽거나 쓴다.
            }
        },
        OuterLexicalEnvironmentRef:{
            //nested 함수 밖의 범위를 검색할 수 있도록 바깥 함수의 LexicalEnvironment의 참조를 저장한다.
        }
    },//LexicalEnvironment same type
    VariableEnvironment:{
         EnvironmentRecord:{},
        OuterLexicalEnvironmentRef:{}
    },//LexicalEnvironment same type
    ThisBinding:null// 호출한 방법에 따라서 this 바인딩이 달라진다. 
}

```

