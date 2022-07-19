<h1 align="center">Fastify</h1>

## Fluent Schema

[유효성 검사 및 직렬화](../Reference/Validation-and-Serialization.md) 문서는 
JSON 스키마 유효성 검사를 설정하기 위해 Fastify에서 허용하는 모든 매개변수를 설명합니다.
JSON 스키마를 사용해 입력의 유효성을 검사하고 직렬화를 통해 출력을 최적화합니다.

[`fluent-json-schema`](https://github.com/fastify/fluent-json-schema) 를
사용하여 상수의 재사용을 허용하면서 이 작업을 단순화할 수 있습니다.

### 기본 설정

```js
const S = require('fluent-json-schema')

// 아래와 같은 개체를 사용하거나 DB를 쿼리하여 값을 가져올 수 있습니다.
const MY_KEYS = {
  KEY1: 'ONE',
  KEY2: 'TWO'
}

const bodyJsonSchema = S.object()
  .prop('someKey', S.string())
  .prop('someOtherKey', S.number())
  .prop('requiredKey', S.array().maxItems(3).items(S.integer()).required())
  .prop('nullableKey', S.mixed([S.TYPES.NUMBER, S.TYPES.NULL]))
  .prop('multipleTypesKey', S.mixed([S.TYPES.BOOLEAN, S.TYPES.NUMBER]))
  .prop('multipleRestrictedTypesKey', S.oneOf([S.string().maxLength(5), S.number().minimum(10)]))
  .prop('enumKey', S.enum(Object.values(MY_KEYS)))
  .prop('notTypeKey', S.not(S.array()))

const queryStringJsonSchema = S.object()
  .prop('name', S.string())
  .prop('excitement', S.integer())

const paramsJsonSchema = S.object()
  .prop('par1', S.string())
  .prop('par2', S.integer())

const headersJsonSchema = S.object()
  .prop('x-foo', S.string().required())

// `.valueOf()`는 호출할 필요가 없다는 것에 주의하세요!
const schema = {
  body: bodyJsonSchema,
  querystring: queryStringJsonSchema, // (or) query: queryStringJsonSchema
  params: paramsJsonSchema,
  headers: headersJsonSchema
}

fastify.post('/the/url', { schema }, handler)
```

### 재사용

`fluent-json-schema`를 사용하면 스키마를 보다 쉽고 프로그래밍적으로 다루고 
`add Schema()` 메서드로 재사용할 수 있습니다. [유효성 검사 및 직렬화](../Reference/Validation-and-Serialization.md#adding-a-shared-schema) 
문서에 자세히 설명되어 있는 두 가지 다른 방법으로 스키마를 참조할 수 있습니다.

몇 가지 사용 예는 다음과 같습니다:

**`$ref-way`**: 외부 스키마를 참조합니다.

```js
const addressSchema = S.object()
  .id('#address')
  .prop('line1').required()
  .prop('line2')
  .prop('country').required()
  .prop('city').required()
  .prop('zipcode').required()

const commonSchemas = S.object()
  .id('https://fastify/demo')
  .definition('addressSchema', addressSchema)
  .definition('otherSchema', otherSchema) // 필요한 스키마를 추가할 수 있습니다.

fastify.addSchema(commonSchemas)

const bodyJsonSchema = S.object()
  .prop('residence', S.ref('https://fastify/demo#address')).required()
  .prop('office', S.ref('https://fastify/demo#/definitions/addressSchema')).required()

const schema = { body: bodyJsonSchema }

fastify.post('/the/url', { schema }, handler)
```


**`replace-way`**: 유효성 검사 프로세스 전에 대체할 공유 스키마를 참조하십시오.

```js
const sharedAddressSchema = {
  $id: 'sharedAddress',
  type: 'object',
  required: ['line1', 'country', 'city', 'zipcode'],
  properties: {
    line1: { type: 'string' },
    line2: { type: 'string' },
    country: { type: 'string' },
    city: { type: 'string' },
    zipcode: { type: 'string' }
  }
}
fastify.addSchema(sharedAddressSchema)

const bodyJsonSchema = {
  type: 'object',
  properties: {
    vacation: 'sharedAddress#'
  }
}

const schema = { body: bodyJsonSchema }

fastify.post('/the/url', { schema }, handler)
```

`fastify.addSchema`를 사용할 때 `$ref-way`와 `replace-way`를 같이 사용할 수 있습니다.
