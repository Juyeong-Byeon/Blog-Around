---
title: "ui 라이브러리의 컴포넌트를 구현하다 생긴 고민."
date: "20210811"
description: "컴포넌트 받기 vs callback 받기"
---

## dooboo-ui
최근 [오픈소스 컨트리뷰톤](https://www.oss.kr/notice/show/d7db473a-f57b-4d5e-be88-c0752f1719ec?page=7) 이라는 프로그램에 참여해서 멘토링을 받으며 [dooboo-ui](https://github.com/dooboolab/dooboo-ui)에 기여할 기회가 생겼다. 

[dooboo-ui](https://github.com/dooboolab/dooboo-ui)는 React-Native 기반의 ui 컴포넌트 라이브러리 프로젝트로서 React-Native UI 컴포넌트들을 공용으로 정의해두고 쉽게 이용할 수 있도록 한다. 내부 스택은  emotion, typescript, jest, react-testing-library, expo, storybook, 등을 이용하고, dooboolab 에서 주로 관리하고있는 오픈소스 라이브러리이다.


## First Issue

가장 먼저 dooboo-ui 프로젝트를 fork하고 로컬 환경을 set 한 뒤 storybook으로 구성된 모바일 preview를 둘러보던 중 checkbox의 반응형이 조금 이상한 것 같아 이를 수정해 PR을 날렸고 바로 반영이 되었다. 
[dooboo-ui 첫 PR](https://github.com/dooboolab/dooboo-ui/pull/61)

물론 회사에서 매일 개발을 하고 있긴 했지만 회사 밖의 개발자들과 소통하고 기여할 수 있다니.. 새로운 성취감을 느꼈다.

## First Issue Report

첫 이슈를 발견해서 해결한 후 자신감이 붙어 조금 더 적극적으로 이슈를 물색하고 구현하려고 했다. 그러던 중 일반적으로 모바일 환경에서 많이 사용하는 Floating Action Button (FAB)가 없는 것을 보고 이를 구현하는 것을 제안했다.그리고 가능하면 나에게 할당해서 기여 기회를 달라고 어필했다. [dooboo-ui 첫 Issue Report](https://github.com/dooboolab/dooboo-ui/issues/63)

## 구현 그리고 고민...

그리고 내 바램대로 바로 할당을 받고 구현을 시작했는데,
구현을 시작한지 얼마 되지않아 바로 고민에 빠졌다. 사실 이번은 두번째 FAB 구현인데 첫 구현은 props로 아이콘과 callback을 받고 이를이용해 내부에서 ButtonView들을 만들어 주는 구조로 구현을 했었다. 이렇게 개발을 했을때는 내부에서 ButtonView를 만들어주기때문에 버튼 뷰들의 스타일을 일관적으로 가져갈 수 잇다는 장점이 있고, 해당 컴포넌트를 사용하는방법을 제한함으로써 정확히 FAB의 용도로만 사용할 수 있도록 강제할 수 있다는 장점이 있었다. 반대로 사용방법을 제한함으로써 유연성을 저해한다는 단점은 있는 것 같다. 

```TS
export const FloatingActionButtonImpure: FC<FloatingActionButtonImpureProps> =
  enhance<FloatingActionButtonImpureProps>(() => {
    const [isActive, setIsActive] = useState(false)

    const onClickFloatingButton = useCallback(() => {
      setIsActive((prev) => !prev)
    }, [])

    const menus = useMemo(
      () => [
        {
          icon: <PlaylistPlay />,
          onClick: () => {
            //
          },
        },
        {
          icon: <Settings />,
          onClick: () => {
            //
          },
        },
        {
          icon: <Information />,
          onClick: () => {
            //
          },
        },
      ],
      []
    )

    return (
      <x.div>
        <FloatingActionButtonPure
          icon={isActive ? <Minus /> : <Add />}
          onClick={onClickFloatingButton}
          active={true}
          index={-1}
        />
        {menus.map((menu, idx) => (
          <FloatingActionButtonPure
            key={idx}
            icon={menu.icon}
            onClick={menu.onClick}
            active={isActive}
            index={idx}
          />
        ))}
      </x.div>
    )
  })(FloatingActionButtonFallback)
```

 이번에는 라이브러리이기 때문에 유연성을 더 주어야 한다고 생각해서 아래와 같이 컴포넌트 자체를 받도록 하고, callback, 등은 외부에서 알아서 넘겨주는 컴포넌트에 바인딩을 하도록 의도해서 첫 커밋을했다. 이렇게 하니 유연성은 늘어난 것 같은데, 사용자가 어떤 컴포넌트를 넘겨줄지도 확실하지 않기때문에 스타일의 통일성이나 버튼 자체의 사용용도를 완전히 다르게 사용할 수 있을 것 같다는 생각이 든다. 

 일단 아래 방향으로 구현을 진행하다가 PR을 날려서 구현 방향에 대해서 다른 컨트리뷰터들과 maintainer에게 질문해봐야겠다. 

```TS
import {DoobooTheme, light, withTheme} from '../theme';
import React, {FC} from 'react';

import {ViewStyle} from 'react-native';
import styled from '@emotion/native';

const FixedPositionWrapperView = styled.View`
  position: fixed;
  top: ${(props: ViewStyle) => props.top};
  bottom: ${(props: ViewStyle) => props.bottom};
  left: ${(props: ViewStyle) => props.left};
  right: ${(props: ViewStyle) => props.bottom};
`;

const ExpendWrapperView = styled.View``;

interface FloatingActionButtonWrapperProps {
  active: boolean;
  DefaultMainActionButton: React.ReactElement;
  ActiveMainActionButton: React.ReactElement;
  ActionButtons: Array<React.ReactElement>;
  top?: number;
  bottom?: number;
  left?: number;
  right?: number;
}

const FloatingActionButtonWrapper: FC<
  FloatingActionButtonWrapperProps & {theme: DoobooTheme}
> = ({
  active,
  DefaultMainActionButton,
  ActiveMainActionButton,
  ActionButtons,
  top,
  bottom,
  left,
  right,
}) => {
  return (
    <FixedPositionWrapperView style={{top, bottom, left, right}}>
      {active ? ActiveMainActionButton : DefaultMainActionButton}
      <ExpendWrapperView>
        {ActionButtons.map((ActionButton) => ActionButton)}
      </ExpendWrapperView>
    </FixedPositionWrapperView>
  );
};

FloatingActionButtonWrapper.defaultProps = {theme: light};

export const IconButton = withTheme(FloatingActionButtonWrapper);

```


## 20210814 -추가 아이디어

저번 금요일에 dooboolab에 직접 가서 메인테이너와, 그리고 컨트리뷰톤 참가자들과 함께 위 내용에 대해서 논의해볼 시간이 생겼다. 
위 내용에 대해서 고민을 해본 후 얻게된 고찰은 위의 두 장점을 합쳐서 하나의 컴포넌트를 제공할 수 있다는 점이었다. 아래처럼

```TSX



export type FABItem = {
  icon: IconName;
  onPress: () => void;
};

interface FloatingActionButtonsWrapperProps {
  active: boolean;
  DefaultFAB: FABItem;
  ActiveFAB: FABItem;
  FABList: FABItem[];
  renderDefaultFAB?: (item: FABItem) => React.ReactElement;
  renderActiveFAB?: (item: FABItem) => React.ReactElement;
  renderFABListItem?: (item: FABItem, idx: number) => React.ReactElement;
}

const FloatingActionButtons: FC<FloatingActionButtonsWrapperProps> = ({
  active,
  DefaultFAB,
  ActiveFAB,
  FABList,
  renderDefaultFAB,
  renderActiveFAB,
  renderFABListItem,
}) => {
  const defaultFAB: React.ReactElement = useMemo(
    () =>
      renderDefaultFAB ? (
        renderDefaultFAB(DefaultFAB)
      ) : (
        <IconButton
          icon={<StyledIcon size={24} name={DefaultFAB.icon} />}
          onPress={DefaultFAB.onPress}
        />
      ),
    [renderDefaultFAB, DefaultFAB],
  );

  const activeFAB: React.ReactElement = useMemo(
    () =>
      renderActiveFAB ? (
        renderActiveFAB(ActiveFAB)
      ) : (
        <IconButton
          icon={<StyledIcon size={24} name={ActiveFAB.icon} />}
          onPress={ActiveFAB.onPress}
        />
      ),
    [renderActiveFAB, ActiveFAB],
  );

  return (
    <AbsolutePositionWrapperView>
      <Animated.View>
        {active &&
          FABList.map((item, idx) =>
            renderFABListItem ? (
              <FABItemWrapperView key={item.icon + idx}>
                {renderFABListItem(item, idx)}
              </FABItemWrapperView>
            ) : (
              <FABItemWrapperView key={item.icon + idx}>
                {
                  <IconButton
                    icon={
                      <StyledIcon theme={theme} size={24} name={item.icon} />
                    }
                    onPress={item.onPress}
                  />
                }
              </FABItemWrapperView>
            ),
          )}
      </Animated.View>
      <FABItemWrapperView>{active ? defaultFAB : activeFAB}</FABItemWrapperView>
    </AbsolutePositionWrapperView>
  );
};

export const FAB = withTheme(FloatingActionButtons);
```