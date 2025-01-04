# Table of Contents

- [커스텀 링크](#커스텀-링크)
  - [크로스 커팅 관심사를 위한 `createLink`](#크로스-커팅-관심사를-위한-createlink)
    - [기본 예제](#기본-예제)
  - [`createLink` with third party libraries](#createlink-with-third-party-libraries)
    - [React Aria Components 예제](#react-aria-components-예제)
    - [Chakra UI 예제](#chakra-ui-예제)
    - [MUI 예제](#mui-예제)
    - [Mantine 예제](#mantine-예제)

# 커스텀 링크

여러분은 많은 상황에서 반복적인 작업을 해도 괜찮다는 것을 알고 있을 겁니다. 하지만 너무 자주 반복하게 되면 불편함을 느낄 수 있습니다. 때로는 추가적인 동작이나 스타일을 가진 공통 컴포넌트를 만들고 싶을 수도 있습니다. 또는 TanStack Router의 타입 안전성과 함께 서드파티 라이브러리를 사용하는 것을 고려할 수도 있습니다.


## 크로스 커팅 관심사를 위한 `createLink`

`createLink`는 `Link`와 동일한 타입 파라미터를 가진 커스텀 `Link` 컴포넌트를 생성합니다. 이를 통해 여러분은 `Link`와 동일한 타입 안전성과 타입스크립트 성능을 제공하는 자신만의 컴포넌트를 만들 수 있습니다.


### 기본 예제

여러분이 기본적인 커스텀 링크 컴포넌트를 만들고 싶다면, 다음과 같이 할 수 있습니다:

```tsx
import * as React from 'react'
import { createLink, LinkComponent } from '@tanstack/react-router'

interface BasicLinkProps extends React.AnchorHTMLAttributes {
  // 앵커 엘리먼트에 전달할 추가 props를 여기에 정의합니다
}

const BasicLinkComponent = React.forwardRef(
  (props, ref) => {
    return (
      // 여기에 링크 컴포넌트의 내용을 작성합니다
    )
  },
)

const CreatedLinkComponent = createLink(BasicLinkComponent)

export const CustomLink: LinkComponent = (props) => {
  return (
    // 커스텀 링크 컴포넌트의 내용을 작성합니다
  )
}
```

이제 새로 만든 `Link` 컴포넌트를 다른 `Link` 컴포넌트처럼 사용할 수 있습니다.

```tsx
// 사용 예제
```

이 예제는 커스텀 링크 컴포넌트를 만드는 기본적인 방법을 보여줍니다. `BasicLinkComponent`를 정의하고, `createLink` 함수를 사용해 링크 컴포넌트를 생성한 후, `CustomLink`로 내보내는 과정을 담고 있습니다.


## `createLink` with third party libraries





### React Aria Components 예제

React Aria Components의 [Link](https://react-spectrum.adobe.com/react-aria/Link.html) 컴포넌트는 표준 `onMouseEnter`와 `onMouseLeave` 이벤트를 지원하지 않습니다. 따라서 TanStack Router의 `preload (intent)` prop과 직접 사용할 수 없습니다.

이에 대한 설명은 다음 링크에서 확인할 수 있습니다:

- [https://react-spectrum.adobe.com/react-aria/interactions.html](https://react-spectrum.adobe.com/react-aria/interactions.html)
- [https://react-spectrum.adobe.com/blog/building-a-button-part-2.html](https://react-spectrum.adobe.com/blog/building-a-button-part-2.html)

이 문제를 해결하기 위해 [React Aria Hooks](https://react-spectrum.adobe.com/react-aria/hooks.html)의 [useLink](https://react-spectrum.adobe.com/react-aria/useLink.html) 훅을 사용하여 표준 앵커 엘리먼트와 함께 사용할 수 있습니다.

```tsx
import * as React from 'react'
import { createLink, LinkComponent } from '@tanstack/react-router'
import {
  mergeProps,
  useFocusRing,
  useHover,
  useLink,
  useObjectRef,
} from 'react-aria'
import type { AriaLinkOptions } from 'react-aria'

interface RACLinkProps extends Omit {
  children?: React.ReactNode
}

const RACLinkComponent = React.forwardRef(
  (props, forwardedRef) => {
    const ref = useObjectRef(forwardedRef)

    const { isPressed, linkProps } = useLink(props, ref)
    const { isHovered, hoverProps } = useHover(props)
    const { isFocusVisible, isFocused, focusProps } = useFocusRing(props)

    return (
      <a
        {...mergeProps(linkProps, hoverProps, focusProps, props)}
        ref={ref}
        data-hovered={isHovered || undefined}
        data-pressed={isPressed || undefined}
        data-focus-visible={isFocusVisible || undefined}
        data-focused={isFocused || undefined}
      />
    )
  },
)

const CreatedLinkComponent = createLink(RACLinkComponent)

export const CustomLink: LinkComponent = (props) => {
  return 
}
```

이 예제에서는 `useLink`, `useHover`, `useFocusRing` 훅을 사용하여 커스텀 링크 컴포넌트를 만들고, 이를 TanStack Router와 통합하는 방법을 보여줍니다.


### Chakra UI 예제

```tsx
import * as React from 'react'
import { createLink, LinkComponent } from '@tanstack/react-router'
import { Link } from '@chakra-ui/react'

interface ChakraLinkProps
  extends Omit, 'href'> {
  // 링크에 전달할 추가적인 props를 여기에 정의
}

const ChakraLinkComponent = React.forwardRef<
  HTMLAnchorElement,
  ChakraLinkProps
>((props, ref) => {
  return 
})

const CreatedLinkComponent = createLink(ChakraLinkComponent)

export const CustomLink: LinkComponent = (
  props,
) => {
  return (
    <CreatedLinkComponent
      textDecoration={'underline'}
      _hover={{ textDecoration: 'none' }}
      _focus={{ textDecoration: 'none' }}
      preload={'intent'}
      {...props}
    />
  )
}
```

이 예제는 Chakra UI와 React Router를 함께 사용하는 방법을 보여줍니다. `ChakraLinkComponent`는 `React.forwardRef`를 사용하여 정의되며, `createLink` 함수를 통해 커스텀 링크 컴포넌트를 생성합니다. `CustomLink` 컴포넌트는 `textDecoration`, `_hover`, `_focus` 등의 스타일을 적용하고, `preload` 속성을 통해 링크를 미리 로드할 수 있습니다.


### MUI 예제

```tsx
import * as React from 'react'
import { createLink, LinkComponent } from '@tanstack/react-router'
import { Button, ButtonProps } from '@mui/material'

interface MUILinkProps extends Omit {
  // 버튼에 전달할 추가적인 props를 여기에 정의
}

const MUILinkComponent = React.forwardRef(
  (props, ref) => {
    return 
  },
)

const CreatedLinkComponent = createLink(MUILinkComponent)

export const CustomLink: LinkComponent = (props) => {
  return 
}
```

이 예제는 MUI(Material-UI)와 `@tanstack/react-router`를 사용하여 커스텀 링크 컴포넌트를 만드는 방법을 보여줍니다. `MUILinkComponent`는 `React.forwardRef`를 사용하여 정의되며, `createLink` 함수를 통해 링크 컴포넌트로 변환됩니다. 이렇게 생성된 `CustomLink` 컴포넌트는 라우터에서 사용할 수 있습니다.


### Mantine 예제

```tsx
import * as React from 'react'
import { createLink, LinkComponent } from '@tanstack/react-router'
import { Anchor, AnchorProps } from '@mantine/core'

interface MantineAnchorProps extends Omit {
  // 앵커에 전달할 추가 프로퍼티를 여기에 정의
}

const MantineLinkComponent = React.forwardRef<
  HTMLAnchorElement,
  MantineAnchorProps
>((props, ref) => {
  return 
})

const CreatedLinkComponent = createLink(MantineLinkComponent)

export const CustomLink: LinkComponent = (
  props,
) => {
  return 
}
```


