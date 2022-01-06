# 낯선이가 들어오면 프로세스를 적절하게 중단하라

### 한문단 요약

코드 내 어딘가에서 에러 처리 객체는 에러가 발생했을 때 어떻게 진행할지 결정한다 - 에러가 신뢰할 수 있는 경우(즉, 작동 오류, 모범 사례 #3의 추가 설명 참조) 로그 파일에 기록하는 것으로 충분할 수 있다. 에러가 익숙하지 않으면 상황이 복잡해진다 - 즉, 일부 구성 요소에 결함이 있는 상태가 될 수 있고 향후 모든 요청이 실패할 수 있다. 예를 들어, 예외가 발생하고 상태가 손실된 싱글톤 상태 저장 토큰 발급자 서비스를 가정하면 지금부터 예기치 않게 작동하여 모든 요청이 실패할 수 있다. 이 시나리오에서는 깨끗한 상태로 다시 시작하려면 프로세스를 죽이고 '리스타터 툴'(Forever, PM2 등)을 사용해야 한다.

### 코드 예시: 충돌 여부 결정

```javascript
// Assuming developers mark known operational errors with error.isOperational=true, read best practice #3
process.on('uncaughtException', function(error) {
  errorManagement.handler.handleError(error);
  if(!errorManagement.handler.isTrustedError(error))
  process.exit(1)
});

// centralized error handler encapsulates error-handling related logic
function errorHandler() {
  this.handleError = function (error) {
    return logger.logError(err)
      .then(sendMailToAdminIfCritical)
      .then(saveInOpsQueueIfCritical)
      .then(determineIfOperationalError);
  }

  this.isTrustedError = function (error) {
    return error.isOperational;
  }
}
```

### 블로그 인용: "가장 좋은 방법은 충돌이다"

Joyent 블로그에서

> …프로그래머 에러를 복구하는 가장 좋은 방법은 즉시 충돌하는 것이다. 충돌 시 자동으로 프로그램을 다시 시작하는 리스타터(restarter)를 사용하여 프로그램을 실행해야 한다. 리스타터(restarter)를 사용하는 경우, 일시적인 프로그래머 오류에 직면하면 신뢰할 수 있는 서비스를 복구하는 가장 빠른 방법은 충돌이다…

### 블로그 인용: "에러 처리에는 주로 세 가지 방법이 있다"

JS Recipes 블로그에서

> …에러 처리에는 주로 세 가지 방법이 있다:
1. 애플리케이션을 중지하고 다시 시작하기.
2. 가능한 모든 에러를 처리하고 충돌하지 않게 하기.
3. 이 두가지 사이의 균형잡힌 접근

### 블로그 인용: "정의되지 않은 취약 상태를 만들지 않고 떠나는 안전한 방법은 없다"

Node.js 공식문서에서

> …자바스크립트에서 던지기(throw) 방식의 특성상, 정의되지 않은 종류의 취약 상태를 만들거나 참조 누수 없이는, 안전하게 "중단한 곳에서 픽업(pick up)" 할 수 있는 방법은 거의 없다. 던져진 에러를 처리하는 가장 안전한 방법은 프로세스를 종료하는 것이다. 물론, 일반적인 웹 서버에서는 많은 연결이 열려 있을 수 있으며, 다른 누군가에 의해 오류가 발생했을 수도 있기 때문에 갑자기 연결을 종료하는 것은 적절하지 않다. 더 나은 방법은 다른 사람들이 정상적인 시간에 완료할 수 있도록 하면서 에러를 발생시킨 요청에 대한 에러 응답을 보내고, 새 요청에 대한 응답을 중지하는 것이다.
