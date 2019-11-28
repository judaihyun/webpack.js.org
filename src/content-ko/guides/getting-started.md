---
title: Getting Started
sort: 2
contributors:
  - jDevH
---

Webpack은 자바스크립트 모듈을 컴파일하기 위해 사용됩니다. [설치](/guides/installation)를 하면, [CLI](/api/cli) 나 [API](/api/node)로 웹팩을 다루게 되는데 아직 웹팩이 처음이라면[ core concepts](/concepts)를 읽고 왜 웹팩을 사용하여야 하는지 그리고 [이 비교](/comparison)를 통하여 다른 툴들과의 비교를 살펴보세요.

W> Webpack은 v5.0.0-beta.1 이므로 webpack을 실행할 최소 node.js 버전은 10.13.0(LTS)

## 기본 설정

먼저, 아래와 같이 디렉터리를 만들고, npm을 초기화, [install webpack locally](/guides/installation#local-installation)하고, webpack-cli( 커맨드 라인에서 웹팩을 사용하기 위한 도구)를 설치합니다.

``` bash
mkdir webpack-demo && cd webpack-demo
npm init -y
npm install webpack webpack-cli --save-dev
```
T> 가이드 전체에서 `diff`블록을 통하여 어떤 파일과 디렉터리,코드가 변경되어 있는지를 알려줄겁니다.

자, 지금부터 우리는 아래와 같이 디렉터리 구조, 파일, 파일 내용을 채웁시다.

__project 구조__

``` diff
  webpack-demo
  |- package.json
+ |- index.html
+ |- /src
+   |- index.js
```

__src/index.js__

``` javascript
function component() {
  let element = document.createElement('div');

  // 아래의 라인이 작동하려면 `lodash`스크립트가 포함되어야 합니다.
  element.innerHTML = _.join(['Hello', 'webpack'], ' ');

  return element;
}

document.body.appendChild(component());
```

__index.html__

``` html
<!doctype html>
<html>
  <head>
    <title>Getting Started</title>
    <script src="https://unpkg.com/lodash@4.16.6"></script>
  </head>
  <body>
    <script src="./src/index.js"></script>
  </body>
</html>
```

우리의 package를 `private`하게 만들기 위하여 `package.json`을 설정해야 합니다. 또한 `main`entry를 지웁시다. 만약에 당신의 코드가 publish되는 일을 막기 위한 과정입니다.

T> `package.json`의 설정 방법에 대하여 더 자세히 알고 싶다면 [npm documentation](https://docs.npmjs.com/files/package.json)를 읽어보세요.

__package.json__

``` diff
  {
    "name": "webpack-demo",
    "version": "1.0.0",
    "description": "",
+   "private": true,
-   "main": "index.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "devDependencies": {
    "webpack": "^4.20.2",
    "webpack-cli": "^3.1.2"
    },
    "dependencies": {}
  }
```

T> `jquery`에서 해당 메서드를 사용할 때 `$`붙이는 것 처럼 `_` 는 `lodash`에서 사용되는 구문이다.

이 예제에서는 `<script>`태그 사이에 `index.js`파일안에 `lodash`가  먼저 로드된 후에 실행되어야 한다는 암묵적 종속성이 내재되어 있습니다. 암묵적 종속성을 갖는 이유는 `index.js`파일 안에 `lodash`를 명시적으로 선언하지 않고, 글로벌 변수 어딘가에 `_`가 존재한다는 가정만을 하기 때문입니다.

웹팩을 사용하지 않고 Javascript 프로젝트를 관리할 때는 다음과 같은 문제가 있다.

-   외부 라이브러리에 의존적인 스크립트는 즉시 반영이 되지 않습니다.
-   의존 모듈이 없거나, 또는 잘못된 순서로 include 되었을 때 어플리케이션은 제대로 기능하지 못합니다.
-   의존 모듈이 include되었지만 사용되지 않을 때도 브라우저는 불필요한 코드까지 다운로드합니다.

대신! 웹팩으로 이러한 관리를 하세요.

## Creating a Bundle

앞서 생성한 디렉터리 구조에서 소스 코드를  `/src`에서 `/dist`로 이동을 합니다.  `/dist`,즉 `distribution`코드는 브라우저에서 로드될 minimized, optimized된 `output`의하여 생성된 코드입니다.

__project__

``` diff
  webpack-demo
  |- package.json
+ |- /dist
+   |- index.html
- |- index.html
  |- /src
    |- index.js
```

먼저, `lodash`종속성을 `index.js`로 bundle하기 위해서는 `lodash`를 로컬로 설치해야 합니다.

``` bash
npm install --save lodash
```

T> 당신의 프로젝트 번들에 bundle로 제공되는 패키지를 설치할 때는 `npm install --save`를 사용해야 합니다. 만약 패키지를 개발 목적(linter, testing, etc)으로 사용할 때는 `npm install --save-dev`명령을 사용하여 설치하십시오. 더 자세한 정보는 [npm documentation](https://docs.npmjs.com/cli/install)에서 확인하세요.

자, 설치가 됐다면 `lodash`를 `src/index.js`에 import 하세요.

__src/index.js__

``` diff
+ import _ from 'lodash';
+
  function component() {
    let element = document.createElement('div');

-     // 아래의 라인이 작동하려면 `lodash`스크립트가 포함되어야 합니다.
    element.innerHTML = _.join(['Hello', 'webpack'], ' ');

    return element;
  }

  document.body.appendChild(component());
```

이제 스크립트를 bundle로써 제공할 것이므로 아래와 같이 `index.html`를 수정해야 합니다. import하여 가져오는 lodash `<script>`를 지우고 `/src`파일 대신 생성된 bundle을 `<script>`에 로드하세요.

__dist/index.html__

``` diff
  <!doctype html>
  <html>
   <head>
     <title>Getting Started</title>
-    <script src="https://unpkg.com/lodash@4.16.6"></script>
   </head>
   <body>
-    <script src="./src/index.js"></script>
+    <script src="main.js"></script>
   </body>
  </html>
```

위 설정에서는 `index.js`는 명시적으로 `lodash`를 포함하도록 요구하며 `_`로 바인딩하고 있습니다. 이를 `main.js`로 변경함으로써 모듈에 필요한 종속성을 웹팩에게 알려주고, 웹팩은 이 정보를 토대로 `dependency graph`를 구축합니다. 그리고 이 그래프를 이용하여 스크립트가 올바른 순서로 실행되게 최적화된 bundle을 생성합니다.

`src/index.js`를 [entry point](/concepts/entry-points)로 삼고 `dist/main.js`를 [output](/concepts/output)으로 생성하는 `npx webpack`을 실행하세요. Node 8.2/npm 5.2.0 이상과 함께 제공되는 이 `npx` 명령어는 우리가 처음 설치한 웹팩의 binary(`./node_modules/.bin/webpack`)를 실행하는 명령어 입니다.


``` bash
npx webpack

...
Built at: 13/06/2018 11:52:07
  Asset      Size  Chunks             Chunk Names
main.js  70.4 KiB       0  [emitted]  main
...

WARNING in configuration
The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/concepts/mode/
```

T> 위의 출력 메시지와는 조금 다를 수 있지만, 성공적으로 빌드가 되었다면 상관없습니다. 또한 나중에 우리가 이것을 다룰 것이므로 경고메시지도 무시해도 좋습니다.

아무런 이상이 없다면 `index.html`파일을 브라우저에서 열었을때 다음 메시지를 보게 될 것입니다. 'Hello webpack'.


## Modules

[`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 와 [`export`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) 구문은 [ES2015](https://babeljs.io/learn-es2015/)에서 정의됐는데도 불구하고 아직 [대부분의 브라우저에서](https://caniuse.com/#search=modules) 지원되지는 않지만 웹팩은 이를 지원합니다.

이를 지원할 수 있는 이유는 웹팩이 오래된 브라우저들도 해석 가능한 코드로 해당 코드를 변환하기 때문입니다. `dist/main.js`를 살펴보면 웹팩이 어떻게 이것을 수행했는지를 확인할 수 있습니다. 이것은 정말 기발한 방법입니다. 웹팩은 `import`와 `export`외에도 다양한 모듈 syntax를 지원합니다.  [Module API](/api/module-methods)에 대한 자세한 정보를 확인하세요.

웹팩은 `import`,`export`구문외에는 어떤 코드도 변경하지 않는 다는 사실을 기억하세요. 만약 [ES2015 features](http://es6-features.org/)의 다른 구문들을 사용하려면 웹팩의 [loader system](/concepts/loaders/)을 통하여 [Babel](https://babeljs.io/)이나 [Bublé](https://buble.surge.sh/guide/)등의 [transpiler](/loaders/#transpiling)를 사용하세요.

 
## Using a Configuration

웹팩 버전 4를 기준에서는 특별한 설정을 요구하지 않습니다. 그러나 대부분의 프로젝트에서는 더 복잡한 설정을 필요로 하므로 웹팩은 [configuration file](/concepts/configuration)를 지원합니다. 이것은 terminal에 많은 명령어를 수동으로 입력해야 하는 것보다 훨씬 효율적입니다. 
한가지 설정 파일을 만들어 보겠습니다.

__project__

``` diff
  webpack-demo
  |- package.json
+ |- webpack.config.js
  |- /dist
    |- index.html
  |- /src
    |- index.js
```

__webpack.config.js__

``` javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

이제 새로운 config 파일을 가지고 build를 다시 해보겠습니다.


``` bash
npx webpack --config webpack.config.js

...
  Asset      Size  Chunks             Chunk Names
main.js  70.4 KiB       0  [emitted]  main
...

WARNING in configuration
The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/concepts/mode/
```

T> 만약 `webpack.config.js`가 존재한다면 웹팩은 이 설정 파일을 사용합니다. `--config`옵션을 통하여 다른 config파일명을 입력하면 그것을 설정 파일로써 사용하게 됩니다. 이것은 여러 설정 파일로 분할되어야 하는 더 복잡한 구성에 유용합니다.

T> If a `webpack.config.js` is present, the `webpack` command picks it up by default. We use the `--config` option here only to show that you can pass a config of any name. This will be useful for more complex configurations that need to be split into multiple files.

설정 파일은 CLI을 사용하는 것보다 훨씬 더 많은 유연성을 제공합니다. loader rules, plugins, resolve option등 기타 여러 가지 사항들을 지정할 수 있기 때문입니다.  이 설정에 대한 더 자세한 사항은 [configuration documentation](/configuration)를 참조하세요. 


## NPM Scripts

CLI상에서 웹팩을 실행하는 것이 부담스럽다는 것을 감안하여  `package.json`에  [npm script](https://docs.npmjs.com/misc/scripts)를 설정함으로써 바로 가기(alias/shortcut)를 설정할 수 있습니다.

__package.json__

``` diff
  {
    "name": "webpack-demo",
    "version": "1.0.0",
    "description": "",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
+     "build": "webpack"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "devDependencies": {
    "webpack": "^4.20.2",
    "webpack-cli": "^3.1.2"
    },
    "dependencies": {
      "lodash": "^4.17.5"
    }
  }
```

이 설정으로 `npm run build`명령어는 앞에서 사용한 `npx`명령어 대신 사용할 수 있습니다.(아래) 이는 `script`내에서 로컬로 설치된 npm 패키지를 `npx`와 동일한 이름으로 참조(별칭)하게 됩니다. 이 convention은 모든 contributor가 동일한 스크립트를 사용할 수 있도록 하기 때문에 대부분의 npm 기반 프로젝트에서 표준으로 사용되어 집니다. 

이제 다음 명령어를 통하여 alias 설정이 된 것을 확인할 수 있습니다.
``` bash
npm run build

...
  Asset      Size  Chunks             Chunk Names
main.js  70.4 KiB       0  [emitted]  main
...

WARNING in configuration
The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/concepts/mode/.
```
T> `npm run build`명령어 뒤에 `--`개의 'dash'문자를 추가하여 웹팩에게 파라미터를 넘겨 줄 수 있습니다. (예 : `npm run build -- -- colors`)


## Conclusion

이제까지 기본 빌드를 구축했으므로 다음 가이드인 [`Asset Management`](/guides/asset-management)로 이동하여 이미지 및 글꼴과 같은 resource를 관리하는 방법을 알아보세요. 잘 수행해 왔다면 당신의 프로젝트 구조는 아래와 같습니다.


__project__

``` diff
webpack-demo
|- package.json
|- webpack.config.js
|- /dist
  |- main.js
  |- index.html
|- /src
  |- index.js
|- /node_modules
```

T> 당신이 npm 5버전을 사용했다면 `package-lock.json`파일도 존재할겁니다.

만약 웹팩 디자인에 대하여 더 알고 싶다면 [basic concepts](/concepts)와 [configuration](/configuration)페이지를 참고하세요. 추가적으로 [API](/api)섹션에서 웹팩이 제공하는 다양한 인터페이스를 살펴볼 수 있습니다.
