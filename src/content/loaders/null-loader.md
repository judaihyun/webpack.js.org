---
title: null-loader
source: https://raw.githubusercontent.com/webpack-contrib/null-loader/master/README.md
edit: https://github.com/webpack-contrib/null-loader/edit/master/README.md
repo: https://github.com/webpack-contrib/null-loader
---


[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![tests][tests]][tests-url]
[![chat][chat]][chat-url]



A webpack loader that returns an empty module.

One use for this loader is to silence modules imported by a dependency. Say, for
example, your project relies on an ES6 library that imports a polyfill you don't
need, so removing it will cause no loss in functionality.

## Requirements

This module requires a minimum of Node v6.9.0 and Webpack v4.0.0.

## Getting Started

To begin, you'll need to install `null-loader`:

```console
$ npm install null-loader --save-dev
```

Then add the loader to your `webpack` config. For example:

```js
// webpack.config.js
const path = require('path');

module.exports = {
  module: {
    rules: [
      {
        // Test for a polyfill (or any file) and it won't be included in your
        // bundle
        test: path.resolve(__dirname, 'node_modules/library/polyfill.js'),
        use: 'null-loader'
      }
    ]
  }
}
```

And run `webpack` via your preferred method.

## Contributing

Please take a moment to read our contributing guidelines if you haven't yet done so.

#### [CONTRIBUTING](https://raw.githubusercontent.com/webpack-contrib/null-loader/master/.github/CONTRIBUTING)

## License

#### [MIT](https://raw.githubusercontent.com/webpack-contrib/null-loader/master/LICENSE)

[npm]: https://img.shields.io/npm/v/null-loader.svg
[npm-url]: https://npmjs.com/package/null-loader

[node]: https://img.shields.io/node/v/null-loader.svg
[node-url]: https://nodejs.org

[deps]: https://david-dm.org/webpack-contrib/null-loader.svg
[deps-url]: https://david-dm.org/webpack-contrib/null-loader

[tests]: 	https://img.shields.io/circleci/project/github/webpack-contrib/null-loader.svg
[tests-url]: https://circleci.com/gh/webpack-contrib/null-loader

[cover]: https://codecov.io/gh/webpack-contrib/null-loader/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/null-loader

[chat]: https://img.shields.io/badge/gitter-webpack%2Fwebpack-brightgreen.svg
[chat-url]: https://gitter.im/webpack/webpack