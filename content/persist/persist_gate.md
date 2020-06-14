# Persist gate

#### Description

**"PersistGate"** in **@statirjs/persist** is a [**react**](https://reactjs.org/) component wrapper that inits **persist** [**pipe**](/content/core/formes.md) to restore data from **storage**

#### Cases

```js
import React from "react";
import { Provider } from "@statirjs/react";
import { PersistGate } from "@statirjs/persist";
import { store } from "./store";

export function App() {
  return (
    <Provider store={store}>
      <PersistGate>
        <Counter />
      </PersistGate>
    </Provider>
  );
}
```

#### Common requirements

1. **gate** component must be child of [**Provider**](/content/react/provider.md)

```js
<Provider store={store}>
  <PersistGate>...</PersistGate>
</Provider>
```

2. components that use **store** must be settled as child

```js
<Provider store={store}>
  <PersistGate>
    <Counter /> 👍
  </PersistGate>
</Provider>

<Provider store={store}>
  <PersistGate>
    <CounterDisplay /> 👍

    <CounterButton />
  </PersistGate>
</Provider>

<Provider store={store}>
  <PersistGate>
    <>
      <Counter /> 👍
    </>
  </PersistGate>
</Provider>
```
