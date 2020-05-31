# Store

#### Description

**"Store"** in **@statirjs/core** it is main js object included **dispatch**, **subscribe** and state getter functionality

#### Declaration

**Store** is created by factory function **initStore** with one parameter

```js
import { createForme, initStore } from "@statirjs/core";

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
  forms: {
    counter,
  },
});
```

> **NOTE:** for [**typescript**](https://www.typescriptlang.org/) it is possible to declare **store** elements type for better typing
>
> ```js
> type IStore = typeof store;
> type IState = typeof store.state;
> type IDispatch = typeof store.dispatch;
> ```

#### Arguments

1. **store** require config object

```js
const store = initStore({ ... });
```

2. config include required [**forms**](/content/core/forms.md) property. **Forms** it is dictionary of **createForme** result objects

```js
const store = initStore({ 👍
  forms: {
    counter,
  },
});

const store = initStore({ 👍
  forms: {
    "incrementor": counter,
  },
});
```

3. config also have optional [**middlewares**](/content/core/middlewares.md) property. **Middlewares** must be an array

```js
const store = initStore({ 👍
  ...
  middlewares: []
});

const store = initStore({ 👍
  ...
  middlewares: [exampleMiddleware]
});
```

4. config also have optional [**upgrades**](/content/core/upgrades.md) property. **Upgrades** must be an array

```js
const store = initStore({ 👍
  ...
  upgrades: []
});

const store = initStore({ 👍
  ...
  upgrades: [exampleUpgrade]
});
```

#### State

User can get actual **store** state. All **forms** states divided by name as specified in config

```js
const stateCount = store.state.counter.count;
```

#### Dispatch

User can use **actions** and **pipes** by **dispatch**. All **forms** activity divided by name as specified in config

```js
store.dispatch.counter.increment();
```

Also it is possible provide **dispatch** to **forme builder** and use external **forms** **actions** and **pipes** in any other **forms** **pipes**

```js
const anotherForme = createForme(
  ... ,
  (dispatch: Dispatch) => ({
    pipes: {
      anotherPipe: {
        push(state) {
          dispatch.counter.increment();
          return state;
        },
      }
    },
  })
);
```

#### Listeners

For listen changes in store user must use **subscribe** functionality of **store**. **Listener** provided to store it is function that get root state object. **Listener** will calls each time when dispatch called

```js
type Listener = (rootState: RootState) => void;

store.subscribe(console.log);
```
