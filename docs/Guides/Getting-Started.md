<h1 align="center">Fastify</h1>

## 시작하기

안녕하세요! Fastify를 찾아주셔서 감사합니다!

본 문서는 Fastify 프레임워크의 특징을 쉽고 간단히 알아볼 수 있도록 하는데 초점을 맞췄습니다. 본 문서는 쉽고 간단한 소개문이며 동시에 다른 문서들의 진입점 역할을 합니다.

그러면 시작해봅시다!

### 설치
<a id="install"></a>

npm으로 설치하기:
```
npm i fastify
```
yarn으로 설치하기:
```
yarn add fastify
```

### 첫번째 서버
<a id="first-server"></a>

다음과 같이 첫번째 서버를 위한 코드를 작성해봅시다.
```js
// 프레임워크를 가져와 인스턴스화 합니다

// ESM
import Fastify from 'fastify'
const fastify = Fastify({
  logger: true
})
// CommonJs
const fastify = require('fastify')({
  logger: true
})

// 라우트를 정의합니다.
fastify.get('/', function (request, reply) {
  reply.send({ hello: 'world' })
})

// 서버를 실행합니다!
fastify.listen({ port: 3000 }, function (err, address) {
  if (err) {
    fastify.log.error(err)
    process.exit(1)
  }
  // Server is now listening on ${address}
})
```

`async/await` 사용을 더 선호 하시나요? Fastify는 당연하게도 이를 지원합니다. <!-- https://web-front-end.tistory.com/84 -->

```js
// ESM
import Fastify from 'fastify'
const fastify = Fastify({
  logger: true
})
// CommonJs
const fastify = require('fastify')({
  logger: true
})

fastify.get('/', async (request, reply) => {
  return { hello: 'world' }
})

/**
 * 서버를 실행합니다!
 */
const start = async () => {
  try {
    await fastify.listen({ port: 3000 })
  } catch (err) {
    fastify.log.error(err)
    process.exit(1)
  }
}
start()
```

훌륭합니다! 쉽게 구현했습니다.

그러나 불운하게도, 이보다 복잡한 어플리케이션을 만드려면 훨씬 많은 코드를 작성해야합니다.
새로운 어플리케이션을 구현해야할 때 부딫히는 고전적인 문제로는 많은 파일들을 어떻게 다룰지, 비동기적인 부트스트래핑을 어떻게 처리할지, 코드의 아키텍처는 어떻게 구성할지와 같은 것들입니다.

Fastify는 이와 같은 문제 이외에도 더 많은 문제들을 쉽게 해결할 수 있는 방법(platform)을 제공합니다!

> ## 비고
> 위에서 보인 예시에서, 로컬호스트인 `127.0.0.1` 만 접속을 허용하도록 기본 설정이 되어 있습니다.
> 모든 IPv4 주소로 부터 접속을 허용하도록 하기 위해서는 다음과 같이 `0.0.0.0`로 변경해야합니다:
>
> ```js
> fastify.listen({ port: 3000, host: '0.0.0.0' }, function (err, address) {
>   if (err) {
>     fastify.log.error(err)
>     process.exit(1)
>   }
>   fastify.log.info(`server listening on ${address}`)
> })
> ```
>
> 유사하게, `::1` 는 IPv6 로컬접속만을 허용합니다. 다음과 같이
> 운영체제에서 IPv6를 지원하는 경우 `::`는 모든 IPv6 주소의 접속을 받아들일 수 있으며, 이는 IPv4 주소 역시 마찬가지입니다.
>
> 도커(Docker)를 사용해 배포하는 경우 (혹은 이외의 방식이더라도) 컨테이너 포트를 `0.0.0.0` 또는
> `::` 로 설정하면 쉽게 외부로 공개할 수 있습니다.

### 첫번째 플러그인
<a id="first-plugin"></a>

자바스크립트에서 모든것이 객체로 취급되듯이, Fastify에서는 모든 것들이 Plugin으로 다뤄질 수 있습니다.

본격적으로 들어가기 전에 한 번 어떻게 작동하는지 확인해봅시다.


먼저 기본 서버를 구성해봅시다, 서버 진입점에 라우트를 정의하는 대신에 외부 파일로 정의할 것입니다.([라우트 정의](../Reference/Routes.md) 문서에 대해 확인해보세요).
```js
// ESM
import Fastify from 'fastify'
import firstRoute from './our-first-route'
/**
 * @type {import('fastify').FastifyInstance} Fastify 인스턴스
 */
const fastify = Fastify({
  logger: true
})

fastify.register(firstRoute)

fastify.listen({ port: 3000 }, function (err, address) {
  if (err) {
    fastify.log.error(err)
    process.exit(1)
  }
  // Server is now listening on ${address}
})
```

```js
// CommonJs
/**
 * @type {import('fastify').FastifyInstance} Fastify 인스턴스
 */
const fastify = require('fastify')({
  logger: true
})

fastify.register(require('./our-first-route'))

fastify.listen({ port: 3000 }, function (err, address) {
  if (err) {
    fastify.log.error(err)
    process.exit(1)
  }
  // Server is now listening on ${address}
})
```

```js
// our-first-route.js

/**
 * 라우트를 캡슐화 합니다.
 * @param {FastifyInstance} fastify 캡슐화된 Fastify 인스턴스
 * @param {Object} options 플러그인 옵션, https://www.fastify.io/docs/latest/Reference/Plugins/#plugin-options 를 참조하세요
 */
async function routes (fastify, options) {
  fastify.get('/', async (request, reply) => {
    return { hello: 'world' }
  })
}

module.exports = routes
```
이번 에제에서는, Fastify 프레임워크의 핵심이 되는 `register` API를 사용했습니다. 이는 라우트(routes)나 플러그인(plugins) 기타 등등을 추가하기 위한 유일한 방법입니다.

해당 가이드의 처음부분에서, Fastify가 비동기적 방식의 부트스트래핑(bootstrapping)의 기반을 제공한다고 이야기했습니다.
이것이 왜 중요할까요?

데이터 저장을 다루기 위해 데이터베이스 연길이 필요한 시나리오를 고려해봅시다. 데이터베이스 연결은 서버가 연결을 받아들이기 전에 사용이 가능해야만 합니다.
어떻게 이러한 문제를 풀어나갈 수 있을까요?

고전적인 해결책으로는 복잡한 콜백함수 또는 프로미스를 사용하는 것입니다. - 이 경우 프레임워크 API 와 다른 라이브러리 및 어플리케이션 코드를 혼합하는 시스템이 됩니다.

Fastify는 이러한 것들을 내부적으로 다루고 있으며, 최소한의 노력으로 실현가능하게 합니다!

그러면 위의 예제를 데이터베이스 연결로 재작성해봅시다.

첫번쨰로, `fastify-plugin` 와 `@fastify/mongodb`를 설치합니다:

```
npm i fastify-plugin @fastify/mongodb
```

**server.js**
```js
// ESM
import Fastify from 'fastify'
import dbConnector from './our-db-connector'
import firstRoute from './our-first-route'

/**
 * @type {import('fastify').FastifyInstance} Instance of Fastify
 */
const fastify = Fastify({
  logger: true
})
fastify.register(dbConnector)
fastify.register(firstRoute)

fastify.listen({ port: 3000 }, function (err, address) {
  if (err) {
    fastify.log.error(err)
    process.exit(1)
  }
  // Server is now listening on ${address}
})
```

```js
// CommonJs
/**
 * @type {import('fastify').FastifyInstance} Fastify 인스턴스
 */
const fastify = require('fastify')({
  logger: true
})

fastify.register(require('./our-db-connector'))
fastify.register(require('./our-first-route'))

fastify.listen({ port: 3000 }, function (err, address) {
  if (err) {
    fastify.log.error(err)
    process.exit(1)
  }
  // Server is now listening on ${address}
})

```

**our-db-connector.js**
```js
// ESM
import fastifyPlugin from 'fastify-plugin'
import fastifyMongo from '@fastify/mongodb'

/**
 * @param {FastifyInstance} fastify
 * @param {Object} options
 */
async function dbConnector (fastify, options) {
  fastify.register(fastifyMongo, {
    url: 'mongodb://localhost:27017/test_database'
  })
}

// fastify 플러그인으로 플러그인 함수를 감싸면, 플러그인 내부의 상위 스코프에 선언된 데코레이터와 훅을 노출합니다.
module.exports = fastifyPlugin(dbConnector)

```

```js
// CommonJs
/**
 * @type {import('fastify-plugin').FastifyPlugin}
 */
const fastifyPlugin = require('fastify-plugin')


/**
 * MongoDB 데이터베이스에 연결합니다.
 * @param {FastifyInstance} fastify 캡슐화된 Fastify 인스턴스
 * @param {Object} options 플러그인 옵션, https://www.fastify.io/docs/latest/Reference/Plugins/#plugin-options 를 참조하세요
 */
async function dbConnector (fastify, options) {
  fastify.register(require('@fastify/mongodb'), {
    url: 'mongodb://localhost:27017/test_database'
  })
}

// fastify 플러그인으로 플러그인 함수를 감싸면, 플러그인 내부의 상위 스코프에 선언된 데코레이터와 훅을 노출합니다.
module.exports = fastifyPlugin(dbConnector)

```

**our-first-route.js**
```js
/**
 * 캡슐화된 라우트를 제공하는 플러그인
 * @param {FastifyInstance} fastify 캡슐화된 Fastify 인스턴스
 * @param {Object} options 플러그인 옵션, https://www.fastify.io/docs/latest/Reference/Plugins/#plugin-options 를 참조하세요
 */
async function routes (fastify, options) {
  const collection = fastify.mongo.db.collection('test_collection')

  fastify.get('/', async (request, reply) => {
    return { hello: 'world' }
  })

  fastify.get('/animals', async (request, reply) => {
    const result = await collection.find().toArray()
    if (result.length === 0) {
      throw new Error('No documents found')
    }
    return result
  })

  fastify.get('/animals/:animal', async (request, reply) => {
    const result = await collection.findOne({ animal: request.params.animal })
    if (!result) {
      throw new Error('Invalid value')
    }
    return result
  })

  const animalBodyJsonSchema = {
    type: 'object',
    required: ['animal'],
    properties: {
      animal: { type: 'string' },
    },
  }

  const schema = {
    body: animalBodyJsonSchema,
  }

  fastify.post('/animals', { schema }, async (request, reply) => {
    // we can use the `request.body` object to get the data sent by the client
    const result = await collection.insertOne({ animal: request.body.animal })
    return result
  })
}

module.exports = routes
```

세상에, 정말 빠르네요!

지금까지 새로운 개념들을 소개하기까지 우리가 무엇을 했었는지 한 번 되돌아봅시다.

알다시피, `register` 를 데이터베이스 커넥터와 라우팅 등록에 사용했습니다.

이것은 Fastify의 최고 기능 중 하나입니다. 정의하는 순서대로 플러그인을 가져올 것이며,
하나의 플러그인을 가져오면 그 다음 플러그인을 가져옵니다. 이러한 방식으로 데이터베이스 커넥터를 가장 먼저 등록한 후
다음 차례에 사용합니다. *(플러그인의 스코프를 다루는 법을 이해하기 위해서는 [이곳을](../Reference/Plugins.md#handle-the-scope))을 읽어보세요)

플러그인의 불러오기는 `fastify.listen()`, `fastify.inject()` 또는 `fastify.ready()`를 사용할 때 시작합니다.

MongoDB 플러그인은 `decorate` API를 사용해 전역적으로 사용가능한 Fastify 인스턴스에 커스텀 객체를 추가합니다.
해당 API의 사용은 쉬운 코드 재사용과 코드 감소 그리고 로직의 중복 제거를 쉽게 도와줍니다.

Fastify의 플러그인이 어떻게 작동하는지, 새로운 플러그인은 어떻게 개발하는지, 어플리케이션을 비동기적으로 부트스래핑하는 복잡함을 해결하기 위한 Fastify의 모든 API 세부사항을
더 자세히 알기위해서는 [플러그인을 여행하는 히치하이커를 위한 안내서](./Plugins-Guide.md) 를 읽어보세요.

### 플러그인 순서대로 불러오기
<a id="plugin-loading-order"></a>

어플리케이션의 일관적이고 예측가능한 행동을 보장하기 위해, 다음과 같이 코드를 순차적으로 불러오는 걸 걍력히 권장합니다:
```
└── plugins (from the Fastify ecosystem)
└── your plugins (your custom plugins)
└── decorators
└── hooks
└── your services
```
이러한 방식으로, 현재 스코프에서 정의된 모든 속성에 대해 언제든지 접근할 수 있습니다.

이전에 논의했듯이, Fastify는 견고한 캡슐화 모델을 제공하며, 독립적인 하나의 서비스로써 어플리케이션을 구성하도록 도와줍니다.
만약 플러그인을 라우트의 하위 집합만으로 플러그인을 등록하기를 원한다면, 위의 구조를 그대로 복제하면 그만입니다.
```
└── plugins (from the Fastify ecosystem)
└── your plugins (your custom plugins)
└── decorators
└── hooks
└── your services
    │
    └──  service A
    │     └── plugins (from the Fastify ecosystem)
    │     └── your plugins (your custom plugins)
    │     └── decorators
    │     └── hooks
    │     └── your services
    │
    └──  service B
          └── plugins (from the Fastify ecosystem)
          └── your plugins (your custom plugins)
          └── decorators
          └── hooks
          └── your services
```

### 데이터 검증하기
<a id="validate-data"></a>

데이터의 검증은 아주 중요하며 프레임워크의 핵심 개념이기도 합니다.

들어오는 요청에 대해 검증하기 위해 Fastify는 [JSON 스키마](https://json-schema.org/)를 사용합니다.
(JTD 스키마도 약하게(loosely) 지원합니다, 그러나 `jsonShorthand` 옵션은 반드시 비활성화 되어야 합니다)

라우트에서 데이터 검증에 대해 확인해보기 위해 에제를 한 번 살펴봅시다:
```js
/**
 * @type {import('fastify').RouteShorthandOptions}
 * @const
 */
const opts = {
  schema: {
    body: {
      type: 'object',
      properties: {
        someKey: { type: 'string' },
        someOtherKey: { type: 'number' }
      }
    }
  }
}

fastify.post('/', opts, async (request, reply) => {
  return { hello: 'world' }
})
```
이번 예제에서는 어떻게 라우트로 옵션 객체를 던져줄 수 있는지 보여줍니다. 라우트와 관련한 모든 스키마(`body`, `querystring`,
`params`, 그리고 `headers`)를 포함하는 `schema` 키를 받을 수 있습니다.

더 많은 것을 배우려면 [검증과 직렬화](../Reference/Validation-and-Serialization.md)을 읽어보세요.

### 데이터 직렬화
<a id="serialize-data"></a>

Fastify는 JSON에 대한 지원을 아주 잘 지원합니다. 그리고 JSON 본문을 파싱하고 JSON 출력을 직렬화 하는데 있어 최적화가 아주 잘 되어있습니다.

JSON 직렬화의 속도롤 높이려면 (그렇습니다. 직렬화는 느립니다!) 다음 예제에서 보이는 것처럼 스키마 옵션의 `response` 키 를 사용하세요:
```js
/**
 * @type {import('fastify').RouteShorthandOptions}
 * @const
 */
const opts = {
  schema: {
    response: {
      200: {
        type: 'object',
        properties: {
          hello: { type: 'string' }
        }
      }
    }
  }
}

fastify.get('/', opts, async (request, reply) => {
  return { hello: 'world' }
})
```
보이는 것과 같이 스키마를 명시하면, 직렬화 속도를 2-3 배정도 빠르게 할 수 있습니다.
또한 잠재적인 중요 데이터의 누락으로부터 도와주기도 합니다. Fastify는 현재 스키마에 보이는 데이터에 대해서만 직렬화를 하기 때문입니다.
관련해서 자세한 내용은 [Validation and Serialization](../Reference/Validation-and-Serialization.md)를 참조하세요.

### 요청 페이로드(payload) 파싱
<a id="request-payload"></a>

Fastify는 `'application/json'`과 `'text/plain'` 요청 페이로드를 기본적으로 파싱하미,
[Fastify요청](../Reference/Request.md) 객체의 `request.body` 에서 파싱된 결과를 접근할 수 있습니다.

다음 예제에서는 파싱된 요청의 본문을 클라이언트에 되돌려줍니다:

```js
/**
 * @type {import('fastify').RouteShorthandOptions}
 */
const opts = {}
fastify.post('/', opts, async (request, reply) => {
  return request.body
})
```

Fastif의 기본 파싱 기능과 다른 콘턴츠 타입을 지원하려면 어떻게 해야하는지 자세히 알아보려면 [Content-Type Parser](../Reference/ContentTypeParser.md) 을 읽어보세요.

### 서버 확장하기
<a id="extend-server"></a>

Fastify는 최대한 확장가능하면서도 최소한의 기능을 제공하기 위해 만들어졌습니다.
우리는 기본 뼈대만 있는 프레임워크가 좋은 어플리케이션을 만들기 위한 필수 요소의 모든 것이라 믿습니다.

달리 말하면, Fastify는 "배터리가 내장된" 프레임워크가 아니며, 훌륭한 [생태계](./Ecosystem.md)가 이를 뒷받침하고 있습니다.

### 서버 테스트하기
<a id="test-server"></a>

Fastify는 테스팅 프레임워크를 제공하지 않고 있지만, 기능과 Fastify 아키텍처에 대해 테스트를 작성하는 것을 추천합니다.

자세한 내용은 [테스팅](./Testing.md) 문서를 읽어보세요!

### CLI 에세 서버 실행하기
<a id="cli"></a>

Fastify [fastify-cli](https://github.com/fastify/fastify-cli) 덕분에 CLI 통합도 가능합니다.

먼저, `fastify-cli`를 설치합니다:

```
npm i fastify-cli
```

또한 `-g` 옵션을 사용해 전역적으로 설치하는 것도 가능합니다.

그런다음 `package.json`에 다음과 같은 라인을 추가합니다:
```json
{
  "scripts": {
    "start": "fastify start server.js"
  }
}
```

그리고 서버 파일을 다음과 같이 생성합니다(s):
```js
// server.js
'use strict'

module.exports = async function (fastify, opts) {
  fastify.get('/', async (request, reply) => {
    return { hello: 'world' }
  })
}
```

그런뒤 서버를 다음과 같이 실행합니다:
```bash
npm start
```

### 발표자료 및 영상
<a id="slides"></a>

- Slides
- [Take your HTTP server to ludicrous
speed](https://mcollina.github.io/take-your-http-server-to-ludicrous-speed)
by [@mcollina](https://github.com/mcollina)
- [What if I told you that HTTP can be
fast](https://delvedor.github.io/What-if-I-told-you-that-HTTP-can-be-fast)
by [@delvedor](https://github.com/delvedor)

- Videos
- [Take your HTTP server to ludicrous
speed](https://www.youtube.com/watch?v=5z46jJZNe8k) by
[@mcollina](https://github.com/mcollina)
- [What if I told you that HTTP can be
fast](https://www.webexpo.net/prague2017/talk/what-if-i-told-you-that-http-can-be-fast/)
by [@delvedor](https://github.com/delvedor)
