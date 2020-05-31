# Contributing guide

#### Development

1. fork, then clone the repo

```shell
git clone https://github.com/statirjs/core
```

2. build **@statirjs/core** with [**rollup**](https://rollupjs.org/), it will create a CommonJS module-per-module build, a ES Modules build and a UMD build

```shell
npm run rollup:build
```

3. fix bug or implement feature

4. check the [**code style**](#code-styling)

5. check the [**tests**](#testing)

6. check the [**typescript typing**](#typescript)

7. check the [**common requirements**](#common-requirements)

8. then push changes to cloned repo

9. send the [**pull request**](#sending-a-pull-request)

> **NOTE:** in fact all this checks will be also executed on commit stage and pull request stage. Thanks to [**husky**](https://github.com/typicode/husky) and [**travis CI**](https://travis-ci.org/getting_started)

#### Typescript

1. use [**typescript**](https://www.typescriptlang.org/) for static type check

```shell
npm run typescript:check
```

2. use **typescript** to generate types files

```shell
npm run typescript:types
```

3. use **scripts/types.js** script to generate single index.d.ts

```shell
npm run scripts:types
```

#### Common requirements

1. use factory functions over constructor functions

2. functions must be divided to small

3. place types to ./src/typing/internal.ts

#### Size check

1. use [**size-limit**](https://github.com/ai/size-limit) for size check

```shell
npm run size:check
```

#### Testing

1. use [**jest**](https://jestjs.io/) tests

```shell
npm run jest:test
```

#### Code styling

1. use [**prettier**](https://prettier.io/) for formating

```shell
npm run prettier:check
```

2. use **prettier** for writing

```shell
npm run prettier:write
```

3. use [**eslint**](https://eslint.org/) for linting

```shell
npm run eslint:check
```

#### Sending a pull request

For new feature or bugs fix, please open an issue with a proposal (do not forget code refactoring, stylings and tests). Main steps to create pull request:

1. fork the repo

2. create a new feature branch based off the master branch

3. make sure all tests pass and there are no linting errors

4. submit a pull request, referencing any issues it addresses

5. please try to keep your pull request focused in scope and avoid including unrelated commits
