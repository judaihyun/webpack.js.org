# Getting Started

webpack 은 컴파일하는데 javascript 모듈을 사용한다. 일단 설치([installed](https://webpack.js.org/guides/installation))하면, [CLI](https://webpack.js.org/api/cli)  나  [API](https://webpack.js.org/api/node) 를 마주하게 된다. 만약 웹팩이 처음이라면 [core concepts](https://github.com/judaihyun/webpack-study/blob/master/documentation/concepts/Core%20Concepts.md) 을 읽어보라.
 그리고 [this comparison](https://webpack.js.org/comparison)에서 왜 많은 커뮤니티에서 이 툴을 사용하는지 알게 될 것이다.

> Webpack은 v5.0.0-beta.1 이므로 webpack을 실행할 최소 node.js 버전은 10.13.0(LTS)

<br>

## Basic Setup

연습할 디렉터리를 생성한 후 npm을 초기화 한다. [install webpack locally](https://webpack.js.org/guides/installation/#local-installation), 그리고 webpack-cli (the tool used to run webpack on the command line) 을 설치한다 :

```bash
mkdir webpack-demo
cd webpack-demo
npm init -y
npm install webpack webpack-cli --save-dev
```

그리고 다음과 같이 디렉터리 구조, 파일, 파일 내용을 작성하라.

**project**

```diff
  webpack-demo
  |- package.json
+ |- index.html
+ |- /src
+   |- index.js
```

**src/index.js**

```javascript
function component() {
  const element = document.createElement('div');

  // Lodash, currently included via a script, is required for this line to work
  element.innerHTML = _.join(['Hello', 'webpack'], ' ');

  return element;
}

document.body.appendChild(component());
```

**index.html**

```html
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
우리는 또한 우리의 패키지를 `private`하게 만들기 위하여 `package.json` 파일을 설정해야한다. 또 `main` entry를 삭제할 것이다. 이 것은 혹시라도 당신의 코드가 발행되는 것을 막기 위함이다.

> If you want to learn more about the inner workings of  `package.json`, then we recommend reading the  [npm documentation](https://docs.npmjs.com/files/package.json).

**package.json**

```diff
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

이 예제에서는 암묵적인 의존성이 `<script>` 태그 사이에 존재한다. 우리의 `index.js` 파일이 실행되기 전에 `lodash`가 페이지에 포함되어 있는지 아닌지에 따라서 의존되어 있는 상태이다. 이것은 `index.js`에서 `lodash`를 명시적으로 선언한 된 적이 없기 때문이다. 단지, 어딘가에 `_` 글로벌 변수가 존재한다고 가정할 뿐이다.

<br>

> [추가설명] `_` 는 `lodash`에서 사용되는 구문이다. `jquery`에서 해당 메서드를 사용할 때 `$`붙이는 것 처럼.

<br> 


기존 웹팩을 사용하지 않고 Javascript 프로젝트를 관리할 때는 다음과 같은 문제가 있다.

-   외부 라이브러리에 의존적인 스크립트는 즉시 반영이 되지 않는다.
-   의존 모듈이 없거나, 또는 잘못된 순서로 include 되었을 때 어플리케이션은 제대로 기능하지 못한다.
-   의존 모듈이 include되었지만 사용되지 않을 때도 브라우저는 불필요한 코드까지 다운로드한다.
<br>
대신! 웹팩으로 이러한 관리를 합시다








