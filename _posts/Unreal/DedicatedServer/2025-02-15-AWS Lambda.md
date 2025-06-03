---
title: AWS Lambda
date: 2025-02-15
categories: [unreal,DedicatedServer]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# AWS Lambda

* Lambda는 `사용자가 서버를 직접 관리할 필요 없이 코드를 실행할 수 있게 해주는 기능`

* AWS의 Console home에 Lambda에 들어가서 생성할 수 있다

### 특징

* 코드는 AWS가 관리하는 서버에서 필요할 때만 실행된다

* 특정 이벤트(자동 트리거 또는 수동 실행)에 반응하여 코드를 실행하도록 설계됨

* 게임 클라이언트는 직접 AWS API를 호출할 수 없지만, 보안 설정된 엔드포인트를 통해 Lambda 함수를 트리거하여 백엔드 작업을 수행하게 할 수 있음

* 코드가 실행되는 시간만큼만 비용이 청구되며, 유휴 상태일 때는 비용이 발생하지 않음. 또한, 동시에 많은 요청이 들어와도 자동으로 확장되어 모든 요청을 처리

* 여러 프로그래밍 언어를 지원(.Net,Java,Node.js,Python,Ruby)


## 문법 몇 개

### `=>`

*  기존의 function 키워드를 사용하는 함수 표현식보다 훨씬 간결하게 함수를 정의할 수 있게 해줌

```js
// 기존 함수 표현식
const add_old = function(a, b) {
  return a + b;
};

// 화살표 함수
const add_new = (a, b) => {
  return a + b;
};

// 함수 본문이 한 줄이면 중괄호와 return 생략 가능
const add_shorter = (a, b) => a + b;

// 매개변수가 하나면 괄호도 생략 가능
const square = x => x * x;
```

<br>

### 비동기 관련

* 자바스크립트는 싱글 스레드 기반 언어지만,  Node.js 환경에서는 시간이 걸리는 작업을 비동기로 처리해서 프로그램 전체가 멈추는 것을 방지

* `async`와 `await`는 Promise를 기반으로, 비동기 코드를 마치 동기 코드처럼 작동하게 만듦


### async 

* 함수 선언/표현식 앞에 async를 붙이면, 해당 함수는 항상 Promise를 반환
* 함수 내에서 `await` 키워드를 사용할 수 있게 됨

```js
async function myAsyncFunction() {
  // 이 함수는 항상 Promise를 반환
  return "작업 완료!";
}

myAsyncFunction().then(result => console.log(result)); // 작업 완료
```


### await

* `async` 함수 내에서만 사용할 수 있음
* `await` 뒤에는 Promise가 와야 함
* `await`은 해당 Promise가 완료될 때까지 함수의 실행을 일시 중지시키고 기다림
  * Promise가 성공적으로 이행(resolve)되면, 그 결과값을 반환

```js
// 가짜로 1초 걸리는 작업 함수 (Promise 반환)
function waitOneSecond(message) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log(message);
      resolve(); // 완료됨을 알림
    }, 1000); // 1000ms
  });
}

// async/await 사용
async function runTasks() {
  try {
    console.log("작업 시작");
    await waitOneSecond("첫 번째 작업 완료 (1초)"); // 1초 기다림
    await waitOneSecond("두 번째 작업 완료 (또 1초)"); // 또 1초 기다림
    console.log("모든 작업 끝");
  } catch (error) {
    // Promise가 reject되면 여기서 에러 처리
    console.error("오류 발생:", error);
  }
}
runTasks();

/*
출력 결과:
작업 시작!
(1초 후)
첫 번째 작업 완료 (1초)
(1초 후)
두 번째 작업 완료 (또 1초)
모든 작업 끝!
*/
```

### var

* 자바 스크립트에서는 var를 주로 썼지만, var은 스코프 문제가 있음

```js
// var의 함수 스코프 문제 예시
function checkVarScope() {
  if (true) {
    var message = "Hello"; // 블록 안에서 선언했지만...
  }
  console.log(message); // ...블록 밖에서도 접근 가능 "Hello" 출력
}
checkVarScope();

// for 루프에서의 문제
for (var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log("var:", i); // 루프가 끝난 후의 i 값(3)이 3번 출력됨
  }, 100);
}
```

* var은 함수 스코프(Function Scope) 를 가져서 함수 내부에서 선언되면 함수 전체에서 유효하고, 함수 외부에서 선언되면 전역 스코프를 가짐
* 그래서 let을 도입


### let

* let은 블록 스코프 변수(선언된 블록 내부에서만 유효)
* const와 달리 값 재할당 가능
* 재선언 금지(var은 가능)

```js
function checkLetScope() {
  if (true) {
    let message = "Hello"; // 이 블록 안에서만 유효
    console.log(message); // "Hello" 출력
  }
  // console.log(message); // ReferenceError: message is not defined (블록 밖 접근 불가)
}
checkLetScope();

// for 루프에서의 개선
for (let i = 0; i < 3; i++) {
  // 각 반복마다 새로운 i 변수 스코프가 생성되는 것처럼 동작
  setTimeout(function() {
    console.log("let:", i); // 각 시점의 i 값(0, 1, 2)이 정상적으로 출력됨
  }, 100);
}
```


