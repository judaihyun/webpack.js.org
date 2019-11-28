---
title: Concepts
sort: 1
contributors:
  - jDevH
---


웹팩의 핵심은 Modern Javascript Application을 위한 정적 모듈 번들러입니다. 웹팩이 application을 처리할때는, 프로젝트에서 필요로 하는 모든 모듈들을 매핑하여 하나 이상의 bundles를 생성하는 [dependency graph](/concepts/dependency-graph/)를 내부적으로 구축합니다.

T> Javascript 모듈과 웹팩 모듈에 대해 더 배우고 싶다면 ... [here](/concepts/modules/).


기본적으로 4.0.0 버전부터 웹팩은 configuration file에 대한 설정을 필요로 하지 않지만 더 나은 요구를 충족하려면 [incredibly configurable](/configuration) 설정이 필요합니다.

핵심만을 이해하고 싶다면 아래의 __Core Concepts__ 을 참조하세요 :

- [Entry](#entry)
- [Output](#output)
- [Loaders](#loaders)
- [Plugins](#plugins)
- [Mode](#mode)
- [Browser Compatibility](#browser-compatibility)

본 문서는 높은 수준의 Webpack 개념을 제공함과 동시에 상세한 사용법에 대한 링크를 제공하고 있습니다.
 Module Bundler에 대한 숨겨진 아이디어와 이것이 어떻게 동작하는 지에 대하여 이해하고자 하면 다음 리소스를 참고하세요.

- [Manually Bundling an Application](https://www.youtube.com/watch?v=UNMkLHzofQI)
- [Live Coding a Simple Module Bundler](https://www.youtube.com/watch?v=Gc9-7PBqOC8)
- [Detailed Explanation of a Simple Module Bundler](https://github.com/ronami/minipack)


## Entry

__entry point__ 는 웹팩이 내부 [dependency graph](/concepts/dependency-graph/)의 구축을 시작하기 위해 어떤 모듈을 사용해야 하는지 나타냅니다. 또한 웹팩은 entry point를 이용하여 어떤 모듈과 라이브러리에 의존하는지를 파악합니다.(직/간접적으로)

기본 __entry point__ 값은 `./src/index.js` 이지만  webpack config의 [entry 프로퍼티](/configuration)에 다른 경로 (or multiple entry points)를 지정할 수 있습니다.

__webpack.config.js__

``` js
module.exports = {
  entry: './path/to/my/entry/file.js'
};
```

T> entry의 자세한 사항은 [entry points](/concepts/entry-points) 섹션을 참조.


## Output

output 프로퍼티는 웹팩이 어디(path)에 bundles을 생성할지, 어떤 파일명(name)으로 생성할 지에 대한 프로퍼티입니다. 
기본값으로써 메인 출력 파일은 `./dist/main.js` 이고 다른 파일들의 경우 `./dist` 폴더로 설정되어 있습니다.

__webpack.config.js__

```javascript
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js'
  }
};
```

위의 예에서 output.path와 output.filename에 output파일명과 위치를 설정합니다.

T> 코드의 위쪽에 있는 const path는 파일 경로를 조작하는 데 사용되는 [Node.js module](https://nodejs.org/api/modules.html) 모듈입니다.

T> `output` 속성에는 더 많은 [설정](/configuration/output/)을 할 수 있으며 자세한 것은 해당 [section](/concepts/output/)을 참조.


## Loaders

T> ES5 to ES6 ... ,  react의 JSX, css-preprocessor의 SASS등의 변환을 수행한다고 보면 됩니다.

웹팩은 JS와 JSON 파일만을 이해하지만 __Loaders__ 는 웹팩이 다른 type의 파일을 처리하고 당신의 application에서 소비되는 유효한 [modules](https://webpack.js.org/concepts/modules)로 변환할 수 있도록 도와주는 역할을 하며 [dependency graph](/concepts/dependency-graph/)에 추가할 수 있도록 합니다.

W> `.css` 파일과 같은 유형의 모듈들을 `import`할 수 있는 기능은 웹팩만의 전용 기능이며, 다른 bundler나 task runner에서 지원되지 않을 수 있음을 유의해주시기 바랍니다. 우리는 이 언어의 확자이 개발자들에게 더 정확한 dependency graph를 그려주는 것을 보장한다고 확신한다.

높은 수준에서 __loaders__ 는 두 가지 프로퍼티를 가지고 있습니다.
 1. `test` 프로퍼티는 어떤 파일이 변환(transform)되어야 하는지를 나타냅니다.
 2. `use` 프로퍼티는 어떤 loader가 변환에 사용되어 질 것인지를 나타냅니다.

__webpack.config.js__

```javascript
const path = require('path');

module.exports = {
  output: {
    filename: 'my-first-webpack.bundle.js'
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  }
};
```

위의 설정에서는 단일 모듈에 대한 `test`와 `use` 속성이 `rules` 에 정의되어 있습니다. 이것은 웹팩 컴파일러에게 다음의 것들을 알려줍니다.

> "*.txt파일 중 require() 또는 import 문을 만나면 bundles에 추가하기 전에 raw-loader를 사용하여 변환하라는 명령을 내려!"

W> 웹팩 config에 `rules`가 아닌 `module.rules` 로 정의하지 않는 것을 꼭 기억하세요. 물론, 잘못된 설정을 할 경우 웹팩은 당신에게 경고를 줄겁니다.


__Loaders__ 의 자세한 사항은 [loaders section](/concepts/loaders)을 참조.



## Plugins

__loaders__ 는 특정한 종류의 모듈을 변환하기 위하여 사용되는 반면, __plugins__ 은 bundle optimization, asset management, injection of environment variables과 같은 광범위한 작업을 수행하는데 사용됩니다.

T> 웹팩에서 플러그인을 확장하는 법과 [plugin interface](https://webpack.js.org/api/plugins)에 대해서 확인하세요.

plugins을 사용하기 위해서는 `require()`를 사용하여 plugin array에 추가해야 합니다. 대부분의 플러그인은 옵션을 통하여 커스터마이징이 가능하며 config에서 플러그인을 여러 목적에 맞게 여러번 재사용할 수 있습니다. 그러므로 `new` 연산자와 함께 호출하여 인스턴스를 생성해야 합니다.


__webpack.config.js__

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); //installed via npm
const webpack = require('webpack'); //to access built-in plugins

module.exports = {
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};
```

위의 예는 `html-webpack-plugin`이라는 플러그인이 생성 완료된 bundles파일에 자동으로 주입하여 당신의 어플리케이션의 HTML파일을 생성하는 것입니다. 

T> bundles된 모듈들을 html에 `<script >` , `<link>` 등의 명시적 선언없이 주입됩니다.

T> 웹팩은 많은 플러그인들을 제공합니다. 자세한 사항은 [list of plugins](https://webpack.js.org/plugins) 참조.

플러그인을 사용하는 것은 간단하지만 많은 use case들이 존재하기 때문에 자세한 설명은 [Learn more about them here](/concepts/plugins). 에서 자세하게 설명합니다.



## Mode

`mode` parameter를 `development`, `production`, `none` 등으로 설정하면 각 환경에 해당하는 최적화를 수행할 수 있습니다. (기본값은 `production`)

```javascript
module.exports = {
  mode: 'production'
};
```

T> 한가지 예로,  development는 bundles.js 소스보기 시 pretty하게 보이고 production은 난독화, minify해서 번들링됩니다.

T> 자세한 사항은 [mode configuration here](https://webpack.js.org/concepts/mode) 참조하여 각 value들이 어떤 역할을 수행하는지 알아보세요.


## Browser Compatibility

웹팩은[ES5-compliant](https://kangax.github.io/compat-table/es5/)를 준수하는 모든 브라우저(IE8 and below 제외)에 대한 지원을 합니다. 웹팩은 import() 와 require.ensure()를 위하여 promise가 필요하기 때문입니다. 만약 이전 버전에 대한 브라우저 지원이 필요하면 [load a polyfill](https://webpack.js.org/guides/shimming/)을 사용하세요.

