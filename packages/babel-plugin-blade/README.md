<div align="center">
<h1>babel-plugin-blade ⛸️</h1>

<p>inline GraphQL</p>
</div>

<hr />

<!-- prettier-ignore-start -->
[![PRs Welcome][prs-badge]][prs]
[![Code of Conduct][coc-badge]][coc]
[![Babel Macro](https://img.shields.io/badge/babel--macro-%F0%9F%8E%A3-f5da55.svg?style=flat-square)](https://github.com/kentcdodds/babel-plugin-macros)
<!-- prettier-ignore-end -->

## The problem

This is a plugin for solving the [double declaration problem](https://babel-blade.netlify.com/docs/declarationdeclaration.html) in GraphQL queries.

> **What is the "double declaration problem"?** Simply it is the bad developer experience of first having to declare what you want to query in the GraphQL template string, and then _again_ when you are using the data in your application. Ommissions are confusing to debug and overfetching due to stale queries is also a problem.

## This solution

This plugin gives you `createQuery` and `createFragment` functions to wrap around the root `data` property of whatever GraphQL client you use. It then tracks everything you do with `data` and generates a GraphQL query based on your usage.

This is accomplished by hooking in to Babel to building up a tree of downstream dependencies on `data`. For query arguments, the arguments are stripped and an alias generated for that specific query.

## Table of Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Installation](#installation)
- [Usage](#usage)
  - [first usage style](#first-usage-style)
- [Configure with Babel](#configure-with-babel)
  - [Via `.babelrc` (Recommended)](#via-babelrc-recommended)
  - [Via CLI](#via-cli)
  - [Via Node API](#via-node-api)
- [Use with `babel-plugin-macros`](#use-with-babel-plugin-macros)
  - [APIs not supported by the macro](#apis-not-supported-by-the-macro)
- [Caveats](#caveats)
- [Examples](#examples)
- [Inspiration](#inspiration)
- [Other Solutions](#other-solutions)
- [LICENSE](#license)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Installation

This module is distributed via [npm][npm] which is bundled with [node][node] and
should be installed as one of your project's `devDependencies`:

```
npm install --save-dev babel-plugin-blade
```

## Usage

Add it to your babel config.

### first usage style

**Before**:

```javascript
import {Connect, query} from 'urql'

const movieQuery = createQuery()
const Movie = ({id, onClose}) => (
  <div>
    <Connect
      query={query(movieQuery, {id: id})}
      children={({data}) => {
        const DATA = movieQuery(data)
        return (
          <div>
            <h2>{DATA.movie.gorilla}</h2>
            <p>{DATA.movie.monkey}</p>
            <p>{DATA.chimp}</p>
          </div>
        )
      }}
    />
  </div>
)
```

**After**:

```javascript
import {Connect, query} from 'urql'

const Movie = ({id, onClose}) => (
  <div>
    <Connect
      query={query(
        `
query movieQuery{
  movie {
    gorilla
    monkey
  }
  chimp
}`,
        {id: id},
      )}
      children={({data}) => {
        const DATA = data
        return (
          <div>
            <h2>{DATA.movie.gorilla}</h2>
            <p>{DATA.movie.monkey}</p>
            <p>{DATA.chimp}</p>
          </div>
        )
      }}
    />
  </div>
)
```

---

#### [For more usage examples please see our full API docs here.](https://babel-blade.netlify.com/docs/graphql-spec.html)

---

## Configure with Babel

### Via `.babelrc` (Recommended)

**.babelrc**

```json
{
  "plugins": ["blade"]
}
```

### Via CLI

```sh
babel --plugins blade script.js
```

### Via Node API

```javascript
require('babel-core').transform('code', {
  plugins: ['blade'],
})
```

## Use with `babel-plugin-macros`

Once you've
[configured `babel-plugin-macros`](https://github.com/kentcdodds/babel-plugin-macros/blob/master/other/docs/user.md)
you can import/require the blade macro at `babel-plugin-blade/macro`. For
example:

```javascript
import { createQuery } from 'babel-plugin-blade/macro'
import {Connect, query} from 'urql'

const movieQuery = createQuery()
const Movie = () => (
  <div>
    <Connect
      query={query(movieQuery)}
      children={({data}) => {
        const DATA = movieQuery(data)
        return (
          <div>
            <h2>{DATA.movie.gorilla}</h2>
            <p>{DATA.movie.monkey}</p>
            <p>{DATA.chimp}</p>
          </div>
        )
      }}
    />
  </div>
)

      ↓ ↓ ↓ ↓ ↓ ↓

import { Connect, query } from 'urql';

const Movie = () => <div>
    <Connect query={query(`
query movieQuery{
  movie {
    gorilla
    monkey
  }
  chimp
}`)} children={({ data }) => {
    const DATA = data;
    return <div>
            <h2>{DATA.movie.gorilla}</h2>
            <p>{DATA.movie.monkey}</p>
            <p>{DATA.chimp}</p>
          </div>;
  }} />
  </div>;
```

### APIs not supported by the macro

- none! the macro has full coverage of the babel-plugin

> You could also use [`blade.macro`][blade.macro] if you'd prefer to type less 😀

## Caveats

This plugin is still very new, please don't use in production unless you are willing to help me out on fixing any bugs you find!

## Examples

- To be written

## Inspiration

This is based on [babel-plugin-macros](https://github.com/kentcdodds/babel-plugin-macros).

## Other Solutions

I'm not aware of any, if you are please [make a pull request][prs] and add it
here!

## LICENSE

MIT

<!-- prettier-ignore-start -->

[npm]: https://www.npmjs.com/
[node]: https://nodejs.org
[build-badge]: https://img.shields.io/travis/kentcdodds/babel-plugin-blade.svg?style=flat-square
[build]: https://travis-ci.org/kentcdodds/babel-plugin-blade
[coverage-badge]: https://img.shields.io/codecov/c/github/kentcdodds/babel-plugin-blade.svg?style=flat-square
[coverage]: https://codecov.io/github/kentcdodds/babel-plugin-blade
[version-badge]: https://img.shields.io/npm/v/babel-plugin-blade.svg?style=flat-square
[package]: https://www.npmjs.com/package/babel-plugin-blade
[downloads-badge]: https://img.shields.io/npm/dm/babel-plugin-blade.svg?style=flat-square
[npmtrends]: http://www.npmtrends.com/babel-plugin-blade
[license-badge]: https://img.shields.io/npm/l/babel-plugin-blade.svg?style=flat-square
[license]: https://github.com/kentcdodds/babel-plugin-blade/blob/master/LICENSE
[prs-badge]: https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square
[prs]: http://makeapullrequest.com
[donate-badge]: https://img.shields.io/badge/$-support-green.svg?style=flat-square
[coc-badge]: https://img.shields.io/badge/code%20of-conduct-ff69b4.svg?style=flat-square
[coc]: https://github.com/kentcdodds/babel-plugin-blade/blob/master/other/CODE_OF_CONDUCT.md
[glamorous]: https://github.com/paypal/glamorous
[preval]: https://github.com/kentcdodds/babel-plugin-preval
[blade.macro]: https://www.npmjs.com/package/blade.macro
[babel-plugin-macros]: https://github.com/kentcdodds/babel-plugin-macros

<!-- prettier-ignore-end -->
