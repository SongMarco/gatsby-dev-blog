---
title: 자바스크립트로 대규모 HTTP 통신이 하고 싶을 때는 어떻게 할까?
date: "2020-05-10T10:00:00.169Z"
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

# 자바스크립트의 비동기 동작에 대해 - 2 : 비동기 통신을 대규모로 하고 싶을 경우

자바스크립트에서 비동기 처리를 하는 방법은 총 3가지 정도가 있으며, 개념 설명은 아래의 링크로 대체하였다. 이 글에서 중점적으로 다룰 부분은 "비동기 동작을 많이(최소 수천 번) 수행해야할 때" 이다.

### 자바스크립트의 비동기 처리 방법

1. 콜백 함수(callback function) : [https://joshua1988.github.io/web-development/javascript/javascript-asynchronous-operation/](https://joshua1988.github.io/web-development/javascript/javascript-asynchronous-operation/)
2. 프로미스(Promise) : [https://joshua1988.github.io/web-development/javascript/promise-for-beginners/](https://joshua1988.github.io/web-development/javascript/promise-for-beginners/)
3. async - await : [https://joshua1988.github.io/web-development/javascript/js-async-await/](https://joshua1988.github.io/web-development/javascript/js-async-await/)

(+ generator 도 있다. 추후 포스팅 검토중 )

비동기 동작 관련해서 개발할 때 자주 겪었던 이슈는, 비동기 처리를 수천 개 이상 해야할 때(이메일, 알림톡 등) 발생했었다. 단순히 반복문을 async await으로 처리하자니 너무 동작이 느려지는 것이다. (HTTP 통신이 하나 끝날 때마다 다음 통신을 진행하니까 ...)

### async - await으로 반복 처리를 하고 싶을 때

async - await의 본질은 결국, 프로미스를 다루는 것이다. await 걸린 코드가 끝나는 것을 기다렸다가, 다음 로직들을 처리한다. 

뭔가 시간이 오래 걸리는 코드가 있다.(프로미스를 반환하는) 이를테면 다음과 같이

- json으로 HTTP POST 요청을 보낼 때 (axios 모듈 사용)

다음과 같이 시간이 오래 걸리는 함수가 있다고 하자.

```jsx
let takeLongTime = () => {

    let result = new Promise(function (resolve, reject) {
        setTimeout(function () {
            let items = [1, 2, 3];
            console.log("타임 아웃 ::: ",items);
            resolve(items)
        }, 500);
    });

    return result;
}
```

이런 함수를 한 번만 실행해야 한다면, 다음과 같이 쉽게 비동기처리를 할 수 있다.

```jsx
let main = async () => {
    console.log("작업 시작");
    let longtimeRes = await takeLongTime();
    console.log("작업 결과 ::: ",longtimeRes);
}

main();
```

- 결과

```jsx
작업 시작
타임 아웃 :::  [ 1, 2, 3 ]
작업 결과 :::  [ 1, 2, 3 ]
```

그런데 시간이 오래 걸리는 함수를 수천 개 실행해야 한다면 어떻게 할까?

ex) 수천 명의 유저에게 이메일이나 SMS 메시지 등을 보내야 하는 경우

for문을 돌려보면, 동기로 동작한다. 병렬 동작이 없기 때문에, 상당히 느린 동작을 보이게 된다. 이 동작을 수천, 수만 번 이상 반복해야 한다면 사용하기가 힘들어진다.

```jsx
let main = async () => {
    console.log("작업 시작");
    //가상의 작업 리스트
    let workList = new Array(100).fill(0);
    
    let loopCount = 0;
    for(let item of workList){
        let longtimeRes = await takeLongTime();
        console.log("longtimeRes :: ", longtimeRes, loopCount);
        ++loopCount;
    }
}

main();
```

```bash
작업 시작
타임 아웃 :::  [ 1, 2, 3 ]
longtimeRes ::  [ 1, 2, 3 ] 0
타임 아웃 :::  [ 1, 2, 3 ]
longtimeRes ::  [ 1, 2, 3 ] 1
타임 아웃 :::  [ 1, 2, 3 ]
longtimeRes ::  [ 1, 2, 3 ] 2
타임 아웃 :::  [ 1, 2, 3 ]
longtimeRes ::  [ 1, 2, 3 ] 3
타임 아웃 :::  [ 1, 2, 3 ]
longtimeRes ::  [ 1, 2, 3 ] 4
```

여러 방법이 있지만, 나는 map과 Promise.all()의 조합을 주로 활용한다. 

```jsx
let workList = new Array(100).fill(0);
```

```jsx
let workRes = await workList.map( async (item, index) => {
        let longtimeRes = await takeLongTime();
        console.log("longtimeRes :: ", longtimeRes, index);
        return Number( index);
    })

await Promise.all( workRes ).then( res => console.log(res) );
```

```jsx
...
타임 아웃 :::  [ 1, 2, 3 ]
longtimeRes ::  [ 1, 2, 3 ] 98
타임 아웃 :::  [ 1, 2, 3 ]
longtimeRes ::  [ 1, 2, 3 ] 99
[
   0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11,
  12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23,
  24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35,
  36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47,
  48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59,
  60, 61, 62, 63, 64, 65, 66, 67, 68, 69, 70, 71,
  72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82, 83,
  84, 85, 86, 87, 88, 89, 90, 91, 92, 93, 94, 95,
  96, 97, 98, 99
]
```

이외에도 가능한 여러 조합이 있다.

- foreach(+ Array.push()) + Promise.all()

주의할 점은, 항상 병렬로만 처리하는 것이 능사는 아니라는 것이다. 클라이언트에서 너무 많은 리퀘스트를 한 번에 병렬로 보내도록 시도한다면, API측에서 받지 못하여 커넥션 에러가 발생하기도 한다. 이렇게 되는 경우 서버가 감당할 수 있는 만큼만 병렬로 보내도록 클라이언트에서 리퀘스트를 나누어서 보내도록 해야할 것이다. 커넥션 풀을 활용하는 것도 좋은 방법이 될 수 있다. 각자의 환경에 따라 조정이 필요하다.