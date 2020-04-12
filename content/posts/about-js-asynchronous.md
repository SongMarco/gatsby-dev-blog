---
title: 자바스크립트의 비동기 처리 방식에 대한 고찰
date: "2020-04-12T10:00:00.169Z"
template: "post"
draft: false
slug: "about-js-asynchronous"
category: "backend"
tags:
  - "javascript"
  - "backend"
  # - "load balancing"
# description: "스키마가 무엇인지 고민해보"
socialImage: "/media/erroruxui.png"
---

### 들어가며

사실 자바스크립트의 설계와 동작 방식에 대한 좋은 글은 이미 많이 있다. 그럼에도 불구하고 이 글을 쓰는 이유는, 내가 직접 이해하고 나만의 언어로 표현함으로써 자바스크립트를 더 잘 알게 되리라 생각했기 때문이다. 또한 웹 개발을 하면서 가장 어렵게 다뤘던 부분이 자바스크립트의 비동기 처리여서, 꼭 한 번은  정리하고 싶었다. 

### 자바스크립트의 동작 방식에 대하여

자바스크립트는 싱글 스레드이며, 동기 방식으로 동작한다. 자바나 C++처럼 병렬 실행을 위해 스레드를 새로 만들 수 없다. 따라서, 싱글 스레드로 동작하는데 어느 한 코드에서 발이 묶여버린다면 그대로 전체 프로그램이 굳어 버린다. (block 된다고 표현한다.) 웹 페이지에서 이런 일이 일어난다면 UI가 완전 멈추게 되어, 브라우저에서 다음과 같이 에러를 발생시킨다.

![erroruxui](/media/erroruxui.png)

이런 블로킹으로 인한 퍼포먼스 저하를 막기 위해, 자바스크립트는 비동기 방식으로 사용할 수 있도록 설계됐다. 시간이 오래 걸리는 작업을 기다리지 않아도 동작하도록 코드를 짤 수 있다. 핵심 원리는 오래 걸리는 일을 그 자리에서 기다리는 대신, 결과가 나왔을 때 받아서 처리하는 것이다.  따라서 HTTP 통신, 파일 입출력 등 시간이 많이 필요한 코드가 수행되는 동안 다른 동작(UX/UI 등)을 계속 수행할 수 있고, 유저 경험을 해치지 않게 되는 것이다.

정리하면 **자바스크립트는 기본적으로 동기 방식이며, 싱글 스레드로 동작한다. 다만 비동기 처리를 위한 몇몇 유용한 기능을 지원한다. (틀리다면 피드백 환영합니다. 아래는 참고자료입니다.)**

- [https://stackoverflow.com/questions/2035645/when-is-javascript-synchronous](https://stackoverflow.com/questions/2035645/when-is-javascript-synchronous)
- [https://medium.com/better-programming/is-javascript-synchronous-or-asynchronous-what-the-hell-is-a-promise-7aa9dd8f3bfb](https://medium.com/better-programming/is-javascript-synchronous-or-asynchronous-what-the-hell-is-a-promise-7aa9dd8f3bfb)

### 자바스크립트의 비동기 처리 방법

자바스크립트에서 비동기 처리를 하는 방법은 총 3가지 정도가 있다.

1. callback function
2. Promise
3. async - await

(generator는 제외)

각 기능들은 이전의 기능의 불편함을 개선하면서 등장했기 때문에, 이전 기능을 이해하지 않고 사용하면 겉핥기로만 사용할 우려가 있다. 특히 ES2017 이후 등장한 async / await 은 사용성이 너무 좋다보니, 이전의 개념들을 이해하지 않아도 어떻게든 쓸 수는 있다. 그러나 버그가 발생했을 때 Promise 에 대해서 모른다면 디버깅하기가 참 힘들다. 특히 반복문(foreach, map)이랑 사용할 때 시행착오를 많이 겪게 된다. async - await 을 알려면 Promise를 알아야 하고, Promise를 알려면 비동기 처리에 대해서 알아야 하니, 자바스크립트의 비동기 처리에 대해 개념 정리를 확실히 해두는 것이 좋다.

각 비동기 동작의 내용에 대해서는 다음 시리즈에서 다룰 예정이다. 

### 참고 자료

- [https://engineering.huiseoul.com/자바스크립트는-어떻게-작동하는가-엔진-런타임-콜스택-개관-ea47917c8442](https://engineering.huiseoul.com/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%9E%91%EB%8F%99%ED%95%98%EB%8A%94%EA%B0%80-%EC%97%94%EC%A7%84-%EB%9F%B0%ED%83%80%EC%9E%84-%EC%BD%9C%EC%8A%A4%ED%83%9D-%EA%B0%9C%EA%B4%80-ea47917c8442)
- [https://joshua1988.github.io/web-development/javascript/js-async-await/](https://joshua1988.github.io/web-development/javascript/js-async-await/)
- 반복문과 함께 async / await 사용 [https://velog.io/@minsangk/2019-09-06-0209-작성됨-eik06xy8mm](https://velog.io/@minsangk/2019-09-06-0209-%EC%9E%91%EC%84%B1%EB%90%A8-eik06xy8mm)
- [https://mygumi.tistory.com/328](https://mygumi.tistory.com/328)
- [https://yorr.tistory.com/20](https://yorr.tistory.com/20)