# Quick start

1. install [**@statirjs/core**](/content/core/home.md), [**@statirjs/react**](/content/react/home.md) and **@statirjs/persist**

```shell
npm i @statirjs/core @statirjs/react @statirjs/persist
```

2. init [**forme**](/content/core/formes.md)

```js
import { createForme } from "@statirjs/core";

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
```

3. create persist [**upgrade**](/content/persist/upgrade.md)

```js
import { createPersistUpgrade } from "@statirjs/persist";

const persistUpgrage = createPersistUpgrade({
  storage: window.localStorage,
});
```

4. init [**store**](/content/core/store.md) with **upgrade**

```js
import { initStore } from "@statirjs/core";

const store = initStore({
  formes: {
    counter,
  },
  upgrades: [persistUpgrage],
});
```

5. wrap your components with [**PersistGate**](/content/persist/persist_gate.md)

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

6. check localStorage content
