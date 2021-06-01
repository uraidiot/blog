---
title: React-Hooks
date: 2021-06-01 17:07:36
tags:
---



### React

> Facebook 公司出品

#### React hooks

> *Hook* 是*React* 16.8 的新增特性。February 6, 2019

`hook`基本`API`:

> `useState`, `useEffect`, `useCallback`, `useMome`, `useRef`, `useContext`

1. `useState`

> 让函数组件具有维持状态的能力

```
useState(initialValue)
```



`state`中永远不要保存可以通过计算得到的值: 

- 从`props`传过来的值
- 从`URL`中读到的值
- 从`cookie`, `localStorage`中读取的值

2. `useEffect`

> 执行一段副作用

```
useEffect(fn, deps)
```



**副作用**是指一段和当前执行结果无关的代码。在函数组件的当次执行过程中，`useEffect` 中代码的执行是**不影响**渲染出来的 UI 的。`useEffect` 是每次组件 `render` 完后判断依赖并执行

##### 理解`Hooks`中的依赖`deps`

> `Hooks`通过依赖提供了监听某个数据变化的能力, 包括 `useState`, `useCallback`, `useMemo` 等中的 `deps`

注意事项: 

- 依赖项中定义的变量一定是会在回调函数中用到的，否则声明依赖项其实是没有意义的。
- 依赖项一般是一个常量数组，而不是一个变量。因为一般在创建 `callback` 的时候，你其实非常清楚其中要用到哪些依赖项了
- React 会使用**浅比较**来对比依赖项是否发生了变化，所以要特别注意数组或者对象类型。如果你是每次创建一个新对象，即使和之前的值是等价的，也会被认为是依赖项发生了变化。

3. `useCallback`

> 缓存回调函数

```
useCallback(fn, deps)
```



每一次 UI 变化都是通过重新执行函数组件实现, 函数组件若声明有 `handle`  事件处理函数, 就会导致重复声明. 此时绑定了该事件处理函数的组件就会重新渲染, 导致额外的性能开销.

```

import React, { useState, useCallback } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  const handleIncrement = useCallback(
    () => setCount(count + 1),
    [count], // 只有当 count 发生变化时，才会重新创建回调函数
  );
  // ...
  return <button onClick={handleIncrement}>+</button> // 若未通过 useCallback 缓存 onClick 的事件处理函数, 每次 UI 变化(或其他 state 导致)就会重新执行函数组件, 从而重新声明 handleIncrement 函数, 而让 button 组件重新渲染
}

```

4. `useMemo`

> 缓存计算的结果

```
useMemo(fn, deps)
```



通过 `state` 的计算后再进行 UI 的渲染, 则该数据可通过 `useMome` 进行缓存. `deps` 有变化后再进行重新计算

```

//...
// 使用 userMemo 缓存计算的结果
const usersToShow = useMemo(() => {
    if (!users) return null;
    return users.data.filter((user) => {
      return user.first_name.includes(searchKey));
    }
  }, [users, searchKey]);
//...
```

同时, `useCallback` 的功能可以通过 `useMemo` 实现: 

```

const myEventHandler = useMemo(() => {
   // 返回一个函数作为缓存结果
   return () => {
     // 在这里进行事件处理
   }
 }, [dep1, dep2]);
```

`useCallback`, `useMemo` 都是建立了一个绑定某个结果到依赖数据的关系。只有当依赖变了，这个结果才需要被重新得到

5. `useRef`

> 在多次渲染之间共享数据

```
const myRefContainer = useRef(initialValue)
```

可以把 `useRef` 看作是在函数组件之外创建的一个容器空间。在这个容器上，我们可以通过唯一的 `current` 属设置一个值，从而在函数组件的多次渲染之间共享这个值。(在类 `class` 组件中, 可以通过设置类的成员变量实现这个功能)

```

import React, { useState, useCallback, useRef } from "react";

export default function Timer() {
  // 定义 time state 用于保存计时的累积时间
  const [time, setTime] = useState(0);

  // 定义 timer 这样一个容器用于在跨组件渲染之间保存一个变量
  const timer = useRef(null);

  // 开始计时的事件处理函数
  const handleStart = useCallback(() => {
    // 使用 current 属性设置 ref 的值
    timer.current = window.setInterval(() => {
      setTime((time) => time + 1);
    }, 100);
  }, []);

  // 暂停计时的事件处理函数
  const handlePause = useCallback(() => {
    // 使用 clearInterval 来停止计时
    window.clearInterval(timer.current);
    timer.current = null;
  }, []);

  return (
    <div>
      {time / 10} seconds.
      <br />
      <button onClick={handleStart}>Start</button>
      <button onClick={handlePause}>Pause</button>
    </div>
  );
}
```

同时也可以作为**某个 `DOM` 节点的引用**

```

function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // current 属性指向了真实的 input 这个 DOM 节点，从而可以调用 focus 方法
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```



6. `useContext`

> 定义全局状态

```
const value = useContext(MyContext);
```

`Context` 更多的是提供了一个强大的机制，让 React 应用具备定义全局的响应式数据的能力。例如可以做全局响应式的 Theme, Language

