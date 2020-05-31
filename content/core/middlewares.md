# Middlewares

#### Description

**"Middlewares"** in **@statirjs/core** used to extend [**action**](/content/core/forms.md) and [**pipes**](/content/core/forms.md). It is mean that **middlewares** resolve situation when user need extra functionality after **action**/**pipes** was called but before **store** change state and [**listeners**](/content/core/store.md) will be called

#### Declaration

**Middleware** it is function that return function, that's all

```js
function exampleMiddleware(next: UpdateState): UpdateState {
  return function (update: Update) {
    next(update);
  };
}
```

#### Cases

For example we have inited **store** with few **forms** and we want logging state changes. We can subscribe to **store** but this not give us any extra information about **forms**, **actions** and **pipes** that fire changes. For this cases **middlewares** exist

```js
const isLoggable = console && console.table;

function logUpdate(update: Update) {
  isLoggable && console.table(update);
}

function exampleMiddleware(next: UpdateState): UpdateState {
  return function (update: Update) {
    logUpdate(update);
    next(update);
  };
}
```

#### Arguments

**Middleware** function receive one argument:

1. **middleware** function must take **next** argument

```js
function exampleMiddleware(next: UpdateState) { ... } 👍
```

2. **next** it is function that take **update** object

```js
export type UpdateState = (update: Update) => void; 👍
```

3. **update** it is plain js object that have all needed data to update store state

```js
store.dispatch.counter.increment();

const update: Update = {
  state: { count: 1 }, // forme state returned after actions or pipes steps
  rootState: {
    counter: { count: 0 }, // root state before updating
  },
  formeName: "counter", // name of forme called by dispatch
  actionName: "increment", // name of pipes or actions called by dispatch
};
```

#### Common requirements

1. **middleware** must be provided to **store** config

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

const store = initStore({ 👍
  forms: {
    counter,
  },
  middlewares: [ ... ] // <- Here place middlewares
});
```

2. **middleware** must return function with **next**-like signature

```js
function exampleMiddleware(next: UpdateState): UpdateState { 👍
  return function(update: Update) { ... }
}
```

3. **middleware** result function must call **next** in body

```js
function exampleMiddleware(next: UpdateState): UpdateState { 👍
  return function (update: Update) {
    next(update);
  };
}

function exampleMiddleware(next: UpdateState): UpdateState { 👍
  return function (update: Update) {
    return next(update);
  };
}

function exampleMiddleware(next: UpdateState): UpdateState { 👎
  return function (update: Update) {
    console.log(next, update);
  };
}
```

#### Underhood

When **initStore** will be called then **middlewares** will be chained (from zero index to last) by closure

```js
function logMiddleware(next: UpdateState): UpdateState { ... }

function devMiddleware(next: UpdateState): UpdateState { ... }

const store = initStore({
  ...
  middlewares: [logiddleware, devMiddleware]
});

store.dispatch.counter.increment();
```

In simplified pseudo implementation it will be like this

```js
const rootState = { ... }

// Simplified @statirjs/core internal function
function internalUpdateFunction({ state, formeName }: Update) {
  const nextState = {
    ...rootState,
    [formeName]: state,
  };

  Object.assign(rootState, nextState)
}

const update = {
  state: counter.actions.increment(),
  rootState,
  formeName: "counter",
  actionName: "increment",
}

devMiddleware(logMiddleware(internalUpdateFunction))(update);
```
