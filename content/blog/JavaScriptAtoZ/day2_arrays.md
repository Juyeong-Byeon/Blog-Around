---
title: JS - 자바스크립트 배열 메서드
date: "20200327"
description: "각종배열 베서드와 유사배열 객체"
---

# 배열 메서드와 유사배열 객체

## 1. 배열의 메서드
배열은 Array 타입의 객체이다. Array.prototype의 객체를 상속받는 다는 것이다. 이 내부에는 많은 메서드가 있는데, 배열의 처리에 유용한 메서드가 많아 몇가지를 정리해보려고 한다. 
### 1. 수정메서드: 원본 배열을 수정하는 메서드

#### push
push 메서드는 배열의 마지막 하에 하나 이상의 요소를 추가한다. 반환으로는 그 배열의 길이를 반환한다. 
```javaScript
let a=[1,2,3,4];
a.push(5);//return 5;  배열[1,2,3,4,5]
```
#### pop
pop 메서드는 배열의 마지막 요소를 잘라내어 반환한다. 
```javaScript
let a=[1,2,3,4,5];
a.pop();//return 5;  배열[1,2,3,4]
```
#### shift
맨 앞에 값을 제거하고 모든 배열요소를 왼쪽으로 이동시킨다. 반환값은 삭제된 요소의 값이다. 
```javaScript
let a=[1,2,3,4];
a.shift();//return 4;  배열[1,2,3]
```
#### unshift
배열을 모두 오른쪽으로 한칸씩 밀고 값을 추가한다. 반환값은 배열의 길이이다.
```javaScript
let a=[1,2,3,4];
a.unshift(0);//return 5;  배열[0,1,2,3,4]
```
#### splice

특정인덱스의 배열요소를 갈아끼울때 사용하는 범용메서드이다. 요소를 끼워 넣기만 할 수도 있고, 삭제만 할 수 도 있다. 삭제된 요소는 배열로 만들어서 반환한다. splice-이어붙이기 
```javaScript
arr.splice(index,howMany,배열에 들어갈 요소 쉼표구분);
let a=[1,2,3,4];
a.splice(3,1);//return [4];  배열[1,2,3]
a.splice(3,1,4)//4를 다시 추가 
a.splice(1)//index 1 이후의 값을 모두 삭제
```

#### sort
배열 안의 요소를 정렬한다. 인수로는 실제로 비교를 담당하는 함수를 넘겨주며 반환값은 정렬된 배열이다.
```javaScript
let a=[4,2,1,3];
a.sort((a,b)=>{return a-b});//return [1,2,3,4];
```
비교를 담당하는 함수의 인수는 2개이며, 첫 번째 인수는 인접한 요소의 왼쪽 요소고 두 번째 인수는 오른쪽 요소이다. 
비교함수를 f(a,b)라고 했을때 다음과 같이 정렬된다.
1. f(a,b)<0 일 경우 a를 b보다 작은 인덱스에 정렬한다. 
2. f(a,b)==0일경우 a와 b의 순서를 바꾸지 않는다.
3. f(a,b)>0 b를 a보다 작은 인덱스에 정렬한다. 

만약 비교함수를 지정하지 않으면 배열의 요소를 문자열로 변환한 다음 사전순으로 지정하고 undefined가 있으면 마지막에 위치시킨다. 

### 2. 접근자 메서드 : 새로 가공한 배열을 반환하는 메서드 원본은 바뀌지 않는다.
#### join 
모든 요소값을 문자열로 바꾼 후 인수로 받은 문자로 연결해서 반환한다. 요소값이 undefined 나 null 일때는 그 요소 값을 빈 문자로 간주한다. 인수를 지정하지 않으면 쉼표로 배열의 요소값을 연결해서 반환한다. 

```javaScript
let a=[1,2,3,4];
a.join();//"1,2,3,4"
a.join('-')//1-2-3-4
```
#### concat
인수로 받은 값을 그 배열의 요소로 추가해 새로운 배열을 생성, 받은 인수가 배열이면 spread 한 후에 배열에 추가한다. concat은 연결한다는 뜻이다. 

```javaScript
let a=[1,2,3,4];
a.concat([5,6,7]);//return [1,2,3,4,5,6,7];
```
#### slice
slice 메서드는 배열의 일부를 제거한 새로운 배열을 반환한다. 
arr.slice(a,b)라고 할때 a는 요소를 자를 시작 인덱스이다. b는 요소를 자를 마지막 인덱스 앞의 요소이다. 즉 인덱스는 a<=target<b 이다. 
```javaScript
let a=[1,2,3,4];
a.slice(0,2);//return [1,2];
```
만약 -1을 인수로 넣을경우 마지막 -1 인덱스까지 slice 를 진행한다. 
#### indexOf lastIndexOf
indexOf 는 배열 내에서 어떠한 값을 가지는 요소의 index를 찾을 때 사용 된다. indexOf는 가장 먼저 찾아진 index를 반환하고, lastIndexOf는 찾아진 요소의 마지막 index를 반환한다. 

```javaScript
let a=['ㄱ','ㄴ','ㄷ','ㄹ'];
a.indexOf('ㄱ'); //return 0 
a.indexOf('ㄷ',2)//2번 index부터 검색 시작 
```
#### toString toLocaleString
배열내용을을 쉼표로 구분해 반환한다.
toLocaleString은 해당 지역에 맞는 언어로 지역화한 문자열로 나타낸다. 

### 반복메서드 : 배열의 모든 요소를 순회해 작업을 수행하거나 필터링을 해 리턴해주는 함수, 대부분 첫번 째 인수로 함수의 참조를 받아오는 고차 함수이다. 
#### forEach
인수로 받은 함수를 배열의 요소별로 한번씩 실행한다. 그 함수에는 인수 3개가 전달된다.(value,index,array);
```javaScript
let a=[1,2,3,4];
a.forEach((val,i,arr)=>console.log(val))//1 2 3 4 
```
for문 대신 사용하기 유용하다.
#### map

map 메서드는 인수로 받은 함수를 배열의 요소별로 한번씩 실행하며 마지막에는 그 함수가 반환한 값으로 배열을 만들어 반환한다. 인수는 마찬가지로(val,index,arr)이 넘어온다. 
map의 파라메터 메서드는 반드시 값을 반환해야 한다. 

```javaScript
let a=[1,2,3,4];
a.map((val,index,arr)=>val*2);//return [2,4,6,8]
a.map((val,index,arr)=>val*2).map((val,index,arr)=>val*2);
//반환되는 함수에 연달아 사용할 수도 있다.
```
#### reduce
배열의 첫번째 요소부터 마지막 요소까지 합성곱 처리를 한다. 합성곱 처리는 배열 요소 하나를 함수로 처리한 후에 그 반환 값을 다음번 요소를 처리 할 때, 함수의 입력 값으로 사용하는 처리 방법을 말한다. 인수는 다음과 같다.reduce((prev,value,index,array)=>{},initialValue)
```javaScript
let a=[1,2,3,4];
a.reduce((acc,val,index,arr)=>acc+val);//return 10;
```

## 2. 다차원 배열

자바스크립트에서는 다차원 배열을 정의하기 위한 문법을 제공하지 않는다. 하지만 배열에 배열을 중첩하면 다차원 배열과 유사한 기능을 사용할 수 있다. 

다음과 같은 방법들로 다차원 배열을 정의 할 수 있다. 
```javaScript
let a=new Array(5)
for(let i=0;i<5;i++){
    a[i]=new Array(5)
}//5*5 다차원 배열 정의

let b=[
    [1,2,3],
    [1,2,3]
]
//리터럴을 이용해서 생성, 초기화 가능
```
각 열에 생성된 배열은 배열 객체를 포함하는 것이다. 
따라서 각각이 다른 객체이기 때문에 다른 길이, 다른 type의 값을 포함할 수 있다.
## 3. 유사배열객체 

자바스크립트에서 배열은 Array 타입의 객체이다. Array 타입의 객체는 다음과 같은 성질이 있다. 

1. 0 이상의 정수값을 프로퍼티 이름으로 가진다. 
2. length 프로퍼티가 있으며, 요소를 추가하거나 삭제하면 length 프로퍼티 값이 바뀐다. 또한 length 프로퍼티 값을 줄이면 그에 따라 배열의 크기가 줄어든다. 
3. 프로토타입이 Array.prototype이므로 상속받아 사용할 수 있다. 또한 instanceof 연산자로 실행하면 Array 생성자 함수로 생성된 객체로 표시된다. 

하지만 array-like object는 length 프로퍼티가 존제하고 프로퍼티 이름이 0이상의 정수인 것만 만족하면 그 객체를 유사배열 객체라고 한다. 

다음과 같은 객체가 유사배열 객체이다. 
함수의 인수를 저장한 
1. Argument 객체

2. DOM의 document.getElementsByTagName 메서드 document.getElementsByName 메서드등이 반환하는 NodeList 객체 

```javaScript
let a={};
for(let i=0;i<10;i++){
    a[i]=i;
}
a.length=10;
```


