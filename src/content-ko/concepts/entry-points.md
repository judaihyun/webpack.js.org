---
title: Entry Points
sort: 2
contributors:
  - jDevH
---

[Getting Started](/guides/getting-started/#using-a-configuration)에서 언급했던 것과 같이, `entry` 프로퍼티를 정의하는 것에는 여러 방법이 존재합니다. 여기서 `entry` 프로퍼티가 유용한 이유와 속성을 정의하는 방법을 보여줍니다.


## Single Entry (Shorthand) Syntax

Usage: `entry: string|Array<string>`

__webpack.config.js__

```javascript
module.exports = {
  entry: './path/to/my/entry/file.js'
};
```

위와 같이 `entry` 단독으로 single entry 문법을 쓸 수 있다.

__webpack.config.js__

```javascript
module.exports = {
  entry: {
    main: './path/to/my/entry/file.js'
  }
};
```

T> __`entry`에 array를 넘기면 무슨 일이 일어날까요?__ `entry`에 파일 경로를 배열로 넘기면 `multi-main entry`이라고 불리는 프로퍼티가 생성되는데, 이것은 여러 개의 종속적인 파일들을 하나의 `chunk`로 `dependency graph`로 표시하려는 경우 유용합니다.

이것은 단 하나의 진입점(라이브러리등)을 가진 어플리케이션이나 도구에 대하여 웹팩 사용을 신속히 설정할 때 좋은 선택이나, 구성을 확장할 수 있는 유연성을 가지고 있진 않습니다.


## Object Syntax

Usage: `entry: {[entryChunkName: string]: string|Array<string>}`

__webpack.config.js__

```javascript
module.exports = {
  entry: {
    app: './src/app.js',
    adminApp: './src/adminApp.js'
  }
};
```
`object` 문법은 `Single Entry Syntax` 보다는 조금 더 장황합니다. 그러나 `entry`들에 대하여 가장 확장성이 좋은 설정 방법입니다.

T> __"확장가능한 webpack configurations"__ 은 다른 설정들과 함께 재사용 가능하고 결합할 수 있는 구성 방식이다. 이것은 environment, build target, runtime등에 대한 관심사를 분리하는 인기 있는 기법이다. 그런 다음 [webpack-merge](https://github.com/survivejs/webpack-merge)와 같은 도구를 사용하여 병합된다.


## Scenarios

아래는 entry 설정 및 실제 사용 사다례에 대한 목록입니다.

### Separate App and Vendor Entries
T> webpack 4버전 미만에서는 vendor를 별도의 진입점으로 추가하여 별도의 파일로 컴파일하는 것이 일반적이다.(`CommonsChunkPlugin`과 결합하여).
이것은 웹팩 4버전 미만의 단점이지만 대신 `[optimization.splitChunks](configuration/optimization/#optimizationsplitchunks)`옵션은 vender와 모듈을 분리하고 별도의 파일을 생성하여 처리한다.


### Multi Page Application

__webpack.config.js__

```javascript
module.exports = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
};
```

__이것이 무엇을 하고 있나?__ 위의 예제와 같이 3개(pageOne,pageTwo,pageThree)로 분리된 의존성 그래프를 보여주고 있습니다.

__Why?__ multi-page application에서 서버가 새로운 HTML document를 fetch할 때, 새로운 문서와 resource(css, image, etc)를 리로드하게 된다. 이것은 우리에게 여러 가지 일을 할 수 있는 기회를 주게 됩니다.
- `entry point`에서 많은 코드/모듈을 재사용하는 multi-page application에서 `optimization.splitChunks`를 사용하여 각 페이지 간의 공유 코드 bundles을 생성하면 이 기법의 혜택을 크게 받을 수 있습니다.

T> 일반적으로 각 HTML 문서에 대해 하나의 진입점만을 사용한다.

