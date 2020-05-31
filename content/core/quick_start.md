# Quick start

1. install @statirjs/core

```shell
npm i @statirjs/core
```

2. create a [**forme**](/content/core/forms.md)

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

3. initiate a [**store**](/content/core/store.md)

```js
import { initStore } from "@statirjs/core";

const store = initStore({
  forms: {
    counter,
  },
});

type IState = typeof store.state;
type IDispatch = typeof store.dispatch;
```

4. subscribe to **store** state changes

```js
function listner(state: IState) {
  console.log(state);
}

store.subscribe(listner);
```

5. now you can [**dispatch**](/content/core/store.md) actions and see how [**listeners**](/content/core/store.md) will be calls

```js
store.dispatch.counter.increment();
```
