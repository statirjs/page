# Store

#### Description

**"Store"** in **@statirjs/core** is main js object including **dispatch**, **subscribe** and state data

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
  formes: {
    counter,
  },
});
```

> **NOTE:** for [**typescript**](https://www.typescriptlang.org/) it is possible to declare **store** element type for better typing
>
> ```js
> type IStore = typeof store;
> type IState = typeof store.state;
> type IDispatch = typeof store.dispatch;
> ```

#### Arguments

1. **store** requires config object

```js
const store = initStore({ ... });
```

2. config includes required [**formes**](/content/core/formes.md) property. **Formes** are dictionary of **createForme** result objects

```js
const store = initStore({ 👍
  formes: {
    counter,
  },
});

const store = initStore({ 👍
  formes: {
    "incrementor": counter,
  },
});
```

3. config also has optional [**middlewares**](/content/core/middlewares.md) property. **Middlewares** must be an array

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

4. config also has optional [**upgrades**](/content/core/upgrades.md) property. **Upgrades** must be an array

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

User can get actual **store** state. All **formes** states are divided by name as specified in config

```js
const stateCount = store.state.counter.count;
```

#### Dispatch

User can use **actions** and **pipes** by **dispatch**. All **formes** activity are divided by name as specified in config

```js
store.dispatch.counter.increment();
```

> **NOTE:** user can use **actions** and **pipes** with async/await as default js functions
>
> ```js
> console.log("Before pipe");
> await store.dispatch.someForme.somePipe();
> console.log("After pipe");
> console.log("Before action");
> store.dispatch.counter.increment();
> console.log("After action");
>
> // Console log:
> // Before pipe
> // After pipe
> // Before action
> // After action
> ```

Also it is possible to provide **dispatch** to **forme builder** and use external **formes** **actions** and **pipes** in any other **formes** **pipes**

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

For listen changes in **store** user must use **subscribe** functionality of **store**. **Listener** provided to store is a function that gets root state object. **Listener** will be called each time when dispatch called

```js
type Listener = (rootState: RootState) => void;

store.subscribe(console.log);
```
