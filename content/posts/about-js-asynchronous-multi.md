---
title: 자바스크립트의 비동기 동작과 반복문을 함께 사용해보자
date: "2020-05-20T10:00:00.169Z"
template: "post"
draft: false
slug: "about-js-asynchronous-multi"
category: "backend"
tags:
  - "javascript"
  - "backend"
  # - "load balancing"
# description: "스키마가 무엇인지 고민해보"
socialImage: "/media/erroruxui.png"
---

## 자바스크립트의 비동기 동작에 대해 - 2 : 비동기 동작과 반복문을 함께 사용해보자 (async-await + promise)

자바스크립트에서 비동기 처리를 위해 자주 사용하는 방법은 3가지가 있으며, 자세한 개념 설명은 간단한 gif 이미지와, 아래의 링크로 대체한다. 
![sync_all](/media/async_await.gif)

### 자바스크립트의 비동기 처리 방법

1. 콜백 함수(callback function) : [https://joshua1988.github.io/web-development/javascript/javascript-asynchronous-operation/](https://joshua1988.github.io/web-development/javascript/javascript-asynchronous-operation/)
2. 프로미스(Promise) : [https://joshua1988.github.io/web-development/javascript/promise-for-beginners/](https://joshua1988.github.io/web-development/javascript/promise-for-beginners/)
3. async - await : [https://joshua1988.github.io/web-development/javascript/js-async-await/](https://joshua1988.github.io/web-development/javascript/js-async-await/)

(+ generator 도 있음. 추후 포스팅 검토중 )

---

이 글에서는 자바스크립트에서 비동기 동작을 수천 번 이상 수행해야할 때 어떻게 해야하는지를 다뤄보고자 한다.

## 여러 개의 비동기 동작을 빠르게 처리하고 싶다면?

자바스크립트를 사용하면서 비동기 동작 관련하여 개발할 때 힘들었던 이슈는, 비동기 처리를 수천 개 이상 해야할 때였다. 구체적인 예시로는 이메일, 알림톡 등 HTTP 리퀘스트가 많이 발생하는 경우가 있다. 통신, 파일 입출력 등 시간이 오래 걸리는 프로세스를 동기로 실행하게 되면 처리가 상당히 느리며, 병렬로 작업을 가져가도록 코드 설계가 필요해진다.

간단한 코드 처리 예시와 결과를 통해 알아보자.

---

## 예제 함수
시간이 오래 걸리는 함수 프로세스가 있다. 예제에선 500ms 후 작업이 완료되도록 구성하였다.

- json으로 HTTP POST 요청을 보낼 때 (axios 모듈 사용)

```jsx
let longTimeTask = (index) => {
    return new Promise(function (resolve, reject) {
        process.stdout.write(`${index}번째 일하는중...`);
        setTimeout(function () {
            // let items = randomTime;
            resolve(`${index}번째 작업완료`)
        }, 500);
    });
}
```

## 코드 예제 - for문
이런 프로세스를 수천 번 실행해야 한다면 어떻게 할까? 

ex) 수천 명의 유저에게 이메일이나 SMS 메시지 등을 보내야 하는 경우

일단은 반복문을 써보기로 하자.

for문을 돌려보면, 동기로 동작한다. 앞선 작업이 끝나야 다음 작업이 진행되기 때문에, 동작 횟수가 많아지면 시간이 오래 걸린다.

```jsx
let main = async () => {
    console.log("작업 시작");
    //가상의 작업 리스트
    let workList = new Array(10).fill(0);

    let loopCount = 0;
		for(let work of workList){
        let taskRes = await longTimeTask(loopCount);
        console.log(taskRes);
        ++loopCount;
    }
    console.log('모든 작업이 끝났습니다.');
}
```

코드 실행 결과. 앞선 작업이 끝나야 다음 작업이 수행된다... (답답)
![work_sync](/media/work_sync.gif)

## 코드 예제 - Promise.all
따라서 반복문이 병렬로 처리될 수 있도록 추가적인 조치가 필요해진다. 여러 방법이 있지만, 나의 경우 map과 Promise.all()의 조합이 코드 양이 적어서 주로 활용하는 편이다.

```jsx
let workPromises = await workList.map(async (item, index) => {
        let taskRes = await longTimeTask(index);
        console.log(taskRes);
        return index;
    })
await Promise.all(workPromises);
console.log('모든 작업이 끝났습니다.');
```

코드 실행 결과. 앞의 for문을 사용한 케이스와 비교했을 때 훨씬 빠르게 작업이 끝난다.
![work_async](/media/work_async.gif)

이외에도 가능한 여러 조합이 있다.
- foreach 와 Array.push, Promise.all 조합 등등...

주의할 점은, 항상 비동기 처리를 병렬로만 처리하는 것이 능사는 아니라는 것이다. 예를 들어 클라이언트에서 한 번에 많은 리퀘스트를 서버에 병렬로 보내도록 시도한다면, 서버 혹은 중간에 있는 로드밸런서 등의 미들웨어에서 리퀘스트를 받아주지 못하기도 한다. 이렇게 되는 경우 서버가 감당할 수 있는 만큼만 보내도록 제한하는 방법을 생각해볼 수 있다. 항상 각자의 환경에 맞게 최적의 방법을 찾는 노력이 필요하다.