---
title: Dependency Graph
sort: 9
contributors:
  - jDevH
related:
  - title: HTTP2 Aggressive Splitting Example
    url: https://github.com/webpack/webpack/tree/master/examples/http2-aggressive-splitting
  - title: webpack & HTTP/2
    url: https://medium.com/webpack/webpack-http-2-7083ec3f3ce6
---

웹팩은 언제든지 한 파일이 다른 파일을 의존할때 `dependency`(종속)으로 취급합니다. 이를 통해 웹팩은 이미지나 웹 글꼴과 같은 non-code 자산을 종속성으로도 사용할 수 있게 합니다.

웹팩이 application을 처리할 때는 명령줄 또는 설정 파일에서 정의된 모듈 목록에서 시작합니다. [_entry points_](/concepts/entry-points/)에서 시작하여 application에 필요한 모든 모듈을 포함하는 종속성 그래프를 반복적으로 구축한 다음, 이를 통하여 브라우저에서 로드될 소수의 `bundle`(대개, 단 하나의 번들)로 모든 모듈을 묶습니다.

T> 당신의 application을 번들링하면 브라우저가 새로운 요청을 시작하는 동안 앱이 대기해야 하는 시간을 최소화하기 때문에 특히 HTTP/1.1 클라이언트에서 강력합니다. HTTP/2의 경우에서는 [Code Splitting](/guides/code-splitting/)을 사용하여 최상의 결과를 얻을 수도 있습니다.


