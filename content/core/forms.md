# Forms

#### Description

**"Forme"** in @statirjs/core is a base unit for build functional pice of store. User must use **createForme** function to create [**store**](/content/core/store.md) compatibility object.

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

> **_NOTE:_** If you use [**typescript**](https://www.typescriptlang.org/) then for more convenience in future (when you will select data from [**store**](/content/core/store.md) and etc) you can pass state forme as typed object.
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
> const testForme = createForme(state, () => ({
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

1. local **forme** state object (**forme state**)

2. function that return object with **actions** and **pipes** (**forme builder**)

#### Forme state

**Forme state** is required argument and must be an plain object

```js
const formeState = {
  count: 0,
};
```

#### Forme builder

**Forme builder** is not required argument

#### Actions

**Actions** is simple sync functions for store state update.

1. **actions** and **pipes** is also not required property

2. **actions** and **pipes** must be an plain objects

3. **action** in **actions** must be a function

4. **action** must return next **forme state** object for parent **forme**

5. **action** receive two argument: current **forme state**, payload

> **_NOTE:_** for [**typescript**](https://www.typescriptlang.org/) is no need to define **action** first parameter **forme state** type (it will used form first **createForme** argument type)

```js
const actions = {
  identity1(state) {
    return state; // you can return state as is
  }, // Ok
  identity2(state) {
    return {
      ...state, // or new object, for @statirjs/core is no different
    };
  }, // Ok
  increment(state) {
    return {
      ...state,
      count: state.count + 1,
    };
  }, // Ok
  add(state, payload: number) {
    return {
      ...state,
      count: state.count + payload,
    };
  }, // Ok
  reset() {
    return {
      count: 0,
    };
  }, // Ok
  errorAction1() {
    console.log("");
  }, // Error
};
```

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

12. **pipe** steps must be an functions
