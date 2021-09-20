---
title: "prototype vs class in js"
date: "20210919"
description: "프로토 타입 상속에 대해 알아보고, es6의 class가 어떻게 구현 되어있는지 알아보자."
---

prototype에 대해서 공부를 하고 개념에 대해서 이해를 하고 있기는 했지만 이를 es6의 class와 직접 적으로 연관 시켜서 생각해본적은 없었다. 
이번에 공부를 하면서 알게된 내용들이 있어서 정리하게 되었다. 

# prototype chain과 상속
JS 는 타 언어들과 다르게 class 기반의 상속이 아니라 prototype의 상속을 채택하고 있다. js에서 prototype object이란 특정 종류의 객체 내부 메소드와, 속성 들에 대한 정보를 가진 객체이다.
class와 같이 object의 설계도 정도로 생각하면 될 것 같다. 특정 prototype을 가지고 있는 object는 prototype 내에 있는 속성들과 methods들을 사용할 수 있다.
또한 prototype이 다른  prototype object로  prototype을 가질 수 있고, 메소드와 속성을 사용할 수 있는데, 이 것을 상속이라고 하고 이 prototype 간의 관계를 나타낸 것을 prototype chain이라고 한다.

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
