# Forms

#### Description

**"Forme"** in @statirjs/core is a base unit for build independent pice of [**store**](/content/core/store.md). User must use **createForme** function to create object full of needed actions and compatible with **store**.

#### Declaration

**Forme** it's just factory function with two params.

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

> **_NOTE:_** If you use [**typescript**](https://www.typescriptlang.org/) then for more convenience in future (when you will be select data from [**store**](/content/core/store.md) and etc) you can pass state forme as typed object.
>
> ```js
> import { createForme } from "@statirjs/core";
>
> export interface ITestFormeState {
>   count: number;
> }
>
> const state: ITestFormeState = {
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
const counterBuilder = ( ... ) => ({
    actions: {
      ...
    },
    pipes: {
      ...
    }
})

const counter = createForme(counterState, counterBuider);
```

#### Dispatch

For connection **forms** between each other user can define [**dispatch**](/content/core/store.md) as first parameter in **forme builder** and then calls **pipes** and **actions** of others **formes**.

```js
const counter = createForme(
  ... ,
  (dispatch: Dispatch) => ({
    actions: {
      ...
    },
  })
);
```

#### Actions

**Actions** it is strictly synchronous js functions for store state update.

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

5. **action** receive two argument: current **forme state** and payload

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

> **_NOTE:_** for [**typescript**](https://www.typescriptlang.org/) it is no need to define **action** first parameter type (it will extracted from first **createForme** argument)

#### Pipes

**Pipes** in **forme** used to describe asynchronous actions divided into specific steps. All **pipe's** will catch error in **core** (and **done**) step where user can do (as suggested in @statirjs/core design) some async work. User can write asynchronous actions with native js async/await or Promise functionality.

1. **pipe** in **pipes** must be an plain object

2. **pipe** divided in four steps: **push**, **core**, **done**, **error**

3. all steps may be sync or async, but pipe in [**dispatch**](/content/core/store.md) anyway will be async

4. **pipe** steps order: **push**, **core**, if runtime error throw then **fail** otherwise **done**

![pipe](https://raw.githubusercontent.com/statirjs/page/master/content/core/images/pipe.png)

5. **push** step have **action** signature

```js
const pipes = {
  testPipe: {
    push(state, payload: number) {
      console.log(payload); // 1

      return {
        ...state,
        count: state.count + payload,
      };
    },
  },
};
```

6. **core** step have arguments as **action**, but can return any data

7. pipe send **core** step return data to **done** as third argument

```js
const pipes = {
  testPipe: {
    push(state, payload: number) {
      console.log(payload); // 1

      return {
        ...state,
        count: state.count + payload,
      };
    },
    core(state, payload: number) {
      console.log(payload); // 1

      return {
        data: [1, 2, 3],
      };
    },
  },
};
```

8. **done** step have **action** signature and third argument as data that pipe get from **core** step

```js
const pipes = {
  testPipe: {
    push(state, payload: number) {
      console.log(payload); // 1

      return {
        ...state,
        count: state.count + payload,
      };
    },
    async core(state, payload: number) {
      console.log(payload); // 1

      await delay(500);

      return {
        data: [1, 2, 3],
      };
    },
    done(state, payload: number, data: number[]) {
      console.log(payload); // 1
      console.log(data); // [1, 2, 3]

      return state;
    },
  },
};
```

9. **fail** step have **action** signature and third argument as error object that was thrown

```js
const pipes = {
  testPipe: {
    async push(state, payload: number) {
      console.log(payload); // 1

      await delay(500);

      return {
        ...state,
        count: state.count + payload,
      };
    },
    core(state, payload: number) {
      console.log(payload); // 1

      throw new Error("Test error");
    },
    error(state, payload: number, error: Error) {
      console.log(payload); // 1
      console.log(error); // Error('Test error')

      return state;
    },
  },
};
```

10. as you see above payload is same value for all steps

11. only **push** step in **pipe** is required

#### Return value

**createForme** function parse arguments into compatible with [**store**](/content/core/store.md) object.

```js
export interface ReFormeBuilder<
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

If you want somehow enhance creation of **forms** or reshape default **forme** (add beside **actions** and **pipes** some selectors for example), then you must use [**plugins**](/content/core/plugins.md).
