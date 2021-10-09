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
