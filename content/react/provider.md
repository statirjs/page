# Provider

#### Description

**"Provider"** in **@statirjs/react** it is a [**react**](https://reactjs.org/) component wrapper that work with **context**

#### Cases

```js
import React from "react";
import { Provider } from "@statirjs/react";
import { store } from "./store";

export function App() {
  return (
    <Provider store={store}>
      { ... }
    </Provider>
  );
}
```

#### Common requirements

1. **provider** component require [**store**](/content/core/store.md) object created by **initStore**

```js
<Provider store={store}>
```

2. components that use **store** must be setted as child

```js
<Provider store={store}>
  <Counter /> 👍
</Provider>

<Provider store={store}>
  <CounterDisplay /> 👍

  <CounterButton />
</Provider>

<Provider store={store}>
  <>
    <Counter /> 👍
  </>
</Provider>
```
