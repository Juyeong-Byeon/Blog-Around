---
title: "closure"
date: "20210922"
description: "js에서 잘 사용하는 함수적 특성 closure를 알아보고 closure를 이용했을 때의 장점을 알아보자."
---

클로저는 "자기 내부 환경과 자신을 감싸고 있는 outerLexicalEnvironmentRef를 참조해 identifier를 참조할 수 있도록 하는" js 함수적 특성을 말한다.

클로저는 이렇게 생겼다.
```js
function outer(){//nested에겐 외부 lexicalEnvironment. 
    let out=0;
    function nested(){// 내부 환경
        let nest=10;
        console.log(out+nest);
    }
}

outer()()//10;
```

해당 클로저가 어떤 순서로 생성되는지 정리해보면 아래와 같다. 

1. outer가 호출 될 때 outer의 lexical환경 정보를 저장하는 컴포넌트가 실행문맥에 생성된다.
2. nested의 함수 선언문을 evaluation해서 함수 객체를 생성한다. 이 함수 객체의 렉시컬 환경 컴포넌트에는 자신인 nested의 코드와 함수 outer의 outerLexicalEnvironmentRef가 저장된다.
3. 함수 nested를 호출해 실행하면 그 시점에 함수 nested의 lexical환경을 저장하는 컴포넌트가 생성된다. 이와 동시에 outerLexicalEnvironmentRef를 참조해 out 변수의 참조를 구해온다

이 과정을 거쳐 클로저가 생성된다.
정리하면 nested의 환경과 nested가 outerLexicalEnvironmentRef로 참조 할 수 있는 환경의 집합이 closure이다.

```js
ExecutionContext={
    LexicalEnvironment:{
        EnvironmentRecord:{// 자기 자신의 환경과
        },
        OuterLexicalEnvironmentRef:{//자신을 감싸고 있는 환경의 lexical 환경을 chain처럼 참조할 수 있는 것이 closure
        }
    }
}

```

이 때 nested가 outer의 out 변수를 참조하고 있기 때문에 outer는 GC대상이 되지 않는다. 

## Closure를 이용할때의 장점 

closure라는 js의 함수적 특성을 이용함으로써 얻을 수 있는 이점은 크게 두가지가 있는 것 같다. 

1. 함수의 내부 변수를 외부로부터 숨긴 상태로 return하는 function들로 제한된 인터페이스를 제공해 캡슐화 할 수 있다.
2. closure 함수를 Factory형태로 사용할 경우 초기 set을 해주는 시점과 실행 시점을 분리함으로써 문법적인 유연성을 더해준다. 

1번 장점의 경우 많이 알려져있는 장점이고 closure를 배울 때 항상 책에서 소개되는 장점이다. 아래처럼
```name, age, hobby```등을 set하는 interface를 제공해주고 직접 적인 쓰기및 참조를 불가하게 할 수 있다. 
또한 ```introduceSelf```를 위에서 언급한 세 변수를 사용하는 인터페이스로 제공해 다른 용도로 사용하지 못하도록 하였다.

```js
    function People(){
        let name='';
        let age=0;
        let hobby='';
        return {
            function setName(_name){
                name=_name;
            },
            function setAge(_age){
                age=_age;
            },
            function setHobby(_hobby){
                hobby=_hobby;
            },
            function introduceSelf(){
                console.log(`hi!, my name is ${name} and Im ${age} years old.`);
                console.log(`My hobby is ${hobby}`);
            }
        }
    }
```

하지만 요즘에는 class 문법이 제공 되고 심지어 TS를 이용하면 접근제어지시자를 제공해 이 장점은 크게 의미가 없어진 것 같다. 
물론 바닐라+(es6이전버전)을 사용하는 환경에서는 여전히 의미가 있겠지만..

그 다음으로는 2번째 장점인 "함수의 실행 시점을 늦춤으로써 문법적인 유연성을 더해준다." 에 대해서 설명해보겠다.

아래 처럼 url을 받고 이 url을 이용하는 nested함수를 반환한다고 해보자. 



```js
function fetcherFactory(baseUrl){// 기본 엔드포인트 주소를 받는다.
    function async fetchData(id){// 특정 id를 받는다.
        const res await fetch(`${baseUrl}/${id}`);
        const data=res.json();

        //...do something
        return data;
    }
}
```
이 함수는 아래와 같이 사용될 수 있을 것이다.
아래처럼 항상 어떤 end-point에서 데이터를 받아오고 싶을 때 end-point를 인수로 전달해 호출하고, 반환된 함수를 통해 id만을 다르게 넣어 다른 데이터들을 fetch할 수 있을 것이다. 

1. 함수를 factory처럼 이용해 함수의 행위를 set해주는 시점과 사용하는시점을 분리해줄 수 있고. 
2. 비동기로 id를 얻어 fetch를 실행하는 작업을 end-point를 set해주는 실행 문맥과 분리할 수 있다.


```js
const someEndPointFetcher=fetcherFactory("https://some.domain.com/endpoint");

let fetchedData={};

+async function(){
    const id=await getId();
    fetchedData=await someEndPointFetcher(id);//https://some.domain.com/endpoint/id 에서 fetch
}(fetchedData);
```

closure의 장점을 다시 한번 정리하면
1. 내부 변수들을 숨긴 상태로 interface를 제공하는 캡슐화를 제공 할 수 있다. 
2. 함수를 Factory형태로 사용할 경우 반환되는 함수의 실행 시점을 늦춰 중간에 다른 작업을 할 수 있는 유연성을 제공해준다.