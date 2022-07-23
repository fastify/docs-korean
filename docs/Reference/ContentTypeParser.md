<h1 align="center">Fastify</h1>

## `Content-Type` 파서(Parser)

기본적으로 Fastify는 `'application/json'`과 `'text/plain'` 콘텐츠 타입만 지원합니다.
다른 컨텐츠 타입을 사용하면 `FST_ERR_CTP_INVALID_MEDIA_TYPE` 에러가 발생합니다.

`utf-8` 인코딩이 기본값입니다. 다양한 컨텐츠 타입을 지원해야 하는 경우 `addContentTypeParser` API를 사용해야 합니다. _기본 JSON과 text 파서는 변경하거나 제거할 수 있습니다._

_참고: `Content-Type`헤더에 컨텐츠 타입을 지정한다면, UTF-8은 기본값이 아닙니다. `text/html; charset=utf-8`과 같이 UTF-8을 포함해야 합니다._

다른 API와 마찬가지로 `addContentTypeParser`는 선언된 스코프에 캡슐화됩니다.
즉, 루트 범위에 선언하면 모든 곳에서 사용할 수 있는 반면, 플러그인 내부에 선언하면 해당 플러그인과 그 하위 스코프에서만 사용 가능합니다.

Fastify는 파싱된 요청 페이로드를 [Fastify request](./Request.md) 객체에 자동으로 추가합니다. 이는 `request.body`로 접근할 수 있습니다.

`GET`과 `HEAD` 요청의 페이로드는 절대 파싱되지 않는다는 것에 주의하세요. `OPTIONS`와 `DELETE` 요청의 페이로드는 콘텐츠 타입 헤더에 콘텐츠 타입이 주어질 때만 파싱됩니다. 콘텐츠 타입이 주어지지 않으면, [catch-all](#catch-all) 파서는 `POST`, `PUT`, `PATCH`에서처럼 실행되지 않습니다. 페이로드는 그저 파싱되지 않습니다.

### 사용법

```js
fastify.addContentTypeParser(
  "application/jsoff",
  function (request, payload, done) {
    jsoffParser(payload, function (err, body) {
      done(err, body);
    });
  }
);

// 하나의 함수로 여러 Content-Type 처리하기
fastify.addContentTypeParser(
  ["text/xml", "application/xml"],
  function (request, payload, done) {
    xmlParser(payload, function (err, body) {
      done(err, body);
    });
  }
);

// Node 버전 >= 8.0.0부터는 async도 지원됩니다
fastify.addContentTypeParser(
  "application/jsoff",
  async function (request, payload) {
    var res = await jsoffParserAsync(payload);

    return res;
  }
);

// 정규식에 부합하는 모든 콘텐츠 타입을 처리하기
fastify.addContentTypeParser(/^image\/.*/, function (request, payload, done) {
  imageParser(payload, function (err, body) {
    done(err, body);
  });
});

// 기본적인 텍스트/JSON 파서를 다른 컨텐츠 타입에도 사용할 수 있습니다
fastify.addContentTypeParser(
  "text/json",
  { parseAs: "string" },
  fastify.getDefaultJsonParser("ignore", "ignore")
);
```

Fastify는 `RegExp`와 매칭되는 값을 찾기 전에 `string`으로 등록된 컨텐츠 타입 파서를 먼저 확인합니다. 겹치는 컨텐츠 타입을 작성하는 경우, Fastify는 마지막으로 주어진 컨텐츠 타입부터 사용하려고 할 것입니다. 따라서 범용 컨텐츠 타입을 보다 세부적으로 지정하려면, 아래의 예처럼 먼저 범용 컨텐츠 타입을 지정한 다음 구체적인 타입을 지정하세요.

```js
// 여기에서는 첫 번째의 것도 매칭되기 때문에 두 번째 컨텐츠 타입 파서만 호출됩니다
fastify.addContentTypeParser(
  "application/vnd.custom+xml",
  (request, body, done) => {}
);
fastify.addContentTypeParser(
  "application/vnd.custom",
  (request, body, done) => {}
);

//fastify가 `application/vnd.custom+xml`을 먼저 매칭하려고 하기 때문에 이것이 우리가 원했던 동작입니다
fastify.addContentTypeParser(
  "application/vnd.custom",
  (request, body, done) => {}
);
fastify.addContentTypeParser(
  "application/vnd.custom+xml",
  (request, body, done) => {}
);
```

`addContentTypeParser` API 뿐만 아니라 사용 가능한 더 많은 API들이 있습니다.
`hasContentTypeParser`, `removeContentTypeParser`, 그리고 `removeAllContentTypeParsers`도 있습니다.

#### hasContentTypeParser

특정한 컨텐츠 타입 파서가 있는지 확인하기 위해 `hasContentTypeParser`를 사용할 수 있습니다.

```js
if (!fastify.hasContentTypeParser("application/jsoff")) {
  fastify.addContentTypeParser(
    "application/jsoff",
    function (request, payload, done) {
      jsoffParser(payload, function (err, body) {
        done(err, body);
      });
    }
  );
}
```

=======

#### removeContentTypeParser

`removeContentTypeParser`를 사용하면 하나의 혹은 배열로 된 컨텐츠 타입을 제거할 수 있습니다.
이 메서드는 `string`과 `RegExp`로 된 컨텐츠 타입 모두를 지원합니다.

```js
fastify.addContentTypeParser("text/xml", function (request, payload, done) {
  xmlParser(payload, function (err, body) {
    done(err, body);
  });
});

// text/html만 사용 가능하도록 두 개의 내장된 컨텐츠 타입 파서를 제거합니다
fastify.removeContentTypeParser(["application/json", "text/plain"]);
```

#### removeAllContentTypeParsers

위의 예제를 통해 제거하고자 하는 컨텐츠 타입을 모두 직접 지정해주어야 한다는 점을 알 수 있습니다.
이 문제를 해결하기 위해 Fastify는 `removeAllContentTypeParsers` API를 제공합니다.
이를 이용해 현재 존재하는 모든 컨텐츠 타입 파서를 제거할 수 있습니다.
아래 예제에서 우리는 제거하려는 컨텐츠 타입 파서를 직접 지정하지 않고도, 위 예제와 같은 동작을 구현할 수 있습니다.
`removeContentTypeParser`와 마찬가지로 이 API도 캡슐화를 지원합니다.
내장 파서들을 무시하면서 모든 컨텐츠 타입에서 실행되어야 하는 [catch-all 컨텐츠 타입 파서](#Catch-All)를 등록하려는 경우 특히 유용합니다.

```js
fastify.removeAllContentTypeParsers();

fastify.addContentTypeParser("text/xml", function (request, payload, done) {
  xmlParser(payload, function (err, body) {
    done(err, body);
  });
});
```

**경고**: `function (req, done)`이나 `async function (req)`와 같은 오래된 문법들은 여전히 지원되지만 더 이상 사용되지는 않습니다.

#### Body 파서

요청 바디는 2가지 방법으로 파싱할 수 있습니다. 첫 번째 방법은 위에 설명했듯, 사용자 정의 컨텐츠 타입 파서를 추가하고 요청 스트림을 처리하는 것입니다.
두 번째는 바디를 어떻게 가져올지 선언하는 `addContentTypeParser` API에 `parseAs` 옵션을 전달하는 것입니다.
`parseAs` 옵션은 `'string'`이나 `'buffer'` 타입일 수 있습니다. 이 옵션을 사용하면 Fastify는 내부에서 스트림을 처리하고 바디의 [최대 크기](./Server.md#factory-body-limit)나 컨텐츠 길이와 같은 것을 검사합니다.
이러한 제한을 초과하면 사용자 정의 파서는 호출되지 않습니다.

```js
fastify.addContentTypeParser(
  "application/json",
  { parseAs: "string" },
  function (req, body, done) {
    try {
      var json = JSON.parse(body);
      done(null, json);
    } catch (err) {
      err.statusCode = 400;
      done(err, undefined);
    }
  }
);
```

예시를 확인하려면 [`example/parser.js`](https://github.com/fastify/fastify/blob/main/examples/parser.js)를 참고하세요.

##### 커스텀 파서 옵션

- `parseAs` (string): `'string'`과 `'buffer'` 모두 들어오는 데이터를 수집하는 방법을 지정합니다. 기본값은 `'buffer'`입니다.
- `bodyLimit` (number): 커스텀 파서가 허용하는 최대 페이로드 크기(바이트)입니다. 기본값은 [`Fastify factory function`](./Server.md#bodylimit)에 전달된 글로벌 바디 크기로 제한합니다.

#### Catch-All

컨텐츠 타입과 상관없이 모든 요청을 처리해야 하는 몇몇 상황들이 있습니다.
Fastify를 사용하면 `'*'` 컨텐츠 타입을 지정하기만 하면 됩니다.

```js
fastify.addContentTypeParser("*", function (request, payload, done) {
  var data = "";
  payload.on("data", (chunk) => {
    data += chunk;
  });
  payload.on("end", () => {
    done(null, data);
  });
});
```

이를 사용하면 해당 컨텐츠 타입 파서가 없는 모든 요청들은 지정된 함수에서 처리합니다.

또한 요청 스트림을 파이핑하는 데에도 유용합니다.
컨텐츠 파서를 다음과 같이 정의할 수 있습니다:

```js
fastify.addContentTypeParser("*", function (request, payload, done) {
  done();
});
```

그런 다음 원하는 위치에 파이핑하기 위해 코어 HTTP 요청에 직접 접근합니다:

```js
app.post("/hello", (request, reply) => {
  reply.send(request.raw);
});
```

다음은 들어오는 [json line](https://jsonlines.org/) 객체를 로그하는 완전한 예제입니다:

```js
const split2 = require("split2");
const pump = require("pump");

fastify.addContentTypeParser("*", (request, payload, done) => {
  done(null, pump(payload, split2(JSON.parse)));
});

fastify.route({
  method: "POST",
  url: "/api/log/jsons",
  handler: (req, res) => {
    req.body.on("data", (d) => console.log(d)); // 들어오는 모든 객체를 로그합니다
  },
});
```

파일 업로드를 파이핑할 때에는 [이 플러그인](https://github.com/fastify/fastify-multipart)에 관심이 있을 겁니다.

컨텐츠 타입 파서를 컨텐츠 타입이 없는 것들 뿐만 아니라 모든 컨텐츠 타입에서 실행하기를 원한다면, `removeAllContentTypeParsers`메서드를 먼저 호출해야 합니다.

```js
// 이것을 실행하지 않는다면 application/json 타입의 요청 바디는 내장된 JSON 파서에 의해 처리됩니다.
fastify.removeAllContentTypeParsers();

fastify.addContentTypeParser("*", function (request, payload, done) {
  var data = "";
  payload.on("data", (chunk) => {
    data += chunk;
  });
  payload.on("end", () => {
    done(null, data);
  });
});
```
