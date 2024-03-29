---
title: "읽어볼 글들."
date: "20210913"
description: "읽어봐야지 읽어봐야지 하다가 항상 까먹는 것 같아 쓰는 읽어볼 글 목록"
---

1. [Pitfalls of overusing React Context](https://blog.logrocket.com/pitfalls-of-overusing-react-context/)
   소감:

2. [구글 코드리뷰 가이드](https://google.github.io/eng-practices/)
   소감:

3. [React Hook의 어두운면](https://ui.toast.com/weekly-pick/ko_20200922)

소감: 최근에 hooks를 도입할지 말지에 대한 논의와 고민이 있었다. 그 과정에서 react hook의 장점들을 찾아보고, 사용하면서의 이점에 초점을 맞춰서 봤다. 장점은
다음과 같은 것 들이 있었던 것 같다.

### [장점](https://ko.reactjs.org/docs/hooks-intro.html)

1. view와 로직의 분리가 확실하게 된다.
   - 때문에 view, 로직 각각을 재사용하기가 수월하다.
2. custom hook을 이용함으로써 sideEffect, state를 처리 하는 로직이 hook 내부로 들어가기 때문에 view 코드가 깔끔해진다.
3. hook을 다른 hook들과 조합함으로서 새로운 로직을 만들어내기도 수월하다.
4. 작은 컴포넌트의 경우 보일러 플레이트 코드가 줄어든다.
5. class에서 해주던 this 바인딩을 일일히 해줄 필요가 없다.
6. useEffect를 이용하면 물리적으로 가까운 위치에서 side effect를 처리하고 cleanup 해줄 수 있다.
7. 힙하다.

### [단점](https://ui.toast.com/weekly-pick/ko_20200922)

1. sideEffect를 처리하는 로직들이 hooks내부로 숨어있기 때문에 오히려 유지보수가 힘들다. (일부 동의)
2. useEffect는 라이프 사이클과 별개로 돌아가고, 어떻게 동작하는지 따로 학습해야 한다. (일부 동의)
3. useEffect가 디펜던시에 의해 실행되는 시점을 예측하기 힘들다. (동의)
4. class의 경우 this 등을 이용하면 되는데 this 등을 이용하지 못해서 useRef 등을 이용해야 한다.
5. class는 모든 개발자들이 알고 있는 통용되는 개념이지만 useRef, useEffect, useCallback, useMemo 등의 hook이 작동하는 방식을 이해하려면 별도로 리액트만을 위한 학습을 해야한다.
6. 한 컴포넌트 내에서 로직 분리가 쉽지 않고 가독이 좋지 않다. (메서드 불가)

총평 > 커스텀 훅스가 로직을 view로 부터 분리해주고, 이를 숨겨줌으로써 view코드를 우아하게 해주지만 양날의 검으로 분석을 어렵게 하기도 하는 것 같다.
또한 확실히 컴포넌트 내에서의 로직 분리, this 사용 가능 등 class 컴포넌트만에도 있는 장점들이 있는 것 같다. 하지만 그럼에도 hook의 손을 들어주고 싶은 이유는 다음과 같다.

1.  일단 view가 간단해지기 때문에 해당 컴포넌트가 어떤 모양을 하고 있는지를 파악하기가 쉬워지는 것 같다.
2.  이전엔 로직을 HOC등을 이용해서 재사용했어야 했는데 이 과정이 번거롭고 모양도 우아하지 않았던 것 같다. (HOC 는 컴포지션이 상위로 되는 느낌이라면 hook은 수평적으로 더해지는 느낌)
3.  view와 로직이 확실히 분리 되어서 view는 hook에서 주는 인터페이스만 신경쓰면 되고, hook에서는 상태, sideEffect에만 신경쓰면 된다.
