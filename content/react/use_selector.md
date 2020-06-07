# Hook: useSelector

#### Description

**"useSelector"** in **@statirjs/react** it is a [**react**](https://reactjs.org/) hook that consume [**store**](/content/core/store.md) inside with **useContext**

#### Cases

```js
import { useSelector } from "@statirjs/react";

export function CounterDisplay() {
  const count = useSelector((rootState) => rootState.counter.count);

  return <h1>{count}</h1>;
}
```

#### Arguments

1. hook require selector function that select state data from **store**

```js
function selector(rootState) {
  return rootState.counter.count;
}

const count = useSelector(selector);
```

> **NOTE:** for [**typescript**](https://www.typescriptlang.org/) it is possible to re-declare hook with your own types
>
> ```js
> type IState = typeof store.state;
>
> function useOwnSelector<T>(selector: (rootState: IState) => T): T {
>   return useSelector(selector);
> }
> ```

#### Common requirements

1. try to select one value per **useSelector** usage

```js
const count = useSelector((rootState) => rootState.counter.count); 👍

const counter = useSelector((rootState) => rootState.counter); 👎

const rootState = useSelector((rootState) => rootState); 👎
```

2. common **react** hooks requirements
