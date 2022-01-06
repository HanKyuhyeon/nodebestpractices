2.1

# 비동기 에러 처리시에는 async-await 혹은 promise를 사용하라

### 한문단 설명

콜백은 대부분의 개발자에게 익숙하지 않기 때문에 잘 쓰이지 않는다. 콜백은 오류를 전체적으로 확인하고 불쾌한 코드 중첩을 처리하게 하여 코드 흐름에 대해 추론하기 어렵게 만든다. BlueBird, async, Q와 같은 Promise 라이브러리는 RETURN 및 THROW를 사용하여 프로그램 흐름을 제어하는 표준 코드 스타일을 사용할 수 있게 해준다. 특히, try-catch 스타일을 지원하여 기본 코드 경로가 모든 기능의 오류를 매번 처리하지 않도록 한다.

### 코드 예시 – promise를 사용한 에러 잡기

```javascript
doWork()
 .then(doWork)
 .then(doOtherWork)
 .then((result) => doWork)
 .catch((error) => {throw error;})
 .then(verify);
```

### 코드 예시 – 좋지않은 패턴: 콜백 스타일 에러 처리

```javascript
getData(someParameter, function(err, result) {
    if(err !== null) {
        // do something like calling the given callback function and pass the error
        getMoreData(a, function(err, result) {
            if(err !== null) {
                // do something like calling the given callback function and pass the error
                getMoreData(b, function(c) {
                    getMoreData(d, function(e) {
                        if(err !== null ) {
                            // you get the idea? 
                        }
                    })
                });
            }
        });
    }
});
```

### 블로그 인용: "Promise에 문제가 있다"

 pouchdb.com 블로그에서

 > ……그리고 사실 콜백은 훨씬 더 불길한 일을 한다: 콜백은 우리가 프로그래밍 언어에서 당연하게 여기는 스택을 박탈한다. 스택 없이 코드를 작성하는 것은 브레이크 페달 없이 자동차를 운전하는 것과 같다: 스택에 도달했을 때 스택이 없을 경우 비로소 스택이 얼마나 필요한지 깨닫는다. Promise의 요점은 비동기화되었을 때 잃어버린 언어 기본 사항인 return, throw 및 스택을 다시 제공하는 것이다. 그러나 promise의 장점을 활용하려면 promise를 올바르게 사용하는 방법을 알아야 한다.

### 블로그 인용: "Promise 메서드는 훨씬 간결하다"

 gosquared.com 블로그에서

 > ……Promise 메서드는 훨씬 더 간결하고 명확하며 빠르게 작성할 수 있다. 에러 또는 예외가 발생하면 단일 .catch() handler에 의해 처리된다. 모든 에러를 처리할 수 있는 단일 공간이 있다는 것은 각 단계에서 매번 에러를 검사할 필요가 없다는 것을 의미한다.

### 블로그 인용: "Promise는 native ES6이며 generator와 함께 사용할 수 있다"

 StrongLoop 블로그에서

 > ……콜백에는 형편없는 에러 처리 이야기가 있다. Promise가 더 좋다. Express에 내장된 에러 처리를 promise와 결합하면 처리하지 못한 예외(uncaught exception)의 발생 가능성을 크게 낮춘다. Promise는 native ES6이며 generator와 함께 사용할 수 있고 Babel과 같은 컴파일러를 통해 async/await 등의 ES7 제안을 사용할 수 있다.

### 블로그 인용: "익숙했던 모든 일반적인 흐름 제어 구조는 완전히 망가졌다"

 Benno’s 블로그에서

 > ……비동기식 콜백 기반 프로그래밍을 사용하면 익숙했던 모든 일반적인 흐름 제어 구조가 완전히 망가진다. 이 중 가장 망가진 것은 예외를 처리하는 부분이라고 생각한다. Javascript는 예외를 처리하기 위해 상당히 친숙한 try…catch 구조를 제공한다. 예외는 콜 스택에서 오류를 단축하는 훌륭한 방법을 제공하지만 다른 스택에서 오류가 발생하면 완전히 쓸모 없게 된다는 문제가 있다…
