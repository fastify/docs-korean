<h1 align="center">Fastify</h1>

## Request
핸들러 함수의 첫 번째 매개변수는 `Request` 입니다.

Request 는 다음 필드를 포함하는 Fastify 객체의 핵심입니다:
- `query` - 문법적으로 분석된 쿼리스트링, 이의 형식은 [`querystringParser`](./Server.md#querystringparser) 에 의해 지정됩니다.
- `body` - 요청 페이로드, Fastify 가 기본적으로 문법적으로 분석하는 요청 페이로드와 어떻게 다른 콘텐츠 유형을 지원하는지에 대한 세부 정보는
  [Content-Type Parser](./ContentTypeParser.md) 를 참조하십시오.
- `params` - URL 헤더와 일치하는 params
- [`headers`](#headers) - 헤더의 getter 와 setter
- `raw` - Node core 에서 들어오는 HTTP 요청
- `server` - 현재 [캡슐화 컨텍스트](./Encapsulation.md)에 대해 스코프된 Fastify 서버 인스턴스
- `id` - 요청 ID
- `log` - 요청에 들어오는 로그 인스턴스
- `ip` - 요청에 들어오는 IP 주소
- `ips` - 가장 가까운 IP 주소부터 가장 먼 IP 주소까지의 주소 배열이 `X-Forwarded-For` 헤더에 들어 있습니다.
  (단, [`trustProxy`](./Server.md#factory-trust-proxy) 옵션이 활성화되어 있어야 합니다.)
- `hostname` - 요청에 들어오는 호스트 ([`trustProxy`](./Server.md#factory-trust-proxy) 옵션이 활성화되어 있을 때,
  `X-Forwarded-Host` 헤더에서 파생됩니다). HTTP/2 호환성을 위해 호스트 헤더가 존재하지 않으면 `:authority` 를 반환합니다.
- `protocol` - 요청에 들어오는 프로토콜 (`https` or `http`)
- `method` - 요청에 들어오는 메소드
- `url` - 요청에 들어오는 URL
- `routerMethod` - 요청을 처리하는 라우터에 정의된 메소드
- `routerPath` - 요청을 처리하는 라우터에 정의된 경로 패턴
- `is404` - 404 핸들러가 요청을 처리하고 있으면 true, 처리하지 않고 있으면 false.
- `connection` - 사용되지 않음, 대신 `socket` 을 사용하세요. 요청에 들어오는 기반 연결.
- `socket` - 요청에 들어오는 기반 연결.
- `context` - Fastify 의 내부 객체.당신은 직접적으로 사용하거나 수정하지 마십시오.
  이것은 특정 키에 액세스하는 데 유용합니다:
  - `context.config` - 경로[`구성`](./Routes.md#routes-config)객체.
- routeSchema - 요청을 처리하는 라우터에 설정된 스키마 정의
- routeConfig - 경로[`구성`](./Routes.md#routes-config)객체.
- routeOptions - 경로[`옵션`](./Routes.md#routes-options)객체
  - bodyLimit - 서버 제한 또는 경로 제한
  - method - 경로의 HTTP 메소드
  - url - 이 경로와 일치할 URL 의 경로
  - attachValidation - `validationError` 를 요청에 첨부 (스키마가 정의되어 있을 경우)
  - logLevel - 이 경로에 대한 로그 레벨 정의
  - version - 엔드 포인트의 버전을 정의하는 semver 호환 문자열
  - exposeHeadRoute - GET 경로에 대한 자식 HEAD 경로 생성
  - prefixTrailingSlash - 접두사가 있을 경우 /를 경로로 전달하는 방식을 결정하는 문자열.

### Headers

`request.headers` 는 들어오는 요청의 헤더를 가진 객체를 반환하는 getter 입니다.
아래처럼 사용자 정의 헤더를 설정할 수 있습니다:

```js
request.headers = {
  'foo': 'bar',
  'baz': 'qux'
}
```
이 작업은 `request.headers.bar` 를 호출하여 읽을 수 있는 새로운 값을 요청 헤더에 추가합니다.
또한, `request.raw.headers` 속성을 사용하여 표준 요청 헤더에 액세스 할 수 있습니다.

> Note: 성능상의 이유로 `not found` 경로에서 헤더에 Symbol('fastify.RequestAcceptVersion') 추가 속성을 볼 수 있습니다.

```js
fastify.post('/:params', options, function (request, reply) {
  console.log(request.body)
  console.log(request.query)
  console.log(request.params)
  console.log(request.headers)
  console.log(request.raw)
  console.log(request.server)
  console.log(request.id)
  console.log(request.ip)
  console.log(request.ips)
  console.log(request.hostname)
  console.log(request.protocol)
  console.log(request.url)
  console.log(request.routerMethod)
  console.log(request.routerPath)
  request.log.info('some info')
})
```
