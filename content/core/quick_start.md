# Quick start

1. Install @statirjs/core

```shell
npm i @statirjs/core
```

1. Create a [**forme**](/content/core/forms.md)

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

1. Initiate a [**store**](/content/core/store.md)

```js
import { initStore } from "@statirjs/core";

const store = initStore({
  forms: {
    testForme,
  },
});

type IState = typeof store.state;
```

1. Subscribe to **store** state changes

```js
function listner(state: IState) {
  console.log(state);
}

store.subscribe(listner);
```

1. Now you can [**dispatch**](/content/core/store.md) actions and see how [**listeners**](/content/core/store.md) will be calls

```js
store.dispatch.testForme.increment();
```
