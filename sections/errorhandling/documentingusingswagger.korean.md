# Swagger를 이용해 API 에러를 문서화하라

### 한문단 설명

REST API는 HTTP 상태 코드를 사용하여 결과를 반환한다. API 사용자는 API 스키마뿐만 아니라 잠재적인 에러에 대해서도 알고 있어야 한다 - 호출자가 오류를 포착하고 재치 있게 처리할 수 있다. 예를 들어 API가 새 사용자를 등록한다고 가정할 경우 API 문서에는 고객 이름이 이미 존재할 때 HTTP 상태 409가 반환되어 호출자가 주어진 상황에 가장 적합한 UX를 상응하게 렌더링할 수 있다고 미리 명시되어 있을 수 있다. Swagger는 온라인에서 쉽게 문서를 작성할 수 있도록 하는 API 문서의 스키마를 정의하는 표준이다.

If you have already adopted GraphQL for your API endpoints, your schema already contains strict guarantees as to what errors should look like ([outlined in the spec](https://facebook.github.io/graphql/June2018/#sec-Errors)) and how they should be handled by your client-side tooling. In addition, you can also supplement them with comment-based documentation.

API 엔드포인트에 대해 GraphQL을 이미 채택한 경우 스키마에는 오류가 어떻게 표시되어야 하는지([사양에 설명됨](https://facebook.github.io/graphql/June2018/#sec-Errors ))와 클라이언트 측 도구에서 어떻게 처리할지에 대한 엄격한 조건이 이미 포함되어 있다. 또한 주석을 활용하여 문서를 보완할 수도 있습니다. 

### GraphQL 에러 예시

> 해당 예시는 [SWAPI](https://graphql.org/swapi-graphql), the Star Wars API를 사용한다.

```graphql
# should fail because id is not valid
{
  film(id: "1ZmlsbXM6MQ==") {
    title
  }
}
```

```json
{
  "errors": [
    {
      "message": "No entry in local cache for https://swapi.co/api/films/.../",
      "locations": [
        {
          "line": 2,
          "column": 3
        }
      ],
      "path": [
        "film"
      ]
    }
  ],
  "data": {
    "film": null
  }
}
```

### 블로그 인용: "호출자에게 어떤 에러가 발생할 수 있는지 알려줘야 한다"

Joyent 블로그에서 "Node.js logging" 키워드 1위에 위치했다

 > 에러를 처리하는 방법에 대해 이야기했지만 새로운 함수를 작성할 때 함수를 호출한 코드에 에러를 전달하는 방법은 무엇인가? …어떤 에러가 발생할 수 있는지 모르거나 에러가 의미하는 바를 모른다면 프로그램은 실수가 아닌 이상 정확할 수 없다. 따라서 새로운 함수를 작성하는 경우 호출자에게 어떤 에러가 발생할 수 있으며 그 에러의 의미를 알려야 한다…

### 유용한 도구(Tool): Swagger Online Documentation Creator

![Swagger API Scheme](https://github.com/i0natan/nodebestpractices/blob/master/assets/images/swaggerDoc.png "API error handling")
