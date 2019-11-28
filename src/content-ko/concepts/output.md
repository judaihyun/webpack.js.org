---
title: Output
sort: 3
contributors:
  - JdevH
---

`output`옵션을 설정하면 컴파일된 파일(`bundles`)을 디스크에 쓰는 방법을 웹팩에게 알려주는 것입니다. `entry point`는 여러 곳일 수 있지만 `output`은 하나만 지정된 다는 것을 유의해주세요.


## Usage

웹팩 config에서 `output`프로퍼티에 대한 최소 요구 조건은 다음과 같이 [`output.filename`](https://webpack.js.org/configuration/output/#outputfilename) 설정 값을 Object로써 설정하는 것입니다.


__webpack.config.js__

```javascript
module.exports = {
  output: {
    filename: 'bundle.js',
  }
};
```

위 설정은 `dist`디렉토리에 단일 `bundle.js`파일로 출력하게 됩니다.



## Multiple Entry Points

If your configuration creates more than a single "chunk" (as with multiple entry points or when using plugins like CommonsChunkPlugin), you should use [substitutions](/configuration/output#output-filename) to ensure that each file has a unique name.

```javascript
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
};

// writes to disk: ./dist/app.js, ./dist/search.js
```


## Advanced

Here's a more complicated example of using a CDN and hashes for assets:

__config.js__

```javascript
module.exports = {
  //...
  output: {
    path: '/home/proj/cdn/assets/[hash]',
    publicPath: 'http://cdn.example.com/assets/[hash]/'
  }
};
```

In cases where the eventual `publicPath` of output files isn't known at compile time, it can be left blank and set dynamically at runtime via the `__webpack_public_path__` variable in the entry point file:

```javascript
__webpack_public_path__ = myRuntimePublicPath;

// rest of your application entry
```
