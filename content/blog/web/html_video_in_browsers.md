---
title: "브라우저별 HtmlVideoElement 지원 사항 및 제약 사항."
date: "2021040410"
description: "비디오 관련 프로젝트를 하면서 정리한 브라우저별 HTMLVideoElement 지원사항, 정책 정리."
---



# HtmlVideoElement 정책 정리.

동영상은 브라우저별, 기기별 종속성이 큰 요소 타입이다. 본 필자는 회사에서 프로젝트를 진행하면서 각 기기 별로, 브라우저별로 다른 인터페이스 제공, 정책 때문에 애를 먹었다. 
때문에 추후 동영상을 다룰때 필요한 정보를 쉽게 찾기 위해 정리해본다.

## 브라우저별 지원하는 MP4 컨테이너 코덱

* 필자는 이번 프로젝트에서 지원범위를 mp4 미디어 컨테이너로만 잡았다 때문에 mp4에 대해서만 조사를 진행했다. 한 컨테이너 내에서도 여러 코덱이 있고, 각각의 코덱은 브라우저별로 지원여부가 다르기때문에 서비스의 정책을 정할때에도 이를 정리할 필요가 있어보인다.

### MP4 (MPEG4)
* 가장 먼저 알아야 할 내용은 MP4는 영상 + 오디오 컨테이너이고 내부에 들어갈 수 있는 내용물인 사운드, 비디오는 같은 컨테이너 종류 내에서도 각각 다른 형식의 코덱을 가지고 있을 수 있다.  mp4 내에서 사용하는 비디오 코덱은 아래와 같은 것들이 있는데, 각각의 브라우저별 지원여부는 아래와 같다. 



### 비디오 코덱
| codec | chrome | firefox | safari |
|:--------:|:------:|:-------:|:-----:|
|AVC (H.264)|	Yes|	Yes|	Yes|
|AV1|	Yes(Only Chrome OS, Linux, macOS, and Windows at present)	|Yes|	No|
|H.263|	?|	No|	?|
|H.265|	No|	No|	Yes|
|MPEG-4 Part 2 Visual|	No| 	No|	Yes|
|VP9	|Yes|	Yes|	No|



### 오디오 코덱

|codec|chrome|firefox|safari|
|:----:|:-----:|:-----:|:-----:|
| MP3 | yes|	Yes|	Yes|
|ACC	|yes	|Yes	|Yes|
|AC3|	No|	No|	No|





#### 관련 문서:

* https://videojs.com/html5-video-support/

* https://www.chromium.org/audio-video

* https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Containers#mp4

## 자동재생
프로젝트를 진행하며 자동재생을 해달라는 요구사항이 있었다. 처음 내가 해본 시도는 autoplay를 true로 set 하거나 video.play(); 를 호출해 비디오를 실행시키려고했다. 그러나 아래와 같은 에러메시지가 뜨면서 재생이 되지않는 경우가 있었고 이때문에 브라우저 자동재생 정책을 리서치하게 되었다. 

![autoPlay restriction](./assets/autoplay.png)

### 자동재생 제약사항 
* 동영상의 자동재생은 다음과 같은 제약사항들이 있다.

* 음소거가 되어있으면 자동재생(autoplay&&play)은 항상 가능하다.
* 음소거가 되어있지 않은 상태에서 자동재생을 하기 위해서는 아래와 같은 조건일때만 가능하다.
    * 사용자와 도메인의 인터랙션이 한번이라도 있을 때 
    * 데스크탑에서 해당 비디오를 이전에 재생한 적이 있을 때
    * 사용자가 사이트를 모바일 홈스크린이나 PWA를 설치했을때




### 관련 문서:

* https://developers.google.com/web/updates/2017/09/autoplay-policy-changes

* https://webkit.org/blog/6784/new-video-policies-for-ios/

## 동시 재생

* 대부분의 브라우저, OS는 동시재생을 지원한다. 하지만 애플의 IOS, IpadOS는 음량이 있는 경우 inline으로 여러 동영상의 동시재생을 지원하지 않는다. 제약사항은 브라우저별로, 기기별로 차이가 있는데 다음과 같다. 


### PC환경- 동시 재생 가능 여부
|상태|chrome|safari|
|:--:|:----:|:----:|
|소리 재생|	yes|	yes|
|음소거| 	 yes|	yes|

* yes: 동시재생 가능
* no: 동시재생 불가능


### 모바일 환경- 동시 재생 가능 여부 (기기 종속)

|상태|chrome|safari on ios|chrome on ios|
|:--:|:----:|:----:|:---:|
|소리 재생|	yes|	 no|	 no|
|음소거 	 |yes	 |yes	|yes|


* yes: 동시재생 가능
* no: 동시재생 불가능


확인한바 모바일 IOS ,IpadOS에서는 음량이 있는 영상을 동시재생 할 수 없다.
크롬의 경우 IOS에서 웹킷엔진을 이용하기 때문에 safari와 제약사항이 같다. 





## loadmetadata

* loadmetadata event는 비디오가 재생되기 위해 video tag에 set된 src의 메타정보를 읽어왔을 때 발생하는 이벤트이다.
* ios의 경우 video 가 DOM에 보이는 상태로 로드 되지 않으면 onloadMetadata event 가 발생하지 않는다.(display:none 불가)

* 화면에 불러오기 전에 width, height를 먼저 set 해줘야 하는 경우가 많은데 width height 를 읽어올 수 없다.
이런 경우 아래와 같은 처리가 필요하다.
```ts
const videoTag:HTMLVideoElement=document.createElement('video');
//비디오가 화면에서 안보이도록 처리
videoTag.style.width = '0';
videoTag.style.height = '0';
videoTag.style.opacity = '0';
 //실제 돔에 붙여준다.
document.body.appendChild(video);
video.addEventListener('loadmetadata',(e)=>{
//do somthing
 document.body.removeChild(video);//body에서 요소를 때준다.
})
```