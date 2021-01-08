---
title: JS - es6문법 new Map()
date: "20201008"
description: "key: value의 형태의 자료구조인 Map을 알아보자!"
---

# Map

&ensp;회사에서 작업을 하다가 key:value의 형태의 자료구조가 필요했다. 물론 object도 key:value 형태의 자료구조지만 key를 string과 Symbol 만으로 설정할 수 있다는 한계가 있어서 새로운 자료구조를 찾다가 Map을 알게 되어 정리해보게 되었다. 



## 1. Map 
&ensp;Map 객체는 키-값 쌍을 저장하고 각 쌍의 삽입 순서도 기억하는 콜랙션이라고 한다. 그리고 키로는 모든 종류의 타입이 가능하다고 한다. 

### 키 동일성

&ensp;Map을 사용하는 이유는 아마 원하는 key-value를 등록 해두고 원하는 key를 통해 쉽고, 효율적으로 value를 찾아내기 원해서 일것이다. 만약 들어온 key가 원래 있는 키와 동일한지 알아내는 규칙을 잘 알지 못한다면 key를 이용해 value를 찾아내는 핵심을 '느낌'이나 '예상'에 기반해서 코딩을 하게 될것이다. 그렇기 때문에 한번 정리해보자. MDN에 따르면 Map이 키 동일성을 판단하는 기준은 다음과 같다고 한다.

    1. 키 동일성은 sameValueZero 알고리즘에 기반합니다.
    2. NaN !== NaN이지만, 그럼에도 NaN은 NaN과 일치한다고 간주하며, 다른 모든 값은 === 연산자의 결과를 따릅니다.
    3. 현 ECMAScript 명세는 -0과 +0을 같은 값으로 처리하지만 초기 명세에서는 그렇지 않았습니다. 브라우저 호환성의 "Key equality for -0 and 0"을 참고하세요.


&ensp; 1번은 무슨 의미일까? js 정확히는 es6에서 동등을 비교하는 알고리즘은 4가지가 있다. 아래의 기준 중 c로 

    a. 추상적(abstract) 같음 비교 (==) 
    b. 엄격한(strict) 같음 비교 (===): Array.prototype.indexOf, Array.prototype.lastIndexOf 및 case 절 매칭에 쓰임
    c. 등가0(SameValueZero): Map 및 Set 연산뿐만 아니라 %TypedArray% 및 ArrayBuffer 생성자, 그리고 ES2016에 예정된 String.prototype.includes에 쓰임
    d. 등가(SameValue): 그 외 모든 곳에 쓰임

&ensp;MDN의 설명에는 d와 같지만 -0과 0을 같다고 판단한 다고 설명하고 있다. SameValue 알고리즘이 두 값이 같다고 판단하는 규칙은 아래와 같다.

    * 둘 다 undefined
    * 둘 다 null
    * 둘 다 true 또는 둘 다 false
    * 둘 다 같은 문자에 같은 길이인 문자열
    * 둘 다 같은 객체
    * 둘 다 숫자이며
    * 둘 다 +0
    * 둘 다 -0
    * 둘 다 NaN
    * 둘 다 0이나 NaN이 아니고 같은 값을 지님

&ensp; 2번은 무슨 의미일까? 원래 NaN은 js에서 어떠한 값과도, 심지어 자신과도 같지 않은 값이다. 하지만 위에서 Map의 특성을 볼 때 어떠한 타입이라도 key로 사용이 가능하다고 했음을 충족 시키기 위해 NaN은 NaN과 같이 보도록 정의 해둔 것 같다.

```js
NaN===NaN//false
NaN==NaN//false
```

&ensp; 3번은 무슨 의미일까? 말 그대로이다. js는 숫자를 IEEE 754 부동소수점 표기 방식으로 표기 하는데, 이 표기 방식은 부호가 있는 표기 방식이기 때문에 -0과 +0이 생긴다. 그래서 이를 기존에는 다르게 처리했지만 현 명세에서는 같게 처리한다는 것을 설명하고 있는 것이다.

```js
-0===0//true
-0===+0//true
+0===0//true
```
### Map의 method와 property
&ensp;이제 본론으로 들어가서 Map의 사용법을 살펴보자.

1. Map.prototype.set(key, value)
    * Map 객체에 주어진 키(Key)에 값(Value)를 집어넣고, Map 객체를 반환한다.

```js
let foo=new Map();
foo.set('1',1);//Map(1) {"1"=>1}
foo.set({},2);//Map(2) {"1"=>,{...}=>2} 
foo.set(NaN,99);//Map(3){"1"=>,{...}=>2,NaN=>99} ? 놀랍게도 NaN도 key로 사용할 수 있다!
```    
Map.set() 메서드는  첫 번 째 인자로 key를 받고, 두 번째 인자로 value를 받아서 Map에 저장을 하고 현재 상태의 Map객체를 반환한다. 

2. Map.prototype.delete(key)
    * 주어진 키(Key)와 해당되는 값(Value)를 제거하고 제거하기 전에 Map.prototype.has(key)가 반환했던 값을 반환한다. 그 후의 Map.prototype.has(key)는 false를 반환한다. 즉 값이 map에 존제해 성공적으로 값을 지웠다면 true, 지우지 못했다면 false를 반환한다.
```js
let foo=new Map();
foo.set('1',1);
foo.set({},2);
foo.set(NaN,99);
foo.delete(NaN);//true
foo.delete(-1);//false
```
3. Map.prototype.entries()
    * Map 객체 안의 모든 요소들을 [key, value] 형태의 array 로 집어넣은 순서대로 가지고 있는 Iterator 객체를 반환한다.

```js
let foo = new Map();
foo.set('1', 1);
foo.set({}, 2);
foo.set(NaN, 99);
console.log(foo.entries()); 
MapIterator {"1" => 1, {…} => 2, NaN => 99}
```

4. Map.prototype.forEach(callbackFn[, thisArg])
    * Map 객체 안에 존재하는 각각의 key/value pair에 집어넣은 순서대로 callbackFn을 부른다. 만약 thisArg 매개변수가 제공되면, 이것이 각 callback의 this 값으로 사용된다.

    ```js
    let foo = new Map();
    foo.set('1', 1);
    foo.set({}, 2);
    foo.set(NaN, 99);
    foo.forEach((value, key, map) => {
        console.log('key: ' + key + '  value: ' + value);
    })
    /*
    key: 1  value: 1
    key: [object Object]  value: 2
    key: NaN  value: 99
    */
    ```

5. Map.prototype.get(key)
    * 주어진 키(Key)에 해당되는 값(value)을 반환하고, 만약 없으면 undefined를 반환한다.
  ```js
    let foo = new Map();
    foo.set('1', 1);
    foo.set({}, 2);
    foo.set(NaN, 99);
    foo.get('1'); //1
    foo.get({});//2
    foo.get(NaN);//99
    foo.get("123");//undefined

    ```


6. Map.prototype.has(key)
    * Map 객체 안에 주어진 key/value pair가 있는지 검사하고 Boolean 값을 반환한다.
    ```js
    let foo = new Map();
    foo.set('1', 1);
    foo.set({}, 2);
    foo.set(NaN, 99);
    foo.has('1');//true
    foo.has(',');//false    
    ```

7. Map.prototype.keys()
    * Map 객체 안의 모든 키(Key)들을 집어넣은 순서대로 가지고 있는 Iterator 객체를 반환한다.

    ```js
    let myMap = new Map();
    myMap.set('0', 'foo');
    myMap.set(1, 'bar');
    myMap.set({}, 'baz');

    let mapIter = myMap.keys();

    console.log(mapIter.next().value); // "0"
    console.log(mapIter.next().value); // 1
    console.log(mapIter.next().value); // Object
    ```

8. Map.prototype.clear()
    * Map 객체의 모든 key/value pair를 제거한다.
    ```js
    let foo = new Map();
    foo.set('1', 1);
    foo.set({}, 2);
    foo.set(NaN, 99);
    foo.clear();//undefined
    console.log(foo);//Map(0) {}
    ```

9. Map.prototype.values()
    * Map 객체 안의 모든 값(Value)들을 집어넣은 순서대로 가지고 있는 Iterator 객체를 반환한다.
    ```js
    let myMap = new Map();
    myMap.set('0', 'foo');
    myMap.set(1, 'bar');
    myMap.set({}, 'baz');
    let mapIter = myMap.values();
    console.log(mapIter.next().value); // "foo"
    console.log(mapIter.next().value); // "bar"
    console.log(mapIter.next().value); // "baz"
    ```

10. Map.prototype[@@iterator]()
    * Map 객체 안의 모든 요소들을 [key, value] 형태의 array 로 집어넣은 순서대로 가지고 있는 Iterator 객체를 반환한다.
    ```js
    const map1 = new Map();
    map1.set('0', 'foo');
    map1.set(1, 'bar');
    const iterator1 = map1[Symbol.iterator]();
    for (const item of iterator1) {
    console.log(item);
    }
    // expected output: Array ["0", "foo"]
    // expected output: Array [1, "bar"]
    ```


### Object VS Map 

1. Map은 모든 타입을 key의 값으로 사용 가능하지만 obj는 string 타입 만으로 할 수 있다. 
2. Map의 원소 개수는 size 프로퍼티로 쉽게 알아낼 수 있지만 obj는 직접 추적 해야 한다. 
3. 잦은 key-value 쌍의 추가와 제거에 Map의 성능이 최적화 되어있다.


## 참고

* [MDN Map](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Map)
* [MDN 동일 비교 기준](https://developer.mozilla.org/ko/docs/Web/JavaScript/Equality_comparisons_and_sameness#%EB%93%B1%EA%B0%800_%EA%B0%99%EC%9D%8C)

