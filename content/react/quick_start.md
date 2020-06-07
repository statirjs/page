# Quick start

1. install [**@statirjs/core**](/content/core/home.md) and **@statirjs/react**

```shell
npm i @statirjs/core @statirjs/react
```

2. init [**store**](/content/core/store.md)

```js
import { initStore, createForme } from "@statirjs/core";

const counter = createForme(
  {
    count: 0,
  },
  () => ({
    actions: {
      increment(state) {
        return {
          ...state,
          count: state.count + 1,
        };
      },
    },
  })
);

const store = initStore({
  formes: {
    counter,
  },
});
```

3. wrap potential consumers by [**provider**](/content/react/provider.md)

```js
import React from "react";
import { Provider } from "@statirjs/react";
import { store } from "./store";

export function App() {
  return (
    <Provider store={store}>
      <Counter> // Your consumers components
    </Provider>
  );
}
```

4. import and use hooks

```js
import { useSelector, useDispath } from "@statirjs/react";

export function Counter() {
  const count = useSelector((rootState) => rootState.counter.count);

  const increment = useDispath((dispatch) => dispatch.counter.increment);

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={increment}>Increment me</button>
    </div>
  );
}
```

> **NOTE:** for [**typescript**](https://www.typescriptlang.org/) it is possible to re-declare hooks with your own types
>
> ```js
> type IState = typeof store.state;
> type IDispatch = typeof store.dispatch;
>
> function useOwnSelector<T>(selector: (rootState: IState) => T): T {
>   return useSelector(selector);
> }
>
> function useOwnDispatch<T>(selector: (dispatch: IDispatch) => T): T {
>   return useDispath(selector);
> }
> ```
