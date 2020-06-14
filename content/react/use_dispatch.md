# Hook: useDispatch

#### Description

**"useDispatch"** in **@statirjs/react** is a [**react**](https://reactjs.org/) hook that consumes [**store**](/content/core/store.md) inside with **useContext**

#### Cases

```js
import { useDispatch } from "@statirjs/react";

export function CounterButton() {
  const increment = useDispatch((dispatch) => dispatch.counter.increment);

  return <button onClick={increment}>Increment me</button>;
}
```

#### Arguments

1. hook requires selector function that selects action function from **dispatch**

```js
function selector(dispatch) {
  return dispatch.counter.increment;
}

const increment = useDispatch(selector);
```

> **NOTE:** for [**typescript**](https://www.typescriptlang.org/) it is possible to re-declare hook with your own types
>
> ```js
> type IDispatch = typeof store.dispatch;
>
> function useOwnDispatch<T>(selector: (dispatch: IDispatch) => T): T {
>   return useDispatch(selector);
> }
> ```

#### Common requirements

1. select one action function per **useDispatch** usage

```js
const increment = useDispatch((dispatch) => dispatch.counter.increment); 👍

const counterActions = useDispatch((dispatch) => dispatch.counter); 👎

const dispatch = useDispatch((dispatch) => dispatch); 👎
```

2. common **react** hooks requirements
