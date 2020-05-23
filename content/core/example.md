# How to use @statirjs/core?

**1. Install @statirjs/core**

```shell
npm i @statirjs/core
```

**2. Create forme**

```js
import { createForme } from "@statirjs/core";

const testForme = createForme(
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

**3. Init store**

```js
import { initStore } from "@statirjs/core";

const store = initStore({
  forms: {
    testForme,
  },
});

type IState = typeof store.state;
```

**4. Subscribe to store state changes**

```js
function listner(state: IState) {
  console.log(state);
}

store.subscribe(listner);
```

**5. Now you can dispatch actions and see how listner will calls**

```js
store.dispatch.testForme.increment();
```
