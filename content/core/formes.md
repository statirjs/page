# Formes

#### Description

**"Forme"** in **@statirjs/core** is a base unit for build independent pice of [**store**](/content/core/store.md)

#### Declaration

**Forme** is created by factory function **createForme** with two parameters

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

> **NOTE:** If you use [**typescript**](https://www.typescriptlang.org/) then for more convenience in future (when you will be select data from [**store**](/content/core/store.md) and etc) you can pass state forme as typed object
>
> ```js
> interface ICounterState {
>   count: number;
> }
>
> const state: ICounterState = {
>   count: 0,
> };
>
> const counter = createForme(state, () => ({
>   actions: {
>     increment(state) {
>       return {
>         ...state,
>         count: state.count + 1,
>       };
>     },
>   },
> }));
> ```

#### Arguments

**createForme** function receive two arguments:

1. required local **forme** state plain object === **forme state**

```js
const counterState = {
  count: 0,
};

const counter = createForme(counterState);
```

2. not required function that return object with **actions** and **pipes** === **forme builder**

```js
const counterBuilder = () => ({
    actions: { ... },
    pipes: { ... }
})

const counter = createForme(counterState, counterBuider);
```

#### Dispatch

For connection **formes** between each other user can define [**dispatch**](/content/core/store.md) as first parameter in **forme builder** and then calls **pipes** and **actions** of others **formes**

```js
const counter = createForme(
  ... ,
  (dispatch: Dispatch) => ({
    actions: { ... },
  })
);
```

#### Actions requirements

**Actions** it is strictly synchronous js functions for store state update

1. **actions** and **pipes** it is not required property

```js
const counterBuilder = () => ({}); 👍
```

2. **actions** and **pipes** must be an plain objects

```js
const counterBuilder = () => ({ 👍
  actions: { ... },
  pipes: { ... },
});
```

3. **action** in **actions** must be a function

```js
const counterBuilder = () => ({ 👍
  actions: {
    increment() { ... }
  },
});
```

4. **action** must return next **forme state** object for **forme**

```js
const counterBuilder = () => ({ 👍
  actions: {
    resetState() {
      return {
        count: 0
      };
    }
  },
});

const counterBuilder = () => ({ 👎
  actions: {
    consoleHello() {
      console.log('Hello @statirjs/core!);
    }
  },
});
```

5. **action** receive two arguments: current **forme state** and payload

```js
const counterBuilder = () => ({ 👍
  actions: {
    tap(state) {
      return state;
    }
  },
});

const counterBuilder = () => ({ 👍
  actions: {
    addNumber(state, payload: number) {
      return {
        ...state,
        count: state.count + payload
      };
    }
  },
});
```

> **NOTE:** for [**typescript**](https://www.typescriptlang.org/) it is no need to define **action** first parameter type (it will extracted from first **createForme** argument)

#### Pipes requirements

**Pipes** in **forme** used to describe asynchronous actions divided into specific steps. All **pipe's** catch an errors in **core** (and **done**) step where user can do (as suggested in **@statirjs/core** design) some asynchron work. User can write asynchronous **pipes** with native js async/await or Promise functionality

1. **pipe** in **pipes** must be a plain object

```js
const counterBuilder = () => ({ 👍
  pipes: {
    incrementAsync: { ... }
  },
});
```

2. **pipe** divided in four steps: **push**, **core**, **done**, **fail**

```js
const counterBuilder = () => ({ 👍
  pipes: {
    incrementAsync: {
      push() { ... },
      core() { ... },
      done() { ... },
      fail() { ... },
    }
  },
});
```

3. only **push** step in **pipe** is required

```js
const counterBuilder = () => ({ 👍
  pipes: {
    incrementAsync: {
      push() { ... },
    }
  },
});

const counterBuilder = () => ({ 👎
  pipes: {
    incrementAsync: {
      done() { ... },
    }
  },
});
```

4. all steps may be sync or async, but pipe in [**dispatch**](/content/core/store.md) anyway will be async

```js
const counterBuilder = () => ({ 👍
  pipes: {
    incrementFalsyAsync: {
      push() { ... },
      core() { ... },
      done() { ... },
      fail() { ... },
    }
  },
});

store.dispatch.counter.incrementFalsyAsync() // () => Promise<void>

const counterBuilder = () => ({ 👍
  pipes: {
    incrementTrulyAsync: {
      async push() { ... },
      async core() { ... },
      async done() { ... },
      async fail() { ... },
    }
  },
});

store.dispatch.counter.incrementTrulyAsync() // () => Promise<void>
```

5. **pipe** steps order: first **push**, second **core**, if runtime error throw in **core** then **fail** otherwise **done**

```js
const counterBuilder = () => ({ 👍
  pipes: {
    incrementAsync: {
      push() { ... },
      core() {
        return 'Example data!';
      },
      done() { ... },
      fail() { ... },
    }
  },
});

// Exacution order
// 'push'
// 'core'
// 'done'

const counterBuilder = () => ({ 👍
  pipes: {
    incrementAsync: {
      push() { ... },
      core() {
        throw new Error('Example error!');
      },
      done() { ... },
      fail() { ... },
    }
  },
});

// Exacution order
// 'push'
// 'core'
// 'fail'
```

6. **push** receive two arguments: current **forme state** and payload. **Push** must return next **forme state** object for **forme**

```js
const counterBuilder = () => ({ 👍
  pipes: {
    addAsync: {
      push(state: payload: number) {
        return {
          ...state,
          count: state.count + payload
        }
      },
    }
  },
});
```

7. **core** receive two arguments: current **forme state** and payload. **Core** may return any data

```js
const counterBuilder = () => ({ 👍
  pipes: {
    addAsync: {
      push(state, payload: number) { ... },
      core(state, payload: number) {
        return 'Example data!';
      }
    }
  },
});

const counterBuilder = () => ({ 👍
  pipes: {
    addAsync: {
      push(state, payload: number) { ... },
      core(state, payload: number) {
        return null;
      }
    }
  },
});
```

8. **done** receive three arguments: current **forme state**, payload, and third argument as data that **pipe** get from **core** step. **Done** must return next **forme state** object for **forme**

```js
const counterBuilder = () => ({ 👍
  pipes: {
    addAsync: {
      push(state, payload: number) { ... },
      core(state, payload: number) {
        return 'Example data!';
      },
      done(state, payload: number, data: string) {
        return state;
      }
    }
  },
});
```

9. **fail** receive three arguments: current **forme state**, payload, and third argument as error that **pipe** get from **core** step. **Fail** must return next **forme state** object for **forme**

```js
const counterBuilder = () => ({ 👍
  pipes: {
    addAsync: {
      push(state, payload: number) { ... },
      core(state, payload: number) {
        throw new Error('Example error!');
      },
      fail(state, payload: number, error: Error) {
        return state;
      }
    }
  },
});
```

10. as you see above, payload it is same value for all steps

```js
const counterBuilder = () => ({ 👍
  pipes: {
    addAsync: {
      push(state, payload: number) { ... }, // payload = 1
      core(state, payload: number) { ... }, // payload = 1
      done(state, payload: number, data: any) { ... } // payload = 1
      fail(state, payload: number, error: Error) { ... } // payload = 1
    }
  },
});

store.dispatch.counter.addAsync(1)
```

#### Underhood

**createForme** function parse arguments into object compatible with **store**

```js
interface ReFormeBuilder<
  T extends State = State,
  K extends Forme<T> = Forme
> {
  state: T;
  getForme(
    rootState: RootState,
    dispatch: Dispatch,
    updateState: UpdateState,
    formeName: string
  ): ReForme<K>;
}
```
