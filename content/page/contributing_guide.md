# Contributing guide

#### Development

1. fork, then clone the repo

```shell
git clone https://github.com/statirjs/page
```

2. serve page localy with [**docsify**](https://docsify.js.org/#/?id=docsify)

```shell
npm run docsify:serve
```

3. fix bug or implement feature

4. check the [**style rulse**](#style-rules)

5. check the [**common requirements**](#common-requirements)

#### Style rules

1. no dots

2. numeric/dots list items start from lower case

3. new lines start from upper case

4. use article titles

5. use two types of emoji: 👍 and 👎

6. show links to external resources with bold text

7. show links to internal resources with bold text if they meet for the first time

8. distinguish important notations with bold text

#### Common requirements

1. don't forget to change related file: top home.md

2. don't forget to change related file: local home.md

3. don't forget to change related file: top \_sidebar.md

#### Sending a pull request

For new feature or bug fix, please open an issue with a proposal (do not forget code refactoring, stylings and tests). Main steps to create pull request:

1. fork the repo

2. create a new feature branch based off the master branch

3. make sure all tests pass and there are no linting errors

4. submit a pull request, referencing any issues it addresses

5. please try to keep your pull request focused in scope and avoid including unrelated commits
