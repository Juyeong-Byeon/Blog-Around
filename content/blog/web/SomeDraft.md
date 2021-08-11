---
title: "ui 라이브러리의 컴포넌트를 구현하다 생긴 고민."
date: "20210811"
description: "컴포넌트 받기 vs callback 받기"
---

## dooboo-ui
최근 [오픈소스 컨트리뷰톤](https://www.oss.kr/notice/show/d7db473a-f57b-4d5e-be88-c0752f1719ec?page=7) 이라는 프로그램에 참여해서 멘토링을 받으며 [dooboo-ui](https://github.com/dooboolab/dooboo-ui)에 기여할 기회가 생겼다. 

[dooboo-ui](https://github.com/dooboolab/dooboo-ui)는 React-Native 기반의 ui 컴포넌트 라이브러리 프로젝트로서 React-Native UI 컴포넌트들을 공용으로 정의해두고 쉽게 이용할 수 있도록 한다. 내부 스택은  emotion, typescript, jest, react-testing-library, expo, storybook, 등을 이용하고, dooboolab 에서 주로 관리하고있는 오픈소스 라이브러리이다.

## First Issue

가장 먼저 dooboo-ui 프로젝트를 fork하고 로컬 환경을 set 한 뒤 storybook으로 구성된 모바일 preview를 둘러보던 중 checkbox의 반응형이 조금 이상한 것 같아 이를 수정해 PR을 날렸고 바로 반영이 되었다. [dooboo-ui 첫 PR](https://github.com/dooboolab/dooboo-ui/pull/61)

물론 회사에서 매일 개발을 하고 있긴 했지만 회사 밖의 개발자들과 소통하고 기여할 수 있다니.. 새로운 성취감을 느꼈다.

## First Issue Report

첫 이슈를 발견해서 해결한 후 자신감이 붙어 조금 더 적극적으로 이슈를 물색하고 구현하려고 했다. 그러던 중 일반적으로 모바일 환경에서 많이 사용하는 Floating Action Button (FAB)가 없는 것을 보고 이를 구현하는 것을 제안했다. [dooboo-ui 첫 Issue Report](https://github.com/dooboolab/dooboo-ui/issues/63)

그리고 내 바램대로 바로 할당을 받고 구현을 시작했다. 
구현을 하던 중 고민이 생긴 부분은 아래와 같다. 

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