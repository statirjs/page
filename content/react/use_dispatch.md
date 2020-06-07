# Hook: useDispatch

#### Description

**"useDispatch"** in **@statirjs/react** it is a [**react**](https://reactjs.org/) hook that consume [**store**](/content/core/store.md) inside with **useContext**

#### Cases

```js
import { useDispath } from "@statirjs/react";

export function CounterButton() {
  const increment = useDispath((dispatch) => dispatch.counter.increment);

  return <button onClick={increment}>Increment me</button>;
}
```

#### Arguments

1. hook require selector function that select action function from **dispatch**

```js
function selector(dispatch) {
  return dispatch.counter.increment;
}

const increment = useDispath(selector);
```

> **NOTE:** for [**typescript**](https://www.typescriptlang.org/) it is possible to re-declare hook with your own types
>
> ```js
> type IDispatch = typeof store.dispatch;
>
> function useOwnDispatch<T>(selector: (dispatch: IDispatch) => T): T {
>   return useDispath(selector);
> }
> ```

#### Common requirements

1. select one action function per **useDispath** usage

```js
const increment = useDispath((dispatch) => dispatch.counter.increment); 👍

const counterActions = useDispath((dispatch) => dispatch.counter); 👎

const dispatch = useDispath((dispatch) => dispatch); 👎
```

2. common **react** hooks requirements
