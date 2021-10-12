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
