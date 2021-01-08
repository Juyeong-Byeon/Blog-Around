---
title: JS - 프로토타입
date: "20200706"
description: "JS의 프로토타입 상속"
---


# Prototype 

이 정리는 [프로토타입 이해하기](https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67),[MDN Object prototypes](https://developer.mozilla.org/ko/docs/Learn/JavaScript/Objects/Object_prototypes) 를 참고해 작성한 글입니다. 

자바스크립트를 처음 배우기 시작할 때 JS는 객체 지향형 언어의 패러다임의 특성을 가지고 있다는 말을 들었을 것이다. ES6 에서 class 가 추가되긴 하였지만 js의 상속은 다른 언어의 상속과는 다르다. 이는 JS가 프로토 타입 상속을 하기 때문인데 Prototype이 뭔지 정리해보자. 

```js
function Person(){
    this.ear=2;
    this.nose=1;
    this.mouth=1;
}

let person1=new Person();
let person2=new Person();

person1.ear;//2

```

이렇게 될 경우 person1과 person2에 각각의 ear, nose, mouth 등이 각각 생성되어 메모리를 차지 한다. 만약 수 많은 객체가 생성되면 메모리의 낭비가 심해질 것이다. 이러한 문제를 해결하고, 여러 메서드, 오브젝트들을 공유하기 위해 사용하는 것이 바로 Prototype 상속이다.

prototype은 하나의 원형이 되는 함수에서 생성되는 모든 함수들이 공유하는 값, 객체들이 들어있는 object인데, 같은 공유되는 ref를 참조한다.

prototype 으로 상속을 할 경우 다음과 같이 하면 되는데
```js
function Person(){}
Person.prototype.name="이름";

let person1=new Person();
let person2=new Person();

person1.name;//"이름"
person2.name;//"이름"
```
이럴 경우 Person object내의 prototype object에 대한 ref가 공유되기 때문에 같은 값을 가지게 된다. 

이 것에 대해 조금 더 차근차근 알아보자

## 함수 생성과정
### 1. prototype Object 

js 에서 객체는 항상 함수로 생성된다. 
```js
let arr=[]; // 사실 let arr=new Array();
let obj={};// 사실 let obj=new Object();
let a=1;//let a=new Number(1)
```
위의 예에서 알 수 있듯이 우리가 사용하는 객체는 항상 함수를 통해 생성됨을 알 수 있다. 

함수를 처음에 정의하면 constructor 라는 자격을 부여해 정의된 함수로 새로운 객체를 만들어낼 수 있는 자격을 준다. 

object.constructor 를 이용하면 해당 객체가 어떠한 생성자를 통해 만들어졌는지 확인 할 수 있다. 

```js

function foo(){}
foo.constructor//Function() { [native code] }new Function()을 통해 생성되었음을 알 수 있다. 
let foo2=new foo();
foo2.constructor//foo(){} 함수를 통해 만들어졌음
```



### 2. prototype object 생성, 연결 
이렇게 constructor로서의 자격을 얻은 함수는 새로 생성된 prototype 객체와 연결이 된다. 이 객체는 상속이 될 때, 하위객체에서 접근 가능하며, 접근하여 값을 읽어올 수 있는 공유된 객체이다.

```js
function foo(){}
foo.prototype
/*
foo.prototype.age=10
10
foo.prototype
{age: 10, constructor: ƒ}//prototype
*/

let foo2=new foo();
foo2.age
//10
foo2.__proto__
//{age: 10, constructor: ƒ}//공유되는 prototype
```
여기서 한가지 더 볼 점은 __proto__는 자신이 상속 받은 객체의 prototype object이다. 위의 참조가 가능하게 하는 이유는 이 객체 때문이다. 
js에서 객체 내에 선언되어있는 객체가 존재 하지 않으면 __proto__를 타고 올라가 상위에서 찾아내고, 최 상위인 object에도 없으면 에러를 일으킨다 이것을 prototype link라고 한다. 

예를 들어 이를 통해 Array가 Object의 메서드 들을 상속 받았음을 증명할 수 있다. t

```js
let arr=[];
arr.__proto__;//arr의 __proto__는 Array정의시에 생성된 Array.prototype()
/*Array에서 지원하는 많은 메서드들
concat: ƒ concat()
constructor: ƒ Array()
copyWithin: ƒ copyWithin()
entries: ƒ entries()
...
..
.

__proto__: Object //이 를 보면 Array의 prototype 또한 Object로 부터 상속받아 생성되었음을 알 수 있다. 
*/
```



