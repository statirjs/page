# Quick start

#### Common case

1. install **@statirjs/core**

```shell
npm i @statirjs/core
```

2. create a [**forme**](/content/core/formes.md)

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
  formes: {
    counter,
  },
});
```

4. subscribe to **store** state changes

```js
function listener(state: IState) {
  console.log(state);
}

store.subscribe(listener);
```

5. now you can [**dispatch**](/content/core/store.md) actions and see how [**listeners**](/content/core/store.md) will be calls

```js
store.dispatch.counter.increment();
```

#### Enhanced case

1. install **@statirjs/core**

2. create a **forme**

3. create a [**middleware**](/content/core/middlewares.md)

```js
function exampleMiddleware(next: UpdateState): UpdateState {
  return function (update: Update) {
    next(update);
  };
}
```

4. create [**upgrade**](/content/core/upgrades.md)

```js
function exampleUpgrade(next: CreateStore): CreateStore {
  return function (config: Config) {
    return next(config);
  };
}
```

5. initiate a **store**

```js
import { initStore } from "@statirjs/core";

const store = initStore({
  formes: {
    counter,
  },
  middlewares: [exampleMiddleware],
  upgrades: [exampleUpgrade],
});
```

6. subscribe to **store** state changes

7. now you can **dispatch** actions and see how **listeners** will be calls
