---
title: JS - 타입
date: "20200704"
description: "JS의 내장 타입에 대한 이해 "
---


# 타입

자바스크립트는 동적 언어이기 때문에 변수를 선언 할 때, 타입을 명시해주지 않는다. 이러한 부분때문에 자바스크립트에서 타입이 있다는 사실을 간과하기 쉬운데 ECMA에서는 다음과 같이 말하고 있다.
```
ECMAScript 프로그래머가 언어를 이용하여 직접 조작하는 값들의 타입이 바로 ECAMScript 언어인데, 이 언어 타입에는 Undefined, Null, Boolean,String,Number, Object 가 있다.
```

여기서 '타입'이란 자바스크립트 엔진, 개발자 모두에게 어떠한 값을 다른 값과 분별할 수 있는, 고유한 내부특성의 집합이다. 

---

## 내장 타입

자바스크립트에는 7가지 내장 타입이 있다. 

1. null
2. undefined
3. boolean
4. number
5. string
6. object
7. symbol

여기서 object를 제외한 타입을 primitive Type 이라고 한다. 

```js
    typeof undefined==="undefined";//true;
    typeof false==="boolean";//true;
    typeof 42==="number";//true;
    typeof "42"==="string";//true;
    typeof {a:1}==="object";//true;
    typeof Symbol()==="symbol";//true;
    typeof null==="object"//true????????
```

~~null은 falsy 한 유일한 원시값이면서 타입은 object인 특이한 값이다.~~


인줄 알았지만 MDN에선 falsy 한 값을 다음과 같이 정의 하고 있다. 

|값|설명|
|--|--|
| false |	키워드 false |
| 0 |	숫자 zero |
|-0 |	음수 zero |
|0n	| BigInt. 불리언으로 사용될 경우, 숫자와 같은 규칙을 따름. 0n은 거짓 같은 값. |
| "" |	빈 string |
| null |	null - 아무런 값도 없음 |
| undefined |	undefined - 원시값 |
| NaN	| NaN - 숫자가 아님 |

이 표를 보면 undefined도 falsy한 값이지만 원시값임을 알 수 있다. 

```js
typeof function foo(){}==="function"//true;
```
function은 object의 하위 타입으로 호출가능한 object이다. 
function이 object여서 좋은 점은 내부에 프로퍼티를 둘 수 있다는 것이다. 
예를 들어 
```js
function foo(a,b){

}

console.log(foo.length)//2
```
과 같은 것이 가능 한 것 이다. 

배열또한 같은 이치로

```js
typeof [1,2,3] // "object" 임을 알 수 있다.
```
이 또한 object의 하위 타입이다. 

---

## "값"이 타입을 가진다.

여타 언어들과 달리 js에서는 변수에는 타입이 없다. 다만 '값'에 타입이 있을 뿐이다. 

따라서 자바트크립트의 변수는 처음에 넣었던 값의 타입과 나중에 넣는 값의 타입이 달라도 된다. 

```js
let a=1;
typeof a;//"number"

a=true;
typeof a;//boolean;
```
위와 같이 변수 자체에 타입이 있는 것이 아니라 변수 내부의 "값"에 타입이 있는 것임을 알 수 있다. 

## undefined vs undeclared

undefined는 접근 가능한 네임스페이스에 변수가 선언 되었지만 값이 할당되지 않은 것 이고, undeclared는 변수가 아예 선언되지 않은 것이다. 
이 둘은 엄연히 다르다.

```js
let t;
//undefined
console.log(t)
//VM1861:1 undefined

console.log(a);
//VM1779:1 Uncaught ReferenceError: a is not defined

```
하지만 여기서 조금 이상한 점이 있다.

```js
let a;
typeof a==="undefined";//true
typeof k==="undefined";//true
```
이것은 typeof의 특성이다. 

이러한 특성은 이상해 보일 수 있으나 브라우저에서 여러 스크립트파일의 변수가 전역 네임스페이스를 공유할 때 유용하다. 

한 예로 프로그램의 디버깅모드를 DEBUG 라는 전역 변수플래그로 조정한다고하자. 콘솔창에 메시지 로깅 등 디버깅 작업을 하기전, 이 변수의 선언 여부를 체크해야할 것이다. 최상위 전역 스코프에 
let DEBUG=true 라고 'debug.js'에서 선언하고 이 파일을 브라우저에서 테스트 시에 로딩하면 될 것이다. 

그러나 만약 다른 어플리케이션 코드에서 DEBUG 전역변수를 체크하기 위해서는 다음과 같이 해야한다. 

```js
if(DEBUG){//에러!
    console.log("디버깅 시작");
}

if(typeof DEBUG!=="undefined"){//안전하게 체크 가능!
console.log("디버깅 시작");
}
```
만약 DEBUG 뿐 아니라 내장 api에도 특정 함수나 변수가 있는지 체크하고 싶을땐 위의 방식을 이용해야 한다. 

한 예로는 
```js
if("undefined"===typeof foo){//not defined일 때, undefined 가 된다. 
    foo=()=>{

    }
}
```
와 같이 전역공간에 같은 네임이 사용되었는지 확인하고 사용할 수 있다. 

만약 이와같이  typeof를 사용하지 않고 전역공간에 특정 object가 있는지 확인하고 싶다면 다음처럼 해도 가능하다.

```js
if(window.DEBUG){
    console.log("디버깅을 시작합니다.")
}
```
다만 이러한경우는 브라우저상에서만 가능하고, node.js 등과 같은 환경에서는 불가능 하다. 

그리고 typeof 의 안전장치는 특정 라이브러리를 가져다가 쓸 때도 유용하게 사용할 수 있는데, 다음과 같은 방법들이 있다. 

```js
function doSomethingCool(){
    let helper=(typeof funcFoo!=="undefined")?funcFoo:function(){};
    let val=helper();
}
```
위와 같이 funcFoo가 이미 정의되어있으면 그대로 사용하고 정의되어있지 않으면 새로 정의해서 사용하는 패턴을 이용가능하다. 


사실 다음과 같이 "의존성주입" 이라는 방식으로 조금더 간단하게 의존성을 전달할 수도 있다. 

```js
function doSomethingCool(){
    let helper=funcFoo||function(){}

    let val=helper();
}



