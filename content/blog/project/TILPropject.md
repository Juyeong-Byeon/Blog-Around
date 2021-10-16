---
title: "TIL"
date: "20211007"
description: "매일 매일 배운 내용들을 정리해보자."
---

### 20211007

1.  div등 focus가 일어나지 않는 요소에 focus를 하고 싶다면?
    음의 정숫값(보통 tabindex="-1")은 연속 키보드 탐색으로 접근할 수는 없으나 JavaScript나 시각적(마우스 클릭)으로는 포커스 가능함을 뜻함.
    ```html
    <div tabidx="-1"></div>
    ```
2.  이미지를 로드해서 바독판 형태로 맵핑 하기  
    canvas를 이용해 이미지를 매핑할 일이 있었다.
    대강 아래 같은느낌.

    ```ts

        function async imageFilter(ctx:CanvasRenderingContext2D,imageSrc:string):Promise<CanvasRenderingContext2D>{


            const loadedImage=await (
                //https://stackoverflow.com/questions/46399223/async-await-in-image-loading
                function (src){
                    new Promise((resolve, reject) => {
                        let img = new Image()
                        img.onload = () => resolve(img.height)
                        img.onerror = reject
                        img.src = src
                })
            )(imageSrc);

            //https://developer.mozilla.org/ko/docs/Web/API/Canvas_API/Tutorial/Using_images
            for (let i = 0; i < 4; i++) {
                for (let j = 0; j < 3; j++) {
                    ctx.drawImage(loadedImage, j * 50, i * 38, 50, 38);
                }
            }

        return ctx;
    }
    ```

### 20211012

Nominal type system vs Structural type system

typescript는 Structural type system을 사용한다.

Structural type system은 구조가 같으면 타입이 같은 것으로 판단한다.
c#, java 같은 경우는 구조가 같더라도 이름이나, 타입의 관계를 정의해주지 않으면 폴리모피즘 등을 구현할 수 없다.
즉 ts는 Structural type system을 사용하기 때문에 아래의 코드가 가능한 것이다.

```ts
interface Point {
  x: number
  y: number
}

const obj = {
  x: 10,
  y: 10,
  name: "point_x",
}

printPoint(obj)

function printPoint(point: Point): void {
  console.log(point)
}
```

### 20211013

1. jest

jest를 이용해 특정 UI를 권한에따라 보여줄지, 보여주지 않을지를 잘 체크하고 있는지 로직을 점검할 일이 었었다.
그 과정에서 아래 두 함수의 차이를 알아보았다.

```ts
expect().toBe() vs expect().toEqual()
```

위 둘의 차이점은 toBe는 reference 비교이고 toEqual은 deep하게 들어가 값을 비교한다는 것이다.
즉

```ts
const inputArr = ["A", "B", "C"]

//...some code

expect(inputArr).toEqual(["A", "B", "C"]) // Pass
expect(inputArr).toEqual(["A", "B", "C"]) // Fail
```

2. scroll effect

apple의 사이트에서 scroll위치에 따라서 에니메이션이 재생되는 이펙트가 있었는데 너무 멋있어보여서 직접 구현해보려고 했는데, 생각보다 성능이 좋지 않았다.
그런데 개발 없이 이렇게 만들 수 도 있는듯 (apple과 같은 기술)

https://www.youtube.com/watch?v=hY1a94niwpY

### 20211016

## duck typing

타입 체계중 하나로 행동이 같다면 같은 타입으로 봐도 무방하다는 타이핑 방식이다.
이는 클래스, 인터페이스로 타입을 구별하는 것과 다르게 메서드, 프로퍼티의 존제 유무를 통해 타입을 구분한다.
이는 동적 타입 언어에서 나타나는 특성이다. 대표적으로 아래와 같이 JS가 있다.

https://stackoverflow.com/questions/3379529/duck-typing-in-javascript

```js
const duck1 = {
  speak: function () {
    console.log("꽥꽥")
  },
}

const duck2 = {
  speak: function () {
    console.log("꿕꿕")
  },
}

function makeDuckSpeak(duck) {
  duck.speak()
}

makeDuckSpeak(duck1) //'꽥꽥' 같은 오리타입
makeDuckSpeak(duck2) //'꿕꿕' 같은 오리타입
```

## duck typing vs structural typing

며칠 전에 봤던 duck typing과 매우 유사한 것 같아 차이점을 알아봤는데,
duck typing은 동적으로 타입바인딩이 일어나는 언어적 특성에 의해서 일어나는 것이고,
structural은 구조가 같으면 같은 타입으로 친다는 것에서는 동일하지만 structural typing은 정적으로 결정된 타입을 체크해서
컴파일 타임에 타입 에러를 찾아 내고, duck typing은 이를 런타임전에는 모른 다는 것이 차이다.

https://softwareengineering.stackexchange.com/questions/259943/is-there-a-difference-between-duck-typing-and-structural-typing
