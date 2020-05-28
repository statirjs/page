# Middlewares

#### Description

**"Middlewares"** in @statirjs/core used to extend [**action**](/content/core/forms.md) and [**pipes**](/content/core/forms.md). Its mean that **middlewares** resolve situation when user need extra functionality after **action**/**pipes** was called but before **store** change state and [**listeners**](/content/core/store.md) will be called.

#### Declaration

**Middleware** it is function that return function, that's all.

```js
function testMiddleware(next: UpdateState): UpdateState {
  return function (update: Update) {
    next(update);
  };
}
```

#### Cases

For example we have inited **store** with few **forms** and we want logging state changes. We can subscribe to **store** but this not give us any extra information about **forms**, **actions** and **pipes** that fire changes. For this cases **middlewares** exist.

```js
const isLoggable = console && console.table;

function logUpdate(update: Update) {
  console.table(update);
}

function testMiddleware(next: UpdateState): UpdateState {
  return function (update: Update) {
    logUpdate(update);
    next(update);
  };
}
```

#### Requirements

1. **Middleware** must be provided to **store** config

```js
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

const store = initStpre({
  forms: {
    counter,
  },
  middlewares: [ ... ] // <- Here place middlewares
});
```

2. **Middleware** function must take **next** argument

```js
function exampleMiddleware(next: UpdateState) {
  ...
}
```

3. **Next** it is function that take **update** object

```js
export type UpdateState = (update: Update) => void;
```

4. **Update** it is plain js object that have all needed data to update store state

```js
store.dispatch.counter.increment();

let update: Update = {
  state: {
    count: 1, // Forme state returned after actions or pipes steps
  },
  rootState: {
    counter: { count: 0 }, // Root state before updating
  },
  formeName: "counter", // Name of forme called by dispatch
  actionName: "increment", // Name of pipes or actions called by dispatch
};
```

5. **Middleware** must return function with **next**-like signature

```js
function exampleMiddleware(next: UpdateState): UpdateState {
  return function(update: Update) {
    ...
  }
}
```

6. **Middleware** result function must call **next** in body

```js
function exampleMiddleware(next: UpdateState): UpdateState {
  return function (update: Update) {
    next(update);
  };
}
```

#### Underhood

When **initStore** will be called then **middlewares** will be chained by closure

```js
function logMiddleware(next: UpdateState): UpdateState {
  ...
}

function devMiddleware(next: UpdateState): UpdateState {
  ...
}

const store = initStpre({
  ...
  middlewares: [logiddleware, devMiddleware]
});

store.dispatch.counter.increment();
```

In simplified pseudo implementation it will be like this

```js
let rootState = { ... }

  // Simplified @statirjs/core internal function
function internalUpdateFunction({ state, formeName }: Update) {
  const nextState = {
    ...rootState,
    [formeName]: state,
  };

  rootState = nextState;
}

const update = {
  state: counter.actions.increment(),
  rootState,
  formeName: "counter",
  actionName: "increment",
}

devMiddleware(logiddleware(internalUpdateFunction))(update);
```
