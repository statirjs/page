# Upgrade

#### Description

**"Persist upgrade"** in **@statirjs/persist** used to extend [**store**](/content/core/store.md) creation process. [**Upgrade**](/content/core/upgrades.md) will add [**listener**](/content/core/store.md) to **store** for saving data to storage and add **persist** [**forme**](/content/core/formes.md) with init [**pipe**](/content/core/formes.md)

#### Declaration

**Persist upgrade** must be created by factory

```js
import { createPersistUpgrade } from "@statirjs/persist";

const persistUpgrage = createPersistUpgrade({
  storage: window.localStorage,
});
```

#### Arguments

**Upgrade** function receive one argument - config:

```js
const config: Config = {
  name: "persit", // key in storage
  storage: window.localStorage, // storage object that satisfied Web Storage API
  whitelist: ["key1", "key2"], // names of formes that will be saved and restored from storage
  blacklist: ["key3", "key4"], // names of formes that will not be saved and restored from storage
};
```

#### Common requirements

1. **name** it is optional value (default = "STATIRJS_PERSIST")

2. **storage** must satisfy subtype of **Storage** interface (Web Storage API it is subtype of **Storage**)

```js
interface Storage {
  getItem(key: string): Promise<string>;
  setItem(key: string, item: string): Promise<void>;
  removeItem(key: string): Promise<void>;
}
```

3. **whitelist** it is optional value (default = [ ]). must be array of **formes** names that will be saved to **storage**

```js
const config = {
  ...,
  whitelist: ['key1', 'key2']
}

const store = initStore({
  formes: {
    key1: { ... },
    key2: { ... },
    key3: { ... },
  },
  ...
});

// Storage data console 👍
// data = {
//   key1: { ... },
//   key2: { ... },
// }
```

```js
const config = {
  ...,
  whitelist: []
}

const store = initStore({
  formes: {
    key1: { ... },
    key2: { ... },
    key3: { ... },
  },
  ...
});

// Storage data console 👍
// data = {
//   key1: { ... },
//   key2: { ... },
//   key3: { ... },
// }
```

4. **blacklist** it is optional value (default = [ ]). must be array of **formes** names that will not be saved to **storage**

```js
const config = {
  ...,
  blacklist: ['key1', 'key2']
}

const store = initStore({
  formes: {
    key1: { ... },
    key2: { ... },
    key3: { ... },
  },
  ...
});

// Storage data console 👍
// data = {
//   key3: { ... },
// }
```

```js
const config = {
  ...,
  whitelist: []
}

const store = initStore({
  formes: {
    key1: { ... },
    key2: { ... },
    key3: { ... },
  },
  ...
});

// Storage data console 👍
// data = {
//   key1: { ... },
//   key2: { ... },
//   key3: { ... },
// }
```

5. **whitelist** determinate **formes** keys before **blacklist**

```js
const config = {
  ...,
  whitelist: ['key1', 'key2'],
  blacklist: ['key1']
}

const store = initStore({
  formes: {
    key1: { ... },
    key2: { ... },
    key3: { ... },
  },
  ...
});

// Storage data console 👍
// data = {
//   key2: { ... },
// }
```
