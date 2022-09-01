---
title:  bigfrontend的React quizzes
date: 2022-06-17 21:07:33
categories: 
- web前端
tags:
- bigfrontend
- 前端
- 题库
- React
---

React Quizzes考察了对于React的理解，是基于16.8版本以上的React hooks了。

<!-- more -->

# React Quizzes

该部分综合考察对React的熟悉程度

## [1. React re-render 1](https://bigfrontend.dev/react-quiz/React-re-render-1)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { useState, useEffect} from 'react'
import ReactDOM from 'react-dom'

function A() {
  console.log('A')
  return <B/>
}

function B() {
  console.log('B')
  return <C/>
}

function C() {
  console.log('C')
  return null
}

function D() {
  console.log('D')
  return null
}

function App() {
  const [state, setState] = useState(0)
  useEffect(() => {
    setState(state => state + 1)
  }, [])
  console.log('App')
  return (
    <div>
      <A state={state}/>
      <D/>
    </div>
  )
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)
```

### 答案

```sh
"App"
"A"
"B"
"C"
"D"
"App"
"A"
"D"
```

### 详细解析

there are three reasons that will cause re-rendering

- update in state
- update in prop
- re-rendering of parent component

One thing to note, while parent component called the re-rendering function, the sebsequent will rerender whether their props change or not

```tsx
import React, { useState, useEffect, memo } from "react";
import ReactDOM from "react-dom";

/* 
Whenever component re-renders, does it mean that React re-renders the real DOM each time? 
No, React only updates the part of the UI that changed. 
A render is scheduled by React each time the state of a component is modified. 
For example, updating state via the setState hook will not happen immediately but React will execute it at the best possible moment.

But calling the render function has some side-effects even if the real DOM is not re-rendered:
    - the code inside the function is executed each time, which can be time-consuming depending on its content
    - the diffing algorithm is executed for each component to be able to determine if the UI needs to be updated
*/

function A() {
  console.log("A");
  return <B />;
}

function B() {
  console.log("B");
  return <C />;
}

function C() {
  console.log("C");
  return null;
}

function D() {
  console.log("D");
  return null;
}

/* 
Each state change in the parent component triggers a re-rendering of the child 
components even if they did not receive any props.

We can prevent children components from this needless rendering but wraping them
inside `React.memo`. They then child won't render on state change of parent. They 
will re-render only when props passed to them change. Even after wrapping in `memo`
they will always render on change of their internal state.

However when child component re-renders then its parent component is not re-rendered.
*/

function App() {
  const [state, setState] = useState(0);
  useEffect(() => {
    setState((state) => state + 1);
  }, []);
  console.log("App");
  return (
    <div>
      <A state={state} />
      <D />
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));

/* Ans:
"App"
"A"
"B"
"C"
"D"
"App"
"A"
"B"
"C"
"D"
 */
```



## [2. React re-render 2 - memo()](https://bigfrontend.dev/react-quiz/React-re-render-2)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { useState, useEffect, memo } from 'react'
import ReactDOM from 'react-dom'

function A() {
  console.log('A')
  return <B/>
}

const B = memo(() => {
  console.log('B')
  return <C/>
})

function C() {
  console.log('C')
  return null
}

function D() {
  console.log('D')
  return null
}

function App() {
  const [state, setState] = useState(0)
  useEffect(() => {
    setState(state => state + 1)
  }, [])
  console.log('App')
  return (
    <div>
      <A state={state}/>
      <D/>
    </div>
  )
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)
```

### 答案

```sh
"App"
"A"
"B"
"C"
"D"
"App"
"A"
"B"
"C"
"D"
```

### 详细解析

only rerender while the memorized value is different from the previous value

```tsx
import React, { useState, useEffect, memo } from "react";
import ReactDOM from "react-dom";

/* 
Whenever component re-renders, does it mean that React re-renders the real DOM each time? 
No, React only updates the part of the UI that changed. 
A render is scheduled by React each time the state of a component is modified. 
For example, updating state via the setState hook will not happen immediately but React will execute it at the best possible moment.

But calling the render function has some side-effects even if the real DOM is not re-rendered:
    - the code inside the function is executed each time, which can be time-consuming depending on its content
    - the diffing algorithm is executed for each component to be able to determine if the UI needs to be updated
*/

function A() {
  console.log("A");
  return <B />;
}

function B() {
  console.log("B");
  return <C />;
}

function C() {
  console.log("C");
  return null;
}

function D() {
  console.log("D");
  return null;
}

/* 
Each state change in the parent component triggers a re-rendering of the child 
components even if they did not receive any props.

We can prevent children components from this needless rendering but wraping them
inside `React.memo`. They then child won't render on state change of parent. They 
will re-render only when props passed to them change. Even after wrapping in `memo`
they will always render on change of their internal state.

However when child component re-renders then its parent component is not re-rendered.
*/

function App() {
  const [state, setState] = useState(0);
  useEffect(() => {
    setState((state) => state + 1);
  }, []);
  console.log("App");
  return (
    <div>
      <A state={state} />
      <D />
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));

/* Ans:
"App"   // first render before useEffect() worked
"A"     // first render
"B"     // first render
"C"     // first render
"D"     // first render
"App"   // second render after useEffect() worked
"A"     // second render -> no B because of memo() -> no C because no B
"D"     // second render
 */
```



## [3. React re-render 3](https://bigfrontend.dev/react-quiz/React-re-render-3)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { useState, useEffect } from 'react'
import ReactDOM from 'react-dom'

function A({ children }) {
  console.log('A')
  return children
}

function B() {
  console.log('B')
  return <C/>
}

function C() {
  console.log('C')
  return null
}

function D() {
  console.log('D')
  return null
}

function App() {
  const [state, setState] = useState(0)
  useEffect(() => {
    setState(state => state + 1)
  }, [])
  console.log('App')
  return (
    <div>
      <A><B/></A>
      <D/>
    </div>
  )
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)
```

### 答案

```sh
"App"
"A"
"B"
"C"
"D"
"App"
"A"
"B"
"C"
"D"
```

### 详细解析

When rerendering function is called in parent component, the sebsequent component will re-render whether they are changed or not

```tsx
// This is a React Quiz from BFE.dev 

import React, { useState, useEffect } from 'react'
import ReactDOM from 'react-dom'

function A({ children }) {
  console.log('A')
  return children
}

function B() {
  console.log('B')
  return <C/>
}

function C() {
  console.log('C')
  return null
}

function D() {
  console.log('D')
  return null
}

function App() {
  const [state, setState] = useState(0)
  useEffect(() => {
    setState(state => state + 1)
  }, [])
  console.log('App')
  return (
    <div>
      <A><B/></A>
      <D/>
    </div>
  )
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)

/* Ans:
"App"
"A"
"B"
"C"
"D"
"App"
"A"
"B"
"C"
"D"
 */
```



## [4. React re-render 4](https://bigfrontend.dev/react-quiz/React-re-render-3)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { useState, useEffect } from 'react'
import ReactDOM from 'react-dom'

function A({ children }) {
  console.log('A')
  return children
}

function B() {
  console.log('B')
  return <C/>
}

function C() {
  console.log('C')
  return null
}

function D() {
  console.log('D')
  return null
}

function App() {
  const [state, setState] = useState(0)
  useEffect(() => {
    setState(state => state + 1)
  }, [])
  console.log('App')
  return (
    <div>
      <A><B/></A>
      <D/>
    </div>
  )
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)
```

### 答案

```sh
"App"
"A"
"B"
"C"
"D"
"A"
```

### 详细解析

```tsx
import React, { useState, useEffect } from "react";
import ReactDOM from "react-dom";

function A({ children }) {
  console.log("A");
  const [state, setState] = useState(0);
  useEffect(() => {
    setState((state) => state + 1);
  }, []);
  /* Here even on state change of `A` parent component, child components will not re-render. When the parent state
   changes, parent component re-renders. But it still has the same children prop it got last time, 
   so React doesn’t visit that subtree. And as a result, child component doesn’t re-render.
  
  
  Thus there are two ways to 
  prevent child components from re-rendering.
    - wrapping them in `memeo`
    - passing them as `children` prop
 */
  return children;
}

function B() {
  console.log("B");
  return <C />;
}

function C() {
  console.log("C");
  return null;
}

function D() {
  console.log("D");
  return null;
}

function App() {
  console.log("App");
  return (
    <div>
      <A>
        <B />
      </A>
      <D />
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));

/* Ans:

"App"
"A"
"B"
"C"
"D"
"A"

*/
```



## [6. React re-render 5 - context](https://bigfrontend.dev/react-quiz/React-re-render-5)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { useState, memo, createContext, useEffect, useContext} from 'react'
import ReactDOM from 'react-dom'

const MyContext = createContext(0);

function B() {
  const count = useContext(MyContext)
  console.log('B')
  return null
}

const A = memo(() => {
  console.log('A')
  return <B/>
})

function C() {
  console.log('C')
  return null
}
function App() {
  const [state, setState] = useState(0)
  useEffect(() => {
    setState(state => state + 1)
  }, [])
  console.log('App')
  return <MyContext.Provider value={state}>
    <A/>
    <C/>
  </MyContext.Provider>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)
```

### 答案

```sh
"App"
"A"
"B"
"C"
"App"
"B"
"C"
```

### 详细解析

context is like "global"

- All of the descendants of a Provider will rerender whenever the Provider's value is updated
- Even though therir parents component doesn't update
- Consumers is not subject to the shouleComponentUpdate

```tsx
// This is a React Quiz from BFE.dev 

import React, { useState, memo, createContext, useEffect, useContext} from 'react'
import ReactDOM from 'react-dom'

const MyContext = createContext(0);

function B() {
  const count = useContext(MyContext)
  console.log('B')
  return null
}
// When the second time render comes, A would not be re-render again for the memo // method, but the computed value contains component B, which called useContext 
// with a modified value of MyContext. so B would re-render, answer below
// "App" "A" "B" "C" "App" "B" "C"
const A = memo(() => {
  console.log('A')
  return <B/>
})

function C() {
  console.log('C')
  return null
}
function App() {
	// state is change. This same state is passed in
    // context. So all those compoonets that use this context and
    // their children will re-render
  const [state, setState] = useState(0)
  useEffect(() => {
    setState(state => state + 1)
  }, [])
  console.log('App')
  return <MyContext.Provider value={state}>
    <A/>
    <C/>
  </MyContext.Provider>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)
```



## [7. Suspense 1](https://bigfrontend.dev/react-quiz/Suspense-1)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { Suspense } from 'react'
import ReactDOM from 'react-dom'

const resource = (() => {
  let data = null
  let status = 'pending'
  let fetcher = null
  return {
    get() {
      if (status === 'ready') {
        return data
      }
      if (status === 'pending') {
        fetcher = new Promise((resolve, reject) => {
          setTimeout(() => {
            data = 1
            status = 'ready'
            resolve()
          }, 100)
        })
        status = 'fetching'
      }

      throw fetcher
    }
  }
})()

function A() {
  console.log('A1')
  const data = resource.get()
  console.log('A2')
  return <p>{data}</p>
}

function Fallback() {
  console.log('fallback')
  return null
}

function App() {
  console.log('App')
  return <div>
    <Suspense fallback={<Fallback/>}>
      <A/>
    </Suspense>
  </div>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)
```

### 答案

```sh
"App"
"A1"
"fallback"
"A1"
"A2"
```

### 详细解析

- "suspend" rendering in order to wait for some async resource
- re-render again when the resources finnally loaded

```tsx
// This is a React Quiz from BFE.dev 

import React, { Suspense } from 'react'
import ReactDOM from 'react-dom'

const resource = (() => {
  let data = null
  let status = 'pending'
  let fetcher = null
  return {
    get() {
      if (status === 'ready') {
        return data
      }
      if (status === 'pending') {
        fetcher = new Promise((resolve, reject) => {
          setTimeout(() => {
            data = 1
            status = 'ready'
            resolve()
          }, 100)
        })
        status = 'fetching'
      }

      throw fetcher
    }
  }
})()

function A() {
  console.log('A1')
  const data = resource.get()
  console.log('A2')
  return <p>{data}</p>
}

function Fallback() {
  console.log('fallback')
  return null
}

function App() {
  console.log('App')
  return <div>
    <Suspense fallback={<Fallback/>}>
      <A/>
    </Suspense>
  </div>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)

/* Ans:

"App"
"A1"
"fallback"
"A1"
"A2"

*/
```



## [8. Suspense 2](https://bigfrontend.dev/react-quiz/Suspense-2)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { Suspense } from 'react'
import ReactDOM from 'react-dom'

const resource = (() => {
  let data = null
  let status = 'pending'
  let fetcher = null
  return {
    get() {
      if (status === 'ready') {
        return data
      }
      if (status === 'pending') {
        fetcher = new Promise((resolve, reject) => {
          setTimeout(() => {
            data = 1
            status = 'ready'
            resolve()
          }, 100)
        })
        status = 'fetching'
      }

      throw fetcher
    }
  }
})()

function A() {
  console.log('A1')
  const data = resource.get()
  console.log('A2')
  return <p>{data}</p>
}

function Fallback() {
  console.log('fallback')
  return null
}

function App() {
  console.log('App')
  return <div>
    <Suspense fallback={<Fallback/>}>
      <A/>
    </Suspense>
  </div>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)
```

### 答案

```sh
"App"
"A1"
"fallback"
"A1"
"A2"
```

### 详细解析

Key:

Suspense component will be rendered when a child ran into async render (but other children will be rendered first) and all children in Suspense will be re-rendered after async resolve

```tsx
// This is a React Quiz from BFE.dev 

import React, { Suspense } from 'react'
import ReactDOM from 'react-dom'

const resource = (() => {
  let data = null
  let status = 'pending'
  let fetcher = null
  return {
    get() {
      if (status === 'ready') {
        return data
      }
      if (status === 'pending') {
        fetcher = new Promise((resolve, reject) => {
          setTimeout(() => {
            data = 1
            status = 'ready'
            resolve()
          }, 100)
        })
        status = 'fetching'
      }

      throw fetcher
    }
  }
})()

function A() {
  console.log('A1')
  const data = resource.get()
  console.log('A2')
  return <p>{data}</p>
}

function Fallback() {
  console.log('fallback')
  return null
}

function App() {
  console.log('App')
  return <div>
    <Suspense fallback={<Fallback/>}>
      <A/>
    </Suspense>
  </div>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)

/* Ans:

"App"
"A1"
"fallback"
"A1"
"A2"

*/
```



## [9. React re-render 6 - Context](https://bigfrontend.dev/react-quiz/react-rerender-6-context)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { useState, createContext, useEffect, useContext} from 'react'
import ReactDOM from 'react-dom'

const MyContext = createContext(0);

function B({children}) {
  const count = useContext(MyContext)
  console.log('B')
  return children
}

const A = ({children}) => {
  const [state, setState] = useState(0)
  console.log('A')
  useEffect(() => {
    setState(state => state + 1)
  }, [])
  return <MyContext.Provider value={state}>
    {children}
  </MyContext.Provider>
}

function C() {
  console.log('C')
  return null
}

function D() {
  console.log('D')
  return null
}
function App() {
  console.log('App')
  return <A><B><C/></B><D/></A>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)
```

### 答案

```sh
"App"
"A"
"B"
"C"
"D"
"A"
"B"
```

### 详细解析

1. Render as usual in order of children
2. When re render from state occurs, we rerender A and all consumers of the context. Theres no need to rerender the other children. So only B is rerendered on the context change

```tsx
// This is a React Quiz from BFE.dev 

import React, { useState, createContext, useEffect, useContext} from 'react'
import ReactDOM from 'react-dom'

const MyContext = createContext(0);

function B({children}) {
  const count = useContext(MyContext)
  console.log('B')
  return children
}

const A = ({children}) => {
  const [state, setState] = useState(0)
  console.log('A')
  useEffect(() => {
    setState(state => state + 1)
  }, [])
  return <MyContext.Provider value={state}>
    {children}
  </MyContext.Provider>
}

function C() {
  console.log('C')
  return null
}

function D() {
  console.log('D')
  return null
}
function App() {
  console.log('App')
  return <A><B><C/></B><D/></A>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)

/* Ans:

"App"
"A"
"B"
"C"
"D"
"A"
"B"

*/
```



## [10. useLayoutEffect()](https://bigfrontend.dev/react-quiz/useLayoutEffect)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { useState, useEffect, useLayoutEffect} from 'react'
import ReactDOM from 'react-dom'

function App() {
  console.log('App')
  const [state, setState] = useState(0)
  useEffect(() => {
    setState(state => state + 1)
  }, [])

  useEffect(() => {
    console.log('useEffect 1')
    return () => {
      console.log('useEffect 1 cleanup')
    }
  }, [state])

  useEffect(() => {
    console.log('useEffect 2')
    return () => {
      console.log('useEffect 2 cleanup')
    }
  }, [state])

  useLayoutEffect(() => {
    console.log('useLayoutEffect')
    return () => {
      console.log('useLayoutEffect cleanup')
    }
  }, [state])

  return null
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)
```

### 答案

```sh
"App"
"useLayoutEffect"
"useEffect 1"
"useEffect 2"
"App"
"useLayoutEffect cleanup"
"useLayoutEffect"
"useEffect 1 cleanup"
"useEffect 2 cleanup"
"useEffect 1"
"useEffect 2"
```

### 详细解析

```tsx
import React, { useState, useEffect, useLayoutEffect} from 'react'
import ReactDOM from 'react-dom'

function App() {
  console.log('App')
  const [state, setState] = useState(0)
  useEffect(() => {
    setState(state => state + 1)
  }, [])

  useEffect(() => {
    console.log('useEffect 1')
    return () => {
      console.log('useEffect 1 cleanup')
    }
  }, [state])

  useEffect(() => {
    console.log('useEffect 2')
    return () => {
      console.log('useEffect 2 cleanup')
    }
  }, [state])

  useLayoutEffect(() => {
    console.log('useLayoutEffect')
    return () => {
      console.log('useLayoutEffect cleanup')
    }
  }, [state])

  return null
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>)

/* Ans:

"App" // Initial rendering cycle doesn't run any clean up.
"useLayoutEffect"
"useEffect 1"
"useEffect 2"
"App" // Re-render
"useLayoutEffect cleanup" // useLayoutEffect is first to be cleaned up and immediately executed.
"useLayoutEffect"
"useEffect 1 cleanup" // Regular useEffects are grouped, cleaned up and then executed for the second rendering cycle.
"useEffect 2 cleanup"
"useEffect 1"
"useEffect 2"

*/
```



## [11. callback props](https://bigfrontend.dev/react-quiz/useLayoutEffect)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { memo, useState} from 'react'
import ReactDOM from 'react-dom'
import { screen, fireEvent } from '@testing-library/dom'

function _A({ onClick }) {
  console.log('A')
  return <button onClick={onClick} data-testid="button">click me</button>
}

const A = memo(_A)

function App() {
  console.log('App')
  const [state, setState] = useState(0)
  return <div>
    {state}
    <A onClick={() => {setState(state => state + 1)}}/>
  </div>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>);

// click the button
;(async function() {
  const button = await screen.findByTestId('button')
  fireEvent.click(button)
})()
```

### 答案

```sh
"App"
"A"
"App"
"A"
```

### 详细解析

a new function gets created on every render and React.memo thinks that the prop has changed

```tsx
// This is a React Quiz from BFE.dev 

import React, { memo, useState} from 'react'
import ReactDOM from 'react-dom'
import { screen, fireEvent } from '@testing-library/dom'

function _A({ onClick }) {
  console.log('A')
  return <button onClick={onClick} data-testid="button">click me</button>
}

const A = memo(_A)

function App() {
  console.log('App')
  const [state, setState] = useState(0)
  return <div>
    {state}
    <A onClick={() => {setState(state => state + 1)}}/>
  </div>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>);

// click the button
;(async function() {
  const button = await screen.findByTestId('button')
  fireEvent.click(button)
})()

/* Ans:

"App"
"A"
"App"
"A"

*/
```



## [11. callback props](https://bigfrontend.dev/react-quiz/callback-props)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { memo, useState} from 'react'
import ReactDOM from 'react-dom'
import { screen, fireEvent } from '@testing-library/dom'

function _A({ onClick }) {
  console.log('A')
  return <button onClick={onClick} data-testid="button">click me</button>
}

const A = memo(_A)

function App() {
  console.log('App')
  const [state, setState] = useState(0)
  return <div>
    {state}
    <A onClick={() => {setState(state => state + 1)}}/>
  </div>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>);

// click the button
;(async function() {
  const button = await screen.findByTestId('button')
  fireEvent.click(button)
})()
```

### 答案

```sh
"App"
"A"
"App"
"A"
```

### 详细解析

a new function gets created on every render and React.memo thinks that the prop has changed

```tsx
// This is a React Quiz from BFE.dev 

import React, { memo, useState} from 'react'
import ReactDOM from 'react-dom'
import { screen, fireEvent } from '@testing-library/dom'

function _A({ onClick }) {
  console.log('A')
  return <button onClick={onClick} data-testid="button">click me</button>
}

const A = memo(_A)

function App() {
  console.log('App')
  const [state, setState] = useState(0)
  return <div>
    {state}
    <A onClick={() => {setState(state => state + 1)}}/>
  </div>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>);

// click the button
;(async function() {
  const button = await screen.findByTestId('button')
  fireEvent.click(button)
})()

/* Ans:

"App"
"A"
"App"
"A"

*/
```



## [12. useEffect](https://bigfrontend.dev/react-quiz/useEffect)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { useEffect, useState } from 'react'
import ReactDOM from 'react-dom'

function App() {
  const [state, setState] = useState(0)
  console.log(state)

  useEffect(() => {
    setState(state => state + 1)
  }, [])

  useEffect(() => {
    console.log(state)
    setTimeout(() => {
      console.log(state)
    }, 100)
  }, [])

  return null
}

ReactDOM.render(<App/>, document.getElementById('root'))
```

### 答案

```sh
0
0
1
0
```

### 详细解析

1st render

console.log(0) 【collect】

after 1st render

callback: setState function

console.log(state), now is still 0 【collect】

callback: setTimeOut(wait for 100 and then fires callback)

start working on the callback queue:

setState -> state=1

setTimeOut(wait for 100 and then fires callback)

2nd render

console.log(1) 【collect】

the 100ms passed, fires the setTimeOut function, here because of closure, console.log(0) 【collect】

```tsx
// This is a React Quiz from BFE.dev 

import React, { useEffect, useState } from 'react'
import ReactDOM from 'react-dom'

function App() {
  const [state, setState] = useState(0)
  console.log(state)

  useEffect(() => {
    setState(state => state + 1)
  }, [])

  useEffect(() => {
    console.log(state)
    setTimeout(() => {
      console.log(state)
    }, 100)
  }, [])

  return null
}

ReactDOM.render(<App/>, document.getElementById('root'))

/* Ans:

0
0
1
0

*/
```



## [13. useRef](https://bigfrontend.dev/react-quiz/useRef)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { useRef, useEffect, useState } from 'react'
import ReactDOM from 'react-dom'

function App() {
  const ref = useRef(null)
  const [state, setState] = useState(1)

  useEffect(() => {
    setState(2)
  }, [])

  console.log(ref.current?.textContent)

  return <div>
    <div ref={state === 1 ? ref : null}>1</div>
    <div ref={state === 2 ? ref : null}>2</div>
  </div>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>);
```

### 答案

```sh
undefined
"1"
```

### 详细解析

//undefined - because on initial render ref is //set to null and `null?.textContent` evaluates //to undefined

//"1" because during initial render current of //ref is set to the first div with textContent //value 1, this value is preserved/cached //when //useEffect/setState triggers a re-render

```tsx
// This is a React Quiz from BFE.dev 

import React, { useRef, useEffect, useState } from 'react'
import ReactDOM from 'react-dom'

function App() {
  const ref = useRef(null)
  const [state, setState] = useState(1)

  useEffect(() => {
    setState(2)
  }, [])

  console.log(ref.current?.textContent)

  return <div>
    <div ref={state === 1 ? ref : null}>1</div>
    <div ref={state === 2 ? ref : null}>2</div>
  </div>
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App/>);

/* Ans:

undefined
"1"

*/
```



## [14. async event handler](https://bigfrontend.dev/react-quiz/async-event-handler)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev 

import React, { useState } from 'react'
import ReactDOM from 'react-dom'
import { screen } from '@testing-library/dom'
import userEvent from '@testing-library/user-event'

function App() {
  const [state, setState] = useState(0)
  const increment = () => {
    setTimeout(() => {
      setState(state + 1)
    }, 0)
  }
  console.log(state)
  return <div>
    <button onClick={increment}>click me</button>
  </div>
}

ReactDOM.render(<App/>, document.getElementById('root'))

// click the button twice
userEvent.click(screen.getByText('click me'))
userEvent.click(screen.getByText('click me'))
```

### 答案

```sh
0
1
1
```

### 详细解析

Key:

setTimeout(() => {}, 0) put setState call in a marcro task with the current state bind into closure

that's why in the second and third render, states are both 1.

Answer:

```tsx
// This is a React Quiz from BFE.dev 

import React, { useState } from 'react'
import ReactDOM from 'react-dom'
import { screen } from '@testing-library/dom'
import userEvent from '@testing-library/user-event'

function App() {
  const [state, setState] = useState(0)
  const increment = () => {
    setTimeout(() => {
      setState(state + 1)
    }, 0)
  }
  console.log(state)
  return <div>
    <button onClick={increment}>click me</button>
  </div>
}

ReactDOM.render(<App/>, document.getElementById('root'))

// click the button twice
userEvent.click(screen.getByText('click me'))
userEvent.click(screen.getByText('click me'))

/* Ans:

0
1
1

*/
```



## [15. memo 2](https://bigfrontend.dev/react-quiz/memo-2)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx

import React, { memo, useState } from 'react'
import ReactDOM from 'react-dom'
import { screen } from '@testing-library/dom'
import userEvent from '@testing-library/user-event'

function _B() {
  console.log('B')
  return null
}

const B = memo(_B)

function _A({ children }) {
  console.log('A')
  return children
}

const A = memo(_A)

function App() {
  const [count, setCount] = useState(0)
  return <div>
    <button onClick={
      () => setCount(count => count + 1)
    }>
      click me
    </button>
    <A><B/></A>
  </div>
}

ReactDOM.render(<App/>, document.getElementById('root'))

userEvent.click(screen.getByText('click me'))
```

### 答案

```sh
"A"
"B"
"A"
```

### 详细解析

first render, both component will be rendered
"A"
"B"
second render, since children in A doesn't have any props change, B will return memorized render result directly, not do re-render, hence only A get rendered
"A"

https://gist.github.com/slikts/e224b924612d53c1b61f359cfb962c06

https://stackoverflow.com/questions/47567429/this-props-children-not-re-rendered-on-parent-state-change

Here's what I understood from those articles, still not 100% sure, but hope can give you some ideas

Essentially, when App render

```js
<A>
    <B />
</A>
```

jsx will create a new React element that pass to A

```js
{
  type: A,
  props: {
    children: { memorized B }
  }
}
```

Note, A's props.children is a new object, so even you memorized A, it still re-rendered

when react render down to B, since B's props didn't change, so it returned memoized result (even it's a new React Element to A, it's rendering result is the same one)

```tsx
import React, { memo, useState } from 'react'
import ReactDOM from 'react-dom'
import { screen } from '@testing-library/dom'
import userEvent from '@testing-library/user-event'

function _B() {
  console.log('B')
  return null
}

const B = memo(_B)

function _A({ children }) {
  console.log('A')
  return children
}

const A = memo(_A)

function App() {
  const [count, setCount] = useState(0)
  return <div>
    <button onClick={
      () => setCount(count => count + 1)
    }>
      click me
    </button>
    <A><B/></A>
  </div>
}

ReactDOM.render(<App/>, document.getElementById('root'))

userEvent.click(screen.getByText('click me'))

/* Ans:

"A"
"B"
"A"

*/
```



## [16. event callback](https://bigfrontend.dev/react-quiz/memo-2)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev

import React, { useState } from 'react'
import ReactDOM from 'react-dom'
import { screen } from '@testing-library/dom'
import userEvent from '@testing-library/user-event'

function App() {
  const [state, setState] = useState(0)
  const onClick = () => {
    console.log('handler')
    setState(state => state + 1)
    console.log('handler ' + state)
  }
  console.log('render ' + state)
  return <div>
    <button onClick={onClick}>click me</button>
  </div>
}

ReactDOM.render(<App/>, document.getElementById('root'))
// click the button
userEvent.click(screen.getByText('click me'))
```

### 答案

```sh
"render 0"
"handler"
"handler 0"
"render 1"
```

### 详细解析

```tsx
// This is a React Quiz from BFE.dev

import React, { useState } from 'react'
import ReactDOM from 'react-dom'
import { screen } from '@testing-library/dom'
import userEvent from '@testing-library/user-event'

function App() {
  const [state, setState] = useState(0)
  const onClick = () => {
    console.log('handler')
    setState(state => state + 1)
    console.log('handler ' + state)
  }
  console.log('render ' + state)
  return <div>
    <button onClick={onClick}>click me</button>
  </div>
}

ReactDOM.render(<App/>, document.getElementById('root'))
// click the button
userEvent.click(screen.getByText('click me'))

/* Ans:

"render 0"
"handler"
"handler 0"
"render 1"

*/
```



## [17. flushSync()](https://bigfrontend.dev/react-quiz/flushsync)

### 题目

What does the code snippet to the right output by `console.log`?

```tsx
// This is a React Quiz from BFE.dev

import React, { useState } from 'react'
import ReactDOM, { flushSync } from 'react-dom'
import { screen } from '@testing-library/dom'
import userEvent from '@testing-library/user-event'

function App() {
  const [state, setState] = useState(0)
  const onClick = () => {
    console.log('handler')
    flushSync(() => {
      setState(state => state + 1)
    })
    console.log('handler ' + state)
  }
  console.log('render ' + state)
  return <div>
    <button onClick={onClick}>click me</button>
  </div>
}

ReactDOM.render(<App/>, document.getElementById('root'))
// click the button
userEvent.click(screen.getByText('click me'))
```

### 答案

```sh
"render 0"
"handler"
"render 1"
"handler 0"
```

### 详细解析

FlushSync flushes the entire tree and actually forces complete re-rendering for updates that happen inside of a call, so you should use it very sparingly. This way it doesn’t break the guarantee of internal consistency between props, state, and refs.

`"render 0"`: logged the first time the DOM is rendered

Then the click event happens

`"handler"`: logged when the handler for the click fires

`"render 1"`: logged first because `flushSync` causes the DOM to *synchronously* get re-rendered with the new state

`"handler 0"`: logged after `flushSync`, but using the state value that was available when this function first initialized

```tsx
// This is a React Quiz from BFE.dev

import React, { useState } from 'react'
import ReactDOM, { flushSync } from 'react-dom'
import { screen } from '@testing-library/dom'
import userEvent from '@testing-library/user-event'

function App() {
  const [state, setState] = useState(0)
  const onClick = () => {
    console.log('handler')
    flushSync(() => {
      setState(state => state + 1)
    })
    console.log('handler ' + state)
  }
  console.log('render ' + state)
  return <div>
    <button onClick={onClick}>click me</button>
  </div>
}

ReactDOM.render(<App/>, document.getElementById('root'))
// click the button
userEvent.click(screen.getByText('click me'))

/* Ans:

"render 0"
"handler"
"render 1"
"handler 0"

*/
```

