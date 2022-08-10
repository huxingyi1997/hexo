---
title:  bigfrontend的React problems
date: 2021-06-17 21:07:33
categories: 
- web前端
tags:
- bigfrontend
- 前端
- 题库
- React
---

国外前端会考察哪些问题？js函数、系统设计、TS类型体操的用法，大大拓宽了我的眼界，去思考和实战更加相关的问题吧，React Problems考察了实用的React技巧，当然，已经是基于16.8版本以上的React hooks了。

<!-- more -->

# React Problems

该部分综合考察对React的熟悉程度

## [1. the React Counter app](https://bigfrontend.dev/react/The-React-Counter)

### 题目

As the first React problem, you are asked to create the famous Counter app.

1. counter starts from 0.
2. click the '+' button to increment.
3. click the '-' button to decrement.

> `data-testid` is used to test your code, please keep them.

### 我的答案

```tsx
import React, {useState} from 'react'

export function App() {
  const [count, setCount] = useState<number>(0);
   const decrement = () => setCount(count - 1);
   const increment = () => setCount(count + 1);
  return (
    <div>
      <button data-testid="decrement-button" onClick={decrement}>-</button>
      <button data-testid="increment-button" onClick={increment}>+</button>
      <p>clicked: {count}</p>
    </div>
  )
}

// run your code by clicking the run button on the right

```



## [2. useTimeout()](https://bigfrontend.dev/react/usetimeout)

### 题目

Create a hook to easily use `setTimeout(callback, delay)`.

1. reset the timer if delay changes
2. DO NOT reset the timer if only callback changes

### 我的答案

```typescript
import {useEffect, useRef} from 'react'

export function useTimeout(callback: () => void, delay: number) {
  // your code here
  const callbackRef = useRef(callback);
  callbackRef.current = callback;

  useEffect(() => {
    const id = setTimeout(() => {
      callbackRef.current();
    }, delay);
    return () => clearTimeout(id);
  }, [delay])
}

// if you want to try your code on the right panel
// remember to export App() component like below

// export function App() {
//   return <div>your app</div>
// }
```



## [3. useIsFirstRender()](https://bigfrontend.dev/react/useIsFirstRender)

### 题目

Create a hook to tell if it is the first render.

```ts
function App() {
  const isFirstRender = useIsFirstRender()
  // only true for the first render
  ...
}
```

### 我的答案

```typescript
import {useEffect, useRef} from 'react'

export function useIsFirstRender(): boolean {
  // your code here
  const isFirstRender = useRef(true);

  useEffect(() => {
    isFirstRender.current = false;
  }, []);

  return isFirstRender.current;
}

// if you want to try your code on the right panel
// remember to export App() component like below

// export function App() {
//   return <div>your app</div>
// }
```

或者不用useEffect

```typescript
import {useRef} from 'react'

export function useIsFirstRender(): boolean {
  // your code here
  const isFirstRender = useRef(true);

  if (isFirstRender.current) {
    isFirstRender.current = false;
    return true;
  }

  return false;
}
```



## [4. useSWR() I](https://bigfrontend.dev/react/useSWR-1)

### 题目

[SWR](https://swr.vercel.app/) is a popular library of data fetching.

Let's try to implement the basic usage by ourselves.

```ts
import React from 'react'

function App() {
  const { data, error } = useSWR('/api', fetcher)
  if (error) return <div>failed</div>
  if (!data) return <div>loading</div>

  return <div>succeeded</div>
}
```

1. this is not to replicate the true implementation of `useSWR()`
2. The first argument `key` is for deduplication, we can safely ignore it for now

### 我的答案

```typescript
import {useState, useEffect} from 'react'

export function useSWR<T = any, E = any>(
  _key: string,
  fetcher: () => T | Promise<T>
): {
  data?: T
  error?: E
} {
  // your code here
  const fetcherRes = fetcher();
  // 同步
  const [data, setData] = useState<T | undefined>(fetcherRes instanceof Promise ? undefined : fetcherRes);
  const [error, setError] = useState<E | undefined>(undefined);

  useEffect(() => {
    // 异步
    if (fetcherRes instanceof Promise) {
      Promise.resolve(fetcherRes)
      .then(res => setData(res))
      .catch(err => setError(err))
    }
  }, [])
  return {
    data,
    error
  }
}
```

使用useMemo

```typescript
import { useEffect, useMemo, useState } from "react";

export function useSWR<T = any, E = any>(
  _key: string,
  fetcher: () => T | Promise<T>
): {
  data?: T
  error?: E
} {
  // your code here
  const [data, setData] = useState<T>();
  const [error, setError] = useState<E>();
  const result = useMemo(fetcher, [_key]);

  useEffect(() => {
    if (result instanceof Promise) {
      result.then(setData, setError);
    }
  }, [])

  return {data: result instanceof Promise ? data : result, error}
}
```



## [5. usePrevious()](https://bigfrontend.dev/react/useSWR-1)

### 题目

Create a hook `usePrevious()` to return the previous value, with initial value of `undefined.

### 我的答案

```typescript
import {useRef, useEffect} from 'react'

export function usePrevious<T>(value: T): T | undefined {
  // your code here
  const previos = useRef<T>();

  useEffect(() => {
    previos.current = value;
  }, [value])

  return previos.current;
}
```



## [6. useHover()](https://bigfrontend.dev/react/useHover)

### 题目

It is common to see conditional rendering based on hover state of some element.

We can achive it by CSS pseduo class `:hover`, but for more complex cases it might be better to have state controlled by script.

Now you are asked to create a `useHover()` hook.

```tsx
function App() {
  const [ref, isHovered] = useHover()
  return <div ref={ref}>{isHovered ? 'hovered' : 'not hovered'}</div>
}
```

### 我的答案

```typescript
import { useRef, useState, useCallback, Ref } from 'react'

export function useHover<T extends HTMLElement>(): [Ref<T>, boolean] {
  // your code here
  const [isHovered, setIsHovered] = useState<boolean>(false);
  const handleMouseEnter = useCallback(() => {setIsHovered(true)}, []);
  const handleMouseLeave = useCallback(() => {setIsHovered(false)}, []);

  const ref = useRef<T>();

  const callbackRef = useCallback((node: T) => {
    if (ref.current) {
      ref.current.removeEventListener('mouseenter', handleMouseEnter);
      ref.current.removeEventListener('mouseleave', handleMouseLeave);
    }

    ref.current = node;
    if (ref.current) {
      ref.current.addEventListener('mouseenter', handleMouseEnter);
      ref.current.addEventListener('mouseleave', handleMouseLeave);
    }
  }, [handleMouseEnter, handleMouseLeave]);
  return [callbackRef, isHovered];
}
```



## [7. useToggle()](https://bigfrontend.dev/react/useToggle)

### 题目

It is quite common to see switches and checkboxes in web apps.

Implement `useToggle()` to make things easier

```tsx
function App() {
  const [on, toggle] = useToggle()
  ...
}
```

### 我的答案

```typescript
import { useState, useCallback } from 'react'

export function useToggle(on: boolean): [boolean, () => void] {
  // your code here
  const [toggle, setToggle] = useState(on);
  const toggleHandler = useCallback(() => {
    setToggle(prev => !prev);
  }, []);

  return [toggle, toggleHandler];
}
```



## [8. useDebounce()](https://bigfrontend.dev/react/useDebounce)

### 题目

For a frequently changing value like text input you might want to debounce the changes.

Implement `useDebounce()` to achieve this.

```tsx
function App() {
  const [value, setValue] = useState(...)
  // this value changes frequently, 
  const debouncedValue = useDebounce(value, 1000)
  // now it is debounced
}
```

The logic should be similar to [6. implement basic debounce()](https://bigfrontend.dev/problem/implement-basic-debounce)

### 我的答案

```typescript
import { useState, useEffect } from 'react'

export function useDebounce<T>(value: T, delay: number): T {
  const [debounced, setDebounced] = useState<T>(value);
  useEffect(() => {
    const timer = setTimeout(() => {
      setDebounced(value);
    }, delay);

    return () => {
      clearTimeout(timer);
    };
  }, [value, delay]);
  return debounced;
}
```



## [9. useEffectOnce()](https://bigfrontend.dev/react/useEffectOnce)

### 题目

Here is a simple problem, implement `useEffectOnce()` as the name says itself, it runs an effect only once.

### 我的答案

```typescript
import { useEffect, EffectCallback } from 'react'

export function useEffectOnce(effect: EffectCallback) {
  useEffect(() => {
    return effect();
  }, []);
}
```



## [10. phone number input](https://bigfrontend.dev/react/phone-number-input)

### 题目

Create a `PhoneNumberInput` component.

1. only accepts numerical digits
2. format the number automatically as `(123)456-7890` by

- adding the parenthesis when the 4th digit is entered
- also adding `-` before 7th digit

You can use the default text input without any styling.

### Follow-up

What if user removes some digits in the middle, does caret jumps to the end in your approach?

> caret position is not covered in our tests.

### 我的答案

```tsx
import React, {useState, ChangeEvent} from 'react'
export function PhoneNumberInput() {
  const [value, setValue] = useState<string>("");
  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const initialValue = e.target.value;
    let value: string = initialValue.replace(/\D/g, "");
    if (value.length > 0 && value.length <= 3) {
      value = value.replace(/^(\d{1,3})$/g, "$1");
    } else if (value.length > 3 && value.length <= 6) {
      value = value.replace(/^(\d{3})(\d{0,3})$/g, "($1)$2");
    } else if (value.length > 6) {
      value = value.replace(/^(\d{3})(\d{3})(\d{0,4})\d*$/g, "($1)$2-$3");
    }
    setValue(value);
  }
  return <input data-testid="phone-number-input" onChange={handleChange} value={value}/>
}

// if you want to try your code on the right panel
// remember to export App() component like below

export function App() {
  return <div><PhoneNumberInput /></div>
}
```



## [11. useFocus()](https://bigfrontend.dev/react/phone-number-input)

### 题目

CSS pseudo-class [:focus-within](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-within) could be used to allow conditional rendering in parent element on the focus state of descendant elements.

While it is cool, in complex web apps, it might be better to control the state in script.

Now please create `useFocus()` to support this.

```tsx
function App() {
  const [ref, isFocused] = useFocus()
  return <div>
    <input ref={ref}/>
    {isFocused && <p>focused</p>}
  </div>
}
```

### 我的答案

```tsx
import React, { Ref, useState, useRef, useCallback, useEffect } from 'react'

export function useFocus<T extends HTMLInputElement>(): [Ref<T>, boolean] {
  const ref = useRef<T>(null);
  const [isFocused, setIsFocused] = useState<boolean>(false);
  const toggle = useCallback(() => {
    setIsFocused(!isFocused);
  }, [isFocused]);

  useEffect(() => {
    const element = ref.current;

    element?.addEventListener('focus', toggle);
    element?.addEventListener('blur', toggle);

    return () => {
      element?.removeEventListener('focus', toggle);
      element?.removeEventListener('blur', toggle);
    }
  });

  return [ref, isFocused];
}

// if you want to try your code on the right panel
// remember to export App() component like below

export function App() {
  const [ref, isFocused] = useFocus()
  return <div>
    <input ref={ref}/>
    {isFocused && <p>focused</p>}
  </div>
}
```



## [12. useArray()](https://bigfrontend.dev/react/useArray)

### 题目

When array is used in React state, we need to deal with actions such as `push` and `remove`.

Implement `useArray()` to make life easier.

```ts
const {value, push, removeByIndex} = useArray([1, 2, 3])
```

### 我的答案

```tsx

// This is a React problem from BFE.dev

import React, { useState, useCallback } from 'react'

type UseArrayActions<T> = {
  push: (item: T) => void,
  removeByIndex: (index: number) => void
}

export function useArray<T>(initialValue: T[]): { value: T[] } & UseArrayActions<T> {
  const [value, setValue] = useState<T[]>(initialValue);

  const push = useCallback((item: T) => {
    setValue((preValue) => {
      return [...preValue, item];
    });
  }, []);

  const removeByIndex = useCallback((index: number) => {
    setValue((preValue) => {
      const newValue = [...preValue];
      newValue.splice(index, 1);
      return newValue;
    });
  }, []);

  return {
    value,
    push,
    removeByIndex
  }
}


// if you want to try your code on the right panel
// remember to export App() component like below

export function App() {
  const { value } = useArray([1, 2, 3])
  return <div>
    {value}
  </div>
}
```

