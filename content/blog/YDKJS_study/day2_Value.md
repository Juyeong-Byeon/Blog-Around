---
title: JS - Value
date: "20200706"
description: "JS의 자바스크립트에 내장된 값 타입과 작동방식에 대한 이해."
---

# 값

##### 자바스크립트에 내장된 값 타입과 작동방식에 대한 이해.
---

## 배열
자바스크립트의 배열은 다른 strict type language와 다르게 문자열, 숫자, 객체, 배열 등 다른 어떠한 타입의 값이든지 상관 없이 담을 수 있다. 

** (이러한 배열을 heterogeneous array 라고한다.)

```js
let a=[1,'a',[a]];//가능!
```

배열의 크기 또한 미리 정하지 않고 선언할 수 있으며 원하는 값을 추가하면 동적으로 조절이 된다. 

또한 다음과 같이 중간의 index를 건너뛴 형태의 배열도 가능하다.

```js
let a=[];
a[0]=1;
a[2]=[3];

a[1]//undefined;
a.length;//3

```
이러한 형태를 sparse array라고 하고, 슬롯이 빠졌다고 한다. 

또한 배열의 인덱스는 기본적으로 숫자를 사용하지만 배열도 object이기 때문에 키,프로퍼티 문자열을 추가할 수 있다. 하지만 이럴경우 length는 중가하지 않는다. 

```js
let a=[];
a[0]=1;
a['name1']=2;
a.length//1;
a['name1']//2
a.name1//2
```

하지만 만약 다음과 같이 숫자형태의 String 타입 값을 key로 사용할경우 Number 로 자동 형변환이 되어 해당 번째의 칸에 값이 들어가고, 그 앞에는 undefined로 채워지게 된다. 
```js
let a=[]
a['16']=1;
a.length;//17
```
(이러한 것들이 가능하지만 일반적으로 key를 이용할 때는 object를 쓰는것이 바람직하다.)

---

## 유사 배열 

유사 배열 값(실제 배열은 아니지만 배열처럼 숫자를 key로 가지고 있는 객체)
```js
let foo=document.getElementsByTagName("div")
Array.isArray(foo);//false
```
를 실제 객체로 바꾸고 싶을 때 보통 배열 유틸리티 함수 indexOf, concat,forEach 를 이용해 해결하는 경우가 많다. 예를 들어 위의 예처럼 배열은 아니지만 유사배열 형태의 DOM원소 리스트가 반환된다. 다른 예로 함수에서 arguments객체를 사용해 인자를 가져오는 것도 같다. 

이런 변환은 slice() 함수 기능을 가장 많이 이용한다. 

```js
function foo(){
    let arr=Array.prototype.slice.call(arguments);
    //0부터 배열을 모두 복사 
    arr.push("b");
    console.log(arr);
}
foo("a")//[a,b]
```
```js
//es6
let arr=Array.from(arguments);
```
---

## 문자열 

흔히 문자열이 문자의 배열이라고 생각하는 경우가 많다. 하지만 이 둘은 생김새는 비슷하지만 엄연히 다르다. 
```
let a="dog";
let b=["d","o","g"];
```

문자열과 배열의 공통점을 보면 둘다 length프로퍼티, indexOf() 메서드 ,concat() 등을 가진다. 
```js
a.length;//3
b.length;//3

a.indexOf("d");//0;
b.indexOf("d");//0;

let c=a.concat("bar"); //"dogbar"
let d=b.concat(["b","a","r"]);// ["d","o"....]

a===c;//false;
b===d;//false;

a;//"dog"
b;//["d","o","g"]
```

이러한 점을 보고 문자열이 문자의 배열이라고 생각하는 경우가 있는데, 그렇지 않다. 

문자열은 "immutable"이지만 배열은 가변값 "Mutable"이다. 
a[1]="A" 등이 크롬 등은 가능하지만 일부 구버전의 익스플로러 등은 문법에러로 받아들인다. a.charAt(1)이 맞는 사용법이다. 또한 문자열은 불변값이기 때문에 문자열 메서드는 내용을 바로 변경하기 않고, 새로 문자열을 생성한 후에 반환한다. 반면 대부분의 배열 메서드는 그 자리를 바로 수정해버린다. 
```js
c=a.toUpperCase();
a===c;//false 새로운 값을 복사했기 때문에 서로 다른 값을 가리킨다.
a;//"foo"
c;"FOO"

let arr1=["f"];
let arr2=arr1
arr1.push(1);
arr1// ["f", 1]
arr2//["f", 1]
//뮤터블 하기 대문에 공유되는 메모리를 직접적으로 수정 (얕은 복사 개념 참고)

let str1="f";
let str2=str
str2.concat("1");//"f1"
str1//"f"
//이뮤터블 한 값이기 때문에 새로운 공간을 만들어 그 곳에 값을 할당 후 변수에 연결 즉 다른 값
```
그리고 push와 같은 대부분의 배열 메서드는 문자열에 사용할 수 없지만 문자열에 대해 불변 메서드등을 빌려쓸 수 있다. 이 것은 [array 메서드 중  접근자 메서드](https://github.com/wndudqus/JavaScriptAToZ/blob/master/day2_arrays.md) 를 참고 하면 이해하기 쉽다.
접근자 메서드는 본래의 배열을 수정하지 않고, 새로운 배열을 반환하기 때문에 이뮤터블한 객체인 string에서도 사용할 수 있는 것이다. 

## Number

자바스크립트에서는 숫자를 모두 number한 타입으로 사용한다. 즉 실수와 정수를 한 number 타입으로 동일하게 사용한다는 것이다. 예를 들어

```js
1===1.0//true
```
로 본다는 것이다.  특이한 점은 js는 다른 현대 프로그래밍 언어와 같이 IEEE 754 표준을 따른다 그런데 이 둘을 구분하지 않고 정수든 실수든 모두 부동 소수점 표준의 형태로 저장하는 것이다. [MDN js는 항상 64bit 부동소수점](http://jun.hansung.ac.kr/CWP/Javascript/JavaScript%20Numbers.html) 

이 때문에 js 에서 숫자를 사용할 때는 타 언어와 다르게 해당 리터럴이 실수임을 표현해주기 위해 명시적으로 .0이나 f를 붙여주는 표기를 하지 않아도 된다. 다음 예들을 보면서 세세한 내용을 이해하자.

```js
let a=10;
let b=10.0
a===b;//true
a=.2
b=0.2
a===b;//true

```
소수자리에 있는 0 들은 생략이 가능하다.
```js
let a=8.100;//8.1
```

아주 크거나 아주 작은 숫자는 exponent form으로 표시한다. 
```
let a=5E10;//50000000000
``` 

숫자값은 Number 객체에서 정의하고 있는 객체로 박싱할 수 있기 때문에 Number.prototype 메서드로 접근 가능하다 .

```js
let a=42.195;
a.toFixed(0)//42
a.toFixed(1)//42.1
a.toFixed(2)//42.19
```

그리고 다음과 같이 리터럴을 이용해 직접적으로 메서드를 호출 할 수도 있다. 
```js
(42.203).toFixed(1);
//다음과 같은 경우는 . 또한 리터럴로 해석되어 괄호를 쳐주어야 한다. 
34.toFixed(2);//에러!
```

### 작은 소수값의 비교

```js
0.1+0.4===0.5;//false
```
수식을 봤을 땐 true라고 생각할 수 있지만 부동소수점 표기로 인해 두 수가 다르다고 판정이 다는 것이다.

일반적인 어플리케이션 들은 정수의 값들을 비교하기 때문에 문제가 없다. 하지만 만약 꼭 소수를 사용해야 한다면 미세한 오차(머신 입실론)를 허용오차로 처리하는 방법이 있다. 

```js
if(!Number.EPSILON){
    Number.EPSILON=Math.pow(2,-52);//입실론 정의하는 법
}

function numbersCloseEnoughToEqual(n1,n2){
    return Math.abs(n1-n2)<Number.EPSILON//두 수의 차를 구하고 입실론보다 작으면 같은 값이다!
}

let a=0.1+0.2
let b=0.3
numbersCloseEnoughToEqual(a,b);//true
Number.EPSILON
//2.220446049250313e-16
function minComp(a,b){return Math.abs(a-b)<Number.EPSILON}
minComp(0.2,0.1+0.1)
//true
```

## 정수의 안전한 범위

부동소수점 표현법을 이용하기 때문에 정수에는 안전값의 범위가 있다. 
이 범위 안에 있는 값은 표현값과 저장값이 같다고 장담할 수 있는 범위 내의 값이다. Number.MAX_VALUE 보다 훨씬 작은 값이지만 여유롭다.

이 값은 ES6에서 Number.MAX_SAFE_INTEGER로 정의 하고있다. 최소값은 Number.MIN_SAFE_INTEGER 로 정의 하고 있다. 

## 정수의 확인
```js
Number.isInteger(43);//true
//es6 이전은

if(!Number.isInteger){
    Number.isInteger=function(num){
        return typeof num=="number"&&num%1==0;
    }
}

Number.isSateInteger(12938478190238074);

```

## 32비트 정수 
```js
a|0//32비트 정수로 강제 형변환
```
## undefined 

undefined 는 lose 모드에서 전역 스코프에서 재정의가 가능 하다.
그리고 undefined 값을 사용하고 싶을 때엔 void __과 같이 어떠한 값을 void 앞에 두면 된다. 관례적으로 void 0으로 사용한다.
```js
undefined=10;//가능하지만 하지 않아야 한다.
void 0;//undefined
```

다음과 같이 사용할 수 있다. 

```js
function someThing(){
    if(!APP.ready){
        return void setTimeout(someThing,100); 
    }
    let res;

    res=APP.goodThing();
    return res;
}

if(someThing()){
    console.log("완료");
}


```
위와 같은 방법으로 처리가 되지 않았을 땐 void로 undefined를 반환하여 참값에 의한 오류를 막는다. 

## 특수 숫자
NaN: Not a Number 이다. 예를 들어 1-"ㅁ" 과 같이 두 피연산자가 숫자가 아닐경우 결과가 NaN이 된다. 

그런데,, 여기서 아주 이상한 일이 있다. 

```js
let a=3-"a";//NaN
typeof a==="number"//true;
```
에,,? 분명히 Not a Number 라고 하였는데 넘버라고 한다. 
사실 NaN은 숫자가 아니라는 뜻 보다는 유효하지 않은 숫자라고 하는 것이 정확한 표현이다. 

그리고 NaN은 Sentinel Value의 하나로 숫자집합 내에서 특정한 종류의 에러를 나타낸다 NaN은 또한 특이한 성질을 가지는데

```js
let a=2-"a";
a==NaN;//false
a===NaN;//false
```
NaN은 반사성이 없는 유일한 값이다. 그렇기 때문에 따로 다음과 같이 NaN을 확인 할 수 있는 함수가 있다. 

```js
let a=2-"a";
isNaN(a)//true
```
단 이 NaN은 말 그대로 숫자가 아닌 것임을 판별해주는 함수임으로 

```js
let a=1+"a";
let b="ad";

window.isNaN(a)//true;
window.isNaN(b)//true
```
다음과 같은 문제가 있었다. 하지만 ES6부터 Number.isNaN() 이 생기며 이러한 문제가 해결되었다. 

폴리필:
```js
if(!Number.isNaN){
    Number.isNaN=function(n){
        return n!==n;
    }
}//NaN이 유일한 반사성이 없는 값이기 때문에 가능하다.
```

## 무한대 
자바스크립트는 타 언어와 다르게 무한대로 나누었을 때, 에러를 발생시키는 것이 아니라 Number.POSITIVE_INFINITY 라는 값이 나온다. 
```js
100/0
Infinity
-100/0
-Infinity
```

자바스크립트는 부동소수점 표기법을 사용하기 때문에 맨 앞에 부호비트가 있어 0값이나 무한 값이 -가 될 수 있다. 
## 0
그리고 JS 에서 무한대를 무한으로 나누면 NaN이 된다. 
유한한 양수를 무한으로 나누면? :0 
유한한 음수를 무한대로 나누면:-0 
앞에서 말했듯이 부동소수점 표기법에는 부호비트가 있기 때문이다. 
```js
0*-10
//-0
a.toString()
//"0"
//하지만 문자열로는 그냥 0이다. 
//반대로 하면 다시 -0으로 보여준다. 

Number("-0")//-0


let a=0;
let b=0/-3;

a==b;//true;
-0==0;//true
a===b;//true;
-0===0;//true;
0>-0//true;
a>b;//false;
//만약 0과 -0을 구분하고 싶다면 
function isNegZero(n){
    n=Number(n);
    return (n===0)&&(1/n===-Infinity);
}
```
이렇게 부호를 유지해서 좋은 점은 에니메이션 등 방향을 나타낼 때 0이어도 이전까지의 방향데이터를 유지할 수 있다는 점이다. 

## Object.is(a,b)
양 값이 절대적으로 같은지 확인 NaN도 확인 가능 -0과 0도 구분 가능하다






