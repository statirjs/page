# Upgrades

#### Description

**"Upgrade"** in **@statirjs/core** is used to extend [**store**](/content/core/store.md) creation proccess. It means that with **upgrades** user can change **store** init config before **store** is initiated and do some extra check/work in pre-init stage

#### Declaration

**Upgrade** is function that returns function, that's all

```js
function exampleUpgrade(next: CreateStore): CreateStore {
  return function (config: Config) {
    return next(config);
  };
}
```

#### Cases

For example we have [**react**](https://reactjs.org/) app with **@statirjs/core** on board and we want to use [**redux-devtool**](https://github.com/reduxjs/redux-devtools) as usual. To connect **@statirjs/core** to **devtool** user can use **upgrade** mechanism

```js
function reduxDevtoolsUpgrade(next: CreateStore): CreateStore {
  return function (config: Config) {
    if (window && window.__REDUX_DEVTOOLS_EXTENSION__) {
      const devtools = window.__REDUX_DEVTOOLS_EXTENSION__.connect();
      const middleware = createReduxDevtoolsMiddleware(devtools);

      const nextConfig: Config = {
        ...config,
        middlewares: [...(config.middlewares || []), middleware],
      };

      const store = next(nextConfig);
      devtools.init(store.state);
      return store;
    }

    return next(config);
  };
}
```

> **NOTE:** **redux-devtool** is built-in **@statirjs/core** **upgrade** and it is not needed to connect this functionality by hand

#### Arguments

**Upgrade** function receives one argument:

1. **upgrade** function must take **next** argument

```js
function exampleUpgrade(next: CreateStore) { ... } 👍
```

2. **next** is function that takes **config** object

```js
type CreateStore = (config: Config) => Store; 👍
```

3. **config** is init **store** config object

```js
const config: Config = {
  formes: {}, // formes object
  upgrades: [], // array of upgrades
  middlewares: [], // array of middlewares
};
```

#### Common requirements

1. **upgrade** must be provided to **store** config

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
  formes: {
    counter
  },
  upgrades: [ ... ] // <- Here place upgrades
});
```

2. **upgrade** must return function with **next**-like signature

```js
function exampleUpgrade(next: CreateStore): CreateStore { 👍
  return function(config: Config) { ... }
}
```

3. function returned from **upgrade** must call **next** in body and return inited store

```js
function exampleUpgrade(next: CreateStore): CreateStore { 👍
  return function (config: Config): Store {
    return next(config);
  };
}

function exampleUpgrade(next: CreateStore): CreateStore { 👍
  return function (config: Config): Store {
    if ( ... ) {
      return next(config);
    } else {
      return next(config);
    }
  };
}

function exampleUpgrade(next: CreateStore): CreateStore { 👎
  return function (config: Config): Store {
    console.log(next, config);
  };
}

function exampleUpgrade(next: CreateStore): CreateStore { 👎
  return function (config: Config): Store {
    next(config);
  };
}
```

#### Underhood

When **initStore**is called then **upgrades** will be chained (from zero index to last) by closure. In simplified pseudo implementation it will be like this

```js
function devtoolUpgrade(next: CreateStore): CreateStore { ... }

function persistUpgrade(next: CreateStore): CreateStore { ... }

// Simplified @statirjs/core internal function
function createStore(config: Config): Store {
  const { state, dispatch } = createBlankStore(config.formes);
  updateDispatch(dispatch, config.middlewares);
  return {
    state,
    dispatch,
  };
}

const config = {
  formes: {
    counter
  },
  upgrades: [devtoolUpgrade, persistUpgrade]
};

const store = persistUpgrade(devtoolUpgrade(createStore))(config);
```
