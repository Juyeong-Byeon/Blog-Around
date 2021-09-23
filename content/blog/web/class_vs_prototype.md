---
title: "prototype vs class in js"
date: "20210919"
description: "프로토 타입 상속에 대해 알아보고, es6의 class가 어떻게 구현 되어있는지 알아보자."
---

prototype에 대해서 공부를 하고 개념에 대해서 이해를 하고 있기는 했지만 이를 es6의 class와 직접 적으로 연관 시켜서 생각해본적은 없었다. 
이번에 공부를 하면서 알게된 내용들이 있어서 정리하게 되었다. 

# prototype chain과 상속
JS 는 타 언어들과 다르게 class 기반의 상속이 아니라 prototype의 상속을 채택하고 있다. js에서 prototype object이란 특정 종류의 객체의 내부 메소드와, 내부 프로퍼티 들에 대한 정보를 가진 설계도 객체이다.
class와 같이 object의 설계도 정도로 생각하면 될 것 같다. 특정 prototype을 가지고 있는 object는 prototype 내에 있는 속성들과 methods들을 사용할 수 있다.
또한 prototype이 다른  prototype object로  prototype을 가질 수 있고, 메소드와 속성을 사용할 수 있는데, 이 것을 상속이라고 하고 이 prototype 간의 관계를 나타낸 것을 prototype chain이라고 한다.

prototype chain: 프로토타입기반 상속 메커니즘에서 상속의 구조를 표현하는 자료구조로 __proto__를 계속해서 타고 올라가면서 해당 prototype에 메서드나 속성이 있는지 체크하는 구조로 작동한다. 

# prototype 상속

prototype 상속 방식은 2가지가 있다. 이 방식들을 보았을때, 유연하긴 하지만 상속을 언어 자체에서 제공하기보다 상속을 사용하기 위해
애쓰는 느낌이었다. 

```js
//super
function Ellipse(w,h){
    this.w=w;
    this.h=h;
}

Ellipse.prototype.getArea=function(){
    return Math.PI*this.a*this.b;
}

Ellipse.prototype.toString=function(){
   return "ellipse"
}

//children
function Circle(r){
    this.w=r;
    this.h=r;
}

Circle.prototype=Object.create(Ellipse.prototype,{// prototype을 상위 객체로 넣어준다. 
    constructor:{
        configurable:true,
        enumerable:true,
        value:Circle,//constructor에는 자신을 넣어준다. 
        writable:true
    }
})

console.log(Circle.prototype.getArea===Ellipse.prototype.getArea)//true
```
위의 방식에는 생성자는 상속이 안된다. 때문에 만약 생성자도 상속받아 확장 하고 싶으면 Circle의 구현을 바꿔줘야 한다. 

```js
//children
function Circle(r){
   Ellipse.call(this,r,r);
}
```


위 Object.create를 이용하는 방법 말고도 상속을 구현하는 방법도 있다. 
```js
function Ellipse(w,h){
    this.w=w;
    this.h=h;
}

Ellipse.prototype.getArea=function(){
    return Math.PI*this.a*this.b;
}

Ellipse.prototype.toString=function(){
   return "ellipse"
}

//children
function Circle(r){
    this.w=r;
    this.h=r;
}

Circle.prototype=new Ellipse();

Circle.prototype.constructor=Circle;

```
위방 식은 이미 생성된 Ellipse의 프로퍼티를 낭비하는 단점이 있다. 



# class 상속

es6부터 추가된 class 상속은 너무나 잘 알려져 있고 간단하다. 
단순히 extends 키워드를 이용해 Ellipse를 상속할 수 있다. 
class를 사용하고 있긴 하지만 사실 사실 이 간단함 속에는 위의 과정이 내부적으로 일어나고 있기 때문이다. 
```js
class Circle extends Ellipse{
    constructor(center,radius){
        this.center=center;
        this.radius=radius;
    }

    foo(){
        //
    }
}
```

아래의 코드를 콘솔에 입력해보면 class 도 prototype을 이용하고 있음을 알 수 있다.

```js
class Foo{
  constructor(){
    this.a=10;
    this.b=20;
  }

  fuzz(){
    console.log('fuzz');
  }
}

const foo=new Foo();

foo.fuzz();//fuzz 자신을 생성한 생성자 함수의 prototype 객체로부터 참조 (암묵적으로 __proto__ 참조)
foo.__proto__.fuzz();//fuzz 자신을 생성한 생성자 함수의 prototype 객체로부터 참조 (명시적으로 __proto__ 참조)
Foo.prototype.fuzz();//fuzz
```
또한 아래처럼 새로운 class를 상속받아 만든 후 상속 받은 class가 같은 prototype객체에 있는 fuzz를 참조하고 있음을 알 수 있다.
이는 위에서 직접 상속을 구현해준 방식에서도 확인 할 수 있었다.
```js
class Foo2 extends Foo{

  check(){
    console.log(this.__proto__);//FOO {check,fuzz2} 자신을 생성한 생성자 함수의 프로토타입 객체
  }

  fuzz2(){
    console.log('fuzz2');
  }
}

const foo2=new Foo2();
console.log(Foo2.prototype.fuzz===Foo.prototype.fuzz);//true
//Foo2의 prototype에 Foo 생성자를 이용해 객체를 생성해 넣어줬기 때문에 위 둘은 같은 함수의 레퍼런스를 참조한다.
```

그리고 new Foo2().__proto__에 값을 새로 넣게 되면 어떻게 될까? 
자신을 생성한 생성자 함수의 prototype을 참조 하게 됨으로 Foo2.prototype에 값이 추가된다.


```js
new Foo2().__proto__.b=1000;//자신을 생성한 생성자의 prototype 객체를 참조해서 b를 넣어준다.
Foo2.prototype//자신의 prototype공간.
Foo2.prototype instanceof Foo//true instance of 는 prototype chain에 특정 생성함수가 있는지 확인해주는 키워드이다.
```

마지막으로 Foo2의 프로토 타입 체인이 어떻게 연결되는지 확인해보자.

```js
new Foo2().__proto__//Foo {b: 1000, constructor: ƒ, check: ƒ, fuzz2: ƒ} Foo2.prototype 즉 new Foo()
new Foo2().__proto__.__proto__//{constructor: ƒ, fuzz: ƒ} // new Foo()의 __proto__ 즉 Foo.prototype
new Foo2().__proto__.__proto__.__proto__//{constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …} Foo.prototype은 new Function()로 만들어졌기 때문에 Foo.__proto__  === Function.prototype
new Foo2().__proto__.__proto__.__proto__.__proto__//null new Object().__proto__.__proto__는 null
```
