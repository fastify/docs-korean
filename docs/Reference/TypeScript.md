<h1 align="center">Fastify</h1>

## 타입스크립트

Fastify 프레임워크는 바닐라 자바스크립트로 작성되어 타입 정의를 유지하기 쉽지 않습니다. 그러나 버전 2부터 운영자와 기여자는 타입을 개선하기 위해 많은 노력을 기울여 왔습니다.

타입 시스템은 Fastify 버전 3에서 변경되었습니다. 새로운 타입 시스템은 제네릭과 디폴트값 설정을 지원하고 요청 바디, 쿼리스트링 등과 같은 스키마 타입을 정의하는 새로운 방법을 도입했습니다! 저희 팀이 프레임워크와 타입의 시너지를 개선하기 위해 작업하면서, 가끔은 API의 일부에 타입이 적용되지 않거나 잘못 타입이 적용될 수 있습니다. 부족한 부분을 채울 수 있도록 **기여**해주시기 바랍니다. 시작하기 전에 [`CONTRIBUTING.md`](https://github.com/fastify/fastify/blob/main/CONTRIBUTING.md) 파일을 읽으면 순조롭게 진행할 수 있을 겁니다!

> 이 섹션의 문서는 Fastify 버전 3.x의 타입을 다룹니다.

> 플러그인은 타입을 포함하거나 포함하지 않을 수 있습니다. 자세한 내용은 [Plugins](#plugins)을 참고하세요. 타입 지원을 개선하기 위해 PR을 보내주시면 좋겠습니다.

🚨 `@types/node`를 설치하는 것을 잊지 마세요.

## 예제로 배우기

Fastify 타입 시스템을 배우는 가장 좋은 방법은 예제입니다! 아래 네 가지 예제는 가장 일반적인 Fastify의 개발 사례를 다루고 있습니다. 그 다음엔 타입 시스템에 대한 더 자세한 문서가 나옵니다.

### 시작하기

이 예제는 Fastify와 Typescript를 시작하고 실행할 수 있도록 합니다. 그 결과 빈 http Fastify 서버가 생성됩니다.

1. 새 npm 프로젝트를 만들고, Fastify를 설치하고, typescript와 @types/node를 피어 의존성으로 설치합니다.

```bash
npm init -y
npm i fastify
npm i -D typescript @types/node
```

2. `package.json`의 `"scripts"`섹션에 아래 라인을 추가합니다.

```json
{
  "scripts": {
    "build": "tsc -p tsconfig.json",
    "start": "node index.js"
  }
}
```

3. 타입스크립트 설정 파일을 초기화합니다.

```bash
npx tsc --init
```

또는 [권장되는 것](https://github.com/tsconfig/bases#node-14-tsconfigjson) 중 하나를 사용하세요.

_주의: [FastifyDeprecation](https://github.com/fastify/fastify/issues/3284) 경고를 방지하려면, `tsconfig.json`의 `target`속성 을 `es2017` 또는 그 이상으로 설정하세요._

4. `index.ts` 파일을 만듭니다. - 여기에는 서버 코드를 포함합니다.
5. 아래 코드 블록을 파일에 추가합니다

   ```typescript
   import fastify from "fastify";

   const server = fastify();

   server.get("/ping", async (request, reply) => {
     return "pong\n";
   });

   server.listen({ port: 8080 }, (err, address) => {
     if (err) {
       console.error(err);
       process.exit(1);
     }
     console.log(`Server listening at ${address}`);
   });
   ```

6. `npm run build`를 실행합니다. - 이는 `index.ts`를 컴파일하여 Node.js에서 실행되는 `index.js`로 만듭니다. 만약 에러를 만나게 된다면 [fastify/help](https://github.com/fastify/help/)에 이슈를 생성해주세요.
7. `npm run start`로 Fastify 서버를 실행합니다.
8. 콘솔에서 `Server listening at http://127.0.0.1:8080`를 볼 겁니다.
9. `curl localhost:8080/ping`로 서버를 실행하면, `pong`🏓이 리턴될 겁니다.

🎉 이제 Typescript Fastify 서버가 작동합니다! 이 예제는 3.x버전 타입 시스템의 간결함을 보여줍니다. 디폴트로, 타입 시스템은 여러분이 `http` 서버를 사용한다고 가정합니다. 이후의 예제에서는 `https`나 `http2`, 스키마를 라우팅하는 방법 등 더 복잡한 서버를 만드는 방법을 보여줄 겁니다.

> Typescript로 Fastify를 시작하는 방법(예: HTTP2 사용)은 더 자세한 API 섹션인 [이곳][fastify]을 참고하세요.

### 제네릭 사용하기

타입 시스템은 가장 정확한 개발 경험을 제공하기 위해 제네릭 속성에 깊이 의존합니다. 일부 사용자는 오버헤드가 다소 번거롭다고 생각할 수 있지만, 그만한 가치가 있습니다! 이 예제는 스키마 라우팅과 라우트 레벨의 `request` 객체에 위치한 동적인 속성에 제네릭을 적용하는 법을 다룹니다.

1. 앞의 예제를 완료하지 않았다면, 1-4 단계를 수행하여 설정합니다.
2. `index.ts`에, 두 인터페이스 `IQuerystring`과 `IHeaders` 정의합니다

   ```typescript
   interface IQuerystring {
     username: string;
     password: string;
   }

   interface IHeaders {
     "h-Custom": string;
   }
   ```

3. 두 인터페이스를 사용하여 새 API 경로를 정의하고, 이를 제네릭으로 전달합니다. `.get`과 같은 약식 라우트 메서드는 다섯 가지 속성을 가진 제네릭 객체 `RouteGenericInterface`를 받습니다. `Body`, `Querystring`, `Params`, `Headers` 그리고 `Reply`가 그것입니다. 인터페이스 `Body`, `Querystring`, `Params` 그리고 `Headers`는 라우트 메서드를 통해 핸들러의 `request` 인스턴스로 전달됩니다. `Reply` 인터페이스는 `reply` 인스턴스로 전달됩니다.

   ```typescript
   server.get<{
     Querystring: IQuerystring;
     Headers: IHeaders;
   }>("/auth", async (request, reply) => {
     const { username, password } = request.query;
     const customerHeader = request.headers["h-Custom"];
     // request 데이터로 뭔가를 합니다.

     return `logged in!`;
   });
   ```

4. `npm run build`와 `npm run start`를 사용하여 서버 코드를 빌드하고 실행합니다.
5. API를 쿼리합니다.
   ```bash
   curl localhost:8080/auth?username=admin&password=Password123!
   ```
   `logged in!`이 리턴되어야 합니다!
6. 더 있습니다! 제네릭 인터페이스는 라우트 레벨의 훅 메서드에서도 사용 가능합니다. `preValidation` 훅을 추가해 이전 경로를 수정합니다

   ```typescript
   server.get<{
     Querystring: IQuerystring;
     Headers: IHeaders;
   }>(
     "/auth",
     {
       preValidation: (request, reply, done) => {
         const { username, password } = request.query;
         done(username !== "admin" ? new Error("Must be admin") : undefined); // `admin` 계정만 검증합니다
       },
     },
     async (request, reply) => {
       const customerHeader = request.headers["h-Custom"];
       // request 데이터로 뭔가를 합니다.
       return `logged in!`;
     }
   );
   ```

7. `username` 쿼리스트링 옵션을 `admin`이 아닌 것으로 설정해 빌드하고 실행해봅니다. API는 이제 HTTP 500 에러를 리턴해야 합니다.
   `{"statusCode":500,"error":"Internal Server Error","message":"Must be admin"}`

🎉 잘 하셨습니다. 이제 당신은 각 경로에 대한 인터페이스를 정의하고, 엄격하게 타입이 적용된 request 및 response 인스턴스를 가질 수 있습니다. Fastify 타입 시스템의 다른 부분도 제네릭 속성에 의존합니다. 사용 가능한 항목에 대해 더 알아보려면 아래의 자세한 타입 시스템 문서를 참고하세요.

### JSON 스키마

요청 및 응답을 검증하기 위해 JSON 스키마 파일을 사용할 수 있습니다. 아직 모른다면, Fastify 경로에 스키마를 정의하면 처리량이 증가할 수 있음을 알아두세요! 더 많은 정보는 [유효성 검사 및 직렬화](./Validation-and-Serialization.md)문서를 확인하세요.
또한 핸들러 내에서 정의된 타입을 사용하면 이점이 있습니다(사전 유효성 검사 등등).

다음은 이를 달성하는 몇 가지 옵션입니다.

#### Fastify 타입 공급자(Type Providers)

Fastify는 `json-schema-to-ts`와 `typebox`를 감싸는 두 패키지를 제공합니다.

- `@fastify/type-provider-json-schema-to-ts`
- `@fastify/type-provider-typebox`

이들은 스키마 유효성 검사 설정을 단순화합니다. [Type
Providers](./Type-Providers.md)페이지에 더 많은 내용이 있습니다.

다음은 바닐라 `typebox`, `json-schema-to-ts` 패키지를 사용해 스키마 유효성 검사를 설정하는 방법입니다.

#### typebox

[typebox](https://www.npmjs.com/package/@sinclair/typebox)와 [fastify-type-provider-typebox](https://github.com/fastify/fastify-type-provider-typebox)는 타입과 스키마를 한 번에 빌드하는 유용한 라이브러리입니다. typebox를 사용하면 코드 내에서 스키마를 정의하고, 필요에 따라 타입 또는 스키마로 바로 사용할 수 있습니다.

fastify 경로에서 특정 페이로드의 유효성 검사를 하려면 다음과 같이 할 수 있습니다.

1. 프로젝트에 `typebox`와 `fastify-type-provider-typebox`를 설치합니다.

   ```bash
   npm i @sinclair/typebox @fastify/type-provider-typebox
   ```

2. 필요한 스키마를 `Type`으로 정의하고, 해당 타입을 `Static`으로 생성합니다.

   ```typescript
   import { Static, Type } from "@sinclair/typebox";

   export const User = Type.Object({
     name: Type.String(),
     mail: Type.Optional(Type.String({ format: "email" })),
   });

   export type UserType = Static<typeof User>;
   ```

3. 정의된 타입과 스키마를 경로를 정의하는 데 사용합니다.

   ```typescript
   import Fastify from "fastify";
   import { TypeBoxTypeProvider } from "@fastify/type-provider-typebox";
   // ...

   const fastify = Fastify().withTypeProvider<TypeBoxTypeProvider>();

   app.post<{ Body: UserType; Reply: UserType }>(
     "/",
     {
       schema: {
         body: User,
         response: {
           200: User,
         },
       },
     },
     (request, reply) => {
       // `name`과 `mail`타입은 자동으로 추론됩니다.
       const { name, mail } = request.body;
       reply.status(200).send({ name, mail });
     }
   );
   ```

   **주의** Ajv 버전 7 또는 그 이상은 `ajvTypeBoxPlugin`를 사용해야 합니다.

   ```typescript
    import Fastify from 'fastify'
    import { ajvTypeBoxPlugin, TypeBoxTypeProvider } from '@fastify/type-provider-typebox'
    const fastify = Fastify({
      ajv: {
        plugins: [ajvTypeBoxPlugin]
      }
    }).withTypeProvider<TypeBoxTypeProvider>()
    ```

#### JSON 파일의 스키마

마지막 예에서는 인터페이스를 사용하여 요청의 쿼리 스트링과 헤더의 타입을 정의했습니다.
많은 사용자는 이미 JSON 스키마를 사용해 이러한 속성을 정의하고 있을 겁니다. 다행히도 기존 JSON 스키마를 TypeScript 인터페이스로 변환하는 방법이 있습니다!

1. '시작하기' 예제를 완료하지 않았다면, 돌아가 1-4 단계를 먼저 따르세요.
2. `json-schema-to-typescript` 모듈을 설치합니다:

   ```bash
   npm i -D json-schema-to-typescript
   ```

3. `schemas`라는 새로운 폴더를 만들고, `headers.json`과 `querystring.json`파일을 추가합니다. 다음 스키마 정의를 복사하여 해당 파일에 붙여넣습니다.

   ```json
   {
     "title": "Headers Schema",
     "type": "object",
     "properties": {
       "h-Custom": { "type": "string" }
     },
     "additionalProperties": false,
     "required": ["h-Custom"]
   }
   ```

   ```json
   {
     "title": "Querystring Schema",
     "type": "object",
     "properties": {
       "username": { "type": "string" },
       "password": { "type": "string" }
     },
     "additionalProperties": false,
     "required": ["username", "password"]
   }
   ```

4. `compile-schemas` 스크립트를 package.json에 추가합니다.

```json
{
  "scripts": {
    "compile-schemas": "json2ts -i schemas -o types"
  }
}
```

`json2ts`는 `json-schema-to-typescript`에 포함된 CLI 유틸리티입니다. `schemas`는 입력 경로, `types`는 출력 경로입니다.

5. `npm run compile-schemas`를 실행합니다. 두 새로운 파일은 `types` 디렉토리에 생성되어야 합니다.
6. `index.ts`에 다음의 코드를 업데이트합니다.

```typescript
import fastify from "fastify";

// json 스키마를 기존과 같이 import합니다.
import QuerystringSchema from "./schemas/querystring.json";
import HeadersSchema from "./schemas/headers.json";

// 생성된 인터페이스를 import합니다.
import { QuerystringSchema as QuerystringSchemaInterface } from "./types/querystring";
import { HeadersSchema as HeadersSchemaInterface } from "./types/headers";

const server = fastify();

server.get<{
  Querystring: QuerystringSchemaInterface;
  Headers: HeadersSchemaInterface;
}>(
  "/auth",
  {
    schema: {
      querystring: QuerystringSchema,
      headers: HeadersSchema,
    },
    preValidation: (request, reply, done) => {
      const { username, password } = request.query;
      done(username !== "admin" ? new Error("Must be admin") : undefined);
    },
    //  만약 async를 사용한다면
    //  preValidation: async (request, reply) => {
    //    const { username, password } = request.query
    //    if (username !== "admin") throw new Error("Must be admin");
    //  }
  },
  async (request, reply) => {
    const customerHeader = request.headers["h-Custom"];
    // request 데이터로 뭔가를 합니다.
    return `logged in!`;
  }
);

server.route<{
  Querystring: QuerystringSchemaInterface;
  Headers: HeadersSchemaInterface;
}>({
  method: "GET",
  url: "/auth2",
  schema: {
    querystring: QuerystringSchema,
    headers: HeadersSchema,
  },
  preHandler: (request, reply, done) => {
    const { username, password } = request.query;
    const customerHeader = request.headers["h-Custom"];
    done();
  },
  handler: (request, reply) => {
    const { username, password } = request.query;
    const customerHeader = request.headers["h-Custom"];
    reply.status(200).send({ username });
  },
});

server.listen({ port: 8080 }, (err, address) => {
  if (err) {
    console.error(err);
    process.exit(0);
  }
  console.log(`Server listening at ${address}`);
});
```

이 파일의 최상단에 있는 import들에 특별한 주의를 기울이세요. 중복되어 보일 수 있지만, 스키마와 생성된 인터페이스를 모두 임포트해야 합니다.

훌륭합니다! 이제 JSON 스키마와 TypeScript 정의를 모두 활용할 수 있습니다.

#### json-schema-to-ts

스키마에서 타입을 생성하지 않고 바로 코드에서 사용하고 싶다면, [json-schema-to-ts](https://www.npmjs.com/package/json-schema-to-ts) 패키지를 사용할 수 있습니다.

개발 의존성으로 설치합니다.

```bash
npm i -D json-schema-to-ts
```

스키마를 일반 객체처럼 만듭니다. 하지만, 모듈의 공식 문서에 설명된 대로 _const_ 로 선언하는 것에 주의하세요.

```typescript
const todo = {
  type: "object",
  properties: {
    name: { type: "string" },
    description: { type: "string" },
    done: { type: "boolean" },
  },
  required: ["name"],
} as const; // const를 사용하세요 !
```

제공된 타입 `FromSchema`를 사용하면, 스키마에서 타입을 빌드하고 핸들러에서 사용할 수 있습니다.

```typescript
import { FromSchema } from "json-schema-to-ts";
fastify.post<{ Body: FromSchema<typeof todo> }>(
  "/todo",
  {
    schema: {
      body: todo,
      response: {
        201: {
          type: "string",
        },
      },
    },
  },
  async (request, reply): Promise<void> => {
    /*
    request.body는 타입을 갖습니다.
    {
      [x: string]: unknown;
      description?: string;
      done?: boolean;
      name: string;
    }
    */

    request.body.name; // type error가 발생하지 않습니다.
    request.body.notthere; // type error가 발생합니다.

    reply.status(201).send();
  }
);
```

### 플러그인

Fastify의 가장 눈에 띄는 기능 중 하나는 광범위한 플러그인 생태계입니다. 플러그인은 타입이 완전히 지원되며 [선언 병합](https://www.typescriptlang.org/docs/handbook/declaration-merging.html) 패턴을 활용합니다. 이 예제는 타입스크립트 Fastify 플러그인 생성, Fastify 플러그인에 대한 타입 정의 생성, 타입스크립트 프로젝트에서 Fastify 플러그인 사용이라는 세 부분으로 나뉩니다.

#### 타입스크립트 Fastify 플러그인 만들기

1. 새 npm 프로젝트를 초기화하고 필요한 종속성을 설치합니다.
   ```bash
   npm init -y
   npm i fastify fastify-plugin
   npm i -D typescript @types/node
   ```
2. `package.json` 파일의 `"scripts"` 섹션에 `build` 스크립트를 추가하고, `"types"` 섹션에 `'index.d.ts'`를 추가합니다.
   ```json
   {
     "types": "index.d.ts",
     "scripts": {
       "build": "tsc -p tsconfig.json"
     }
   }
   ```
3. 타입스크립트 설정 파일을 만듭니다.
   ```bash
   npx typescript --init
   ```
   파일이 생성되면 `"compilerOptions"` 객체의 `"declaration"` 옵션을 활성화합니다.
   ```json
   {
     "compileOptions": {
       "declaration": true
     }
   }
   ```
4. `index.ts` 파일을 만듭니다. 여기에는 플러그인 코드가 포함됩니다.
5. `index.ts`에 아래 코드를 추가합니다.

   ```typescript
   import { FastifyPluginCallback, FastifyPluginAsync } from "fastify";
   import fp from "fastify-plugin";

   // 선언 병합을 활용하여 플러그인 속성을 적합한 fastify 인터페이스에 추가합니다.
   // 플러그인 타입이 여기에 정의된다면, decorate(request, reply)를 호출할 때 값이 타입 체킹됩니다.
   declare module "fastify" {
     interface FastifyRequest {
       myPluginProp: string;
     }
     interface FastifyReply {
       myPluginProp: number;
     }
   }

   // 옵션을 정의합니다
   export interface MyPluginOptions {
     myPluginOption: string;
   }

   // 콜백으로 플러그인을 정의합니다
   const myPluginCallback: FastifyPluginCallback<MyPluginOptions> = (
     fastify,
     options,
     done
   ) => {
     fastify.decorateRequest("myPluginProp", "super_secret_value");
     fastify.decorateReply("myPluginProp", options.myPluginOption);

     done();
   };

   // 프라미스로 플러그인을 정의합니다
   const myPluginAsync: FastifyPluginAsync<MyPluginOptions> = async (
     fastify,
     options
   ) => {
     fastify.decorateRequest("myPluginProp", "super_secret_value");
     fastify.decorateReply("myPluginProp", options.myPluginOption);
   };

   // fastify-plugin으로 플러그인을 export합니다
   export default fp(myPluginCallback, "3.x");
   // 또는
   // export default fp(myPluginAsync, '3.x')
   ```

6. 플러그인 코드를 컴파일하고 JavaScript 소스 파일과 타입 정의 파일을 모두 생성하려면 `npm run build`를 실행합니다.
7. 이제 플러그인이 완료되면 [npm에 게시]하거나 로컬에서 사용할 수 있습니다.
   > 플러그인을 사용하기 위해 npm에 게시할 _필요는_ 없습니다. 이를 Fastify 프로젝트에 포함하고 다른 코드와 마찬가지로 참조할 수 있습니다! TypeScript 사용자로서, TypeScript 인터프리터가 처리할 수 있도록 프로젝트 컴파일에 포함될 위치에 선언 오버라이딩이 있는지 확인하세요.

#### Fastify 플러그인에 대한 타입 정의 생성

이 플러그인 가이드는 JavaScript로 작성된 Fastify 플러그인을 위한 것입니다. 이 예제에 설명된 단계는 플러그인 사용자에게 TypeScript를 지원하기 위한 것입니다.

1. 새 npm 프로젝트를 초기화하고 필요한 종속성을 설치합니다.
   ```bash
   npm init -y
   npm i fastify-plugin
   ```
2. `index.js`와 `index.d.ts` 파일을 만듭니다.
3. `main`과 `types`속성에 다음 파일을 포함하도록 package json을 수정합니다.(파일명이 꼭 `index`일 필요는 없지만, 동일한 이름으로 하기를 권장합니다.)
   ```json
   {
     "main": "index.js",
     "types": "index.d.ts"
   }
   ```
4. `index.js`를 열고 다음 코드를 추가합니다.

   ```javascript
   //플러그인을 작성하기 위해 fastify-plugin을 강력하게 권장합니다.
   const fp = require("fastify-plugin");

   function myPlugin(instance, options, done) {
     //fastify 인스턴스를 myPluginFunc이라는 커스텀 함수로 꾸며줍니다.
     instance.decorate("myPluginFunc", (input) => {
       return input.toUpperCase();
     });

     done();
   }

   module.exports = fp(myPlugin, {
     fastify: "3.x",
     name: "my-plugin", // fastify-plugin이 속성의 이름을 얻기 위해 사용됩니다.
   });
   ```

5. `index.d.ts`를 열고 다음 코드를 추가합니다.

   ```typescript
   import { FastifyPlugin } from "fastify";

   interface PluginOptions {
     //...
   }

   // 다른 export를 선택적으로 추가할 수 있습니다.
   // 여기서는 우리가 추가한 데코레이터를 export합니다.
   export interface myPluginFunc {
     (input: string): string;
   }

   // Fastify 타입 시스템에 커스텀 속성을 추가하기 위해 선언 병합을 사용하는 것이 중요합니다.
   declare module "fastify" {
     interface FastifyInstance {
       myPluginFunc: myPluginFunc;
     }
   }

   // fastify 플러그인은 자동으로 명명된 export를 추가합니다. 따라서 이 타입도 추가해야 합니다.
   // 변수명은 `module.exports.myPlugin`에 없다면 `options.name` 속성에서 얻습니다.
   export const myPlugin: FastifyPlugin<PluginOptions>;

   // fastify 플러그인은 자동으로 `.default` 속성을 exported 플러그인에 추가합니다. 아래 주의 사항을 확인하세요.
   export default myPlugin;
   ```

**주의**: [fastify-plugin](https://github.com/fastify/fastify-plugin) v2.3.0 이상은 `.default` 속성과 명명된 export를 exported 플러그인에 자동으로 추가합니다. 최고의 개발 경험을 위해 `export default`와 `export const myPlugin`를 입력하는 것을 잊지 마세요. 완전한 예제는 [@fastify/swagger](https://github.com/fastify/fastify-swagger/blob/master/index.d.ts)에서 확인할 수 있습니다.

해당 파일들이 완료되면, 플러그인은 이제 모든 TypeScript 프로젝트에서 사용할 준비가 되었습니다!

Fastify 플러그인 시스템을 통해 개발자는 Fastify 인스턴스와 request/reply 인스턴스를 꾸밀 수 있습니다. 더 많은 정보는 [선언 병합과 제네릭 상속](https://dev.to/ethanarrowood/is-declaration-merging-and-generic-inheritance-at-the-same-time-impossible-53cp)에 관한 이 블로그 포스팅을 참고하세요.

#### 플러그인 사용하기

TypeScript에서 Fastify 플러그인을 사용하는 것은 JavaScript에서 플러그인을 사용하는 것만큼 쉽습니다. `import/from`으로 플러그인을 가져오면 모든 설정이 완료됩니다. 단, 한 가지 예외가 있습니다.

Fastify 플러그인은 선언 병합을 사용하여 기존 Fastify 타입 인터페이스를 수정합니다(자세한 내용은 앞의 두 예제를 확인하세요). 선언 병합은 그다지 _스마트하지_ 않습니다. 즉, 플러그인에 대한 타입 정의가 TypeScript 인터프리터의 범위 내에 있는 경우 플러그인 사용 여부에 **관계없이** 플러그인 타입이 포함됩니다. 이것은 TypeScript 사용의 불운한 제약이며 지금으로서는 피할 수 없습니다.

하지만, 이 경험을 개선하는 데 도움이 되는 몇 가지 제안 사항이 있습니다.

- `no-unused-vars`규칙이 [ESLint](https://eslint.org/docs/rules/no-unused-vars)에 활성화되어 있고, 가져온 모든 플러그인이 실제로 로드되고 있는지 확인하세요. 
- [depcheck](https://www.npmjs.com/package/depcheck) 나
  [npm-check](https://www.npmjs.com/package/npm-check) 모듈을 활용하여 플러그인 종속성이 프로젝트에서 사용되고 있는지 검증하세요.

`require`를 사용하면 타입 정의를 적절히 로드하지 못하고 타입 에러가 발생할 수 있음에 주의하세요. TypeScript는 코드로 직접 가져온 타입만 식별할 수 있으므로, 맨 위에 import와 함께 require를 인라인으로 사용할 수 있습니다. 예를 들어:

```typescript
import "plugin"; //여기서 타입 확장이 트리거됩니다.

fastify.register(require("plugin"));
```

```typescript
import plugin from "plugin"; // 여기서 타입 확장이 트리거됩니다.

fastify.register(plugin);
```

또는 tsconfig에 명시적으로 설정할 수 있습니다.

```jsonc
{
  "types": ["plugin"] // 타입스크립트가 타입을 가져오도록 강제합니다.
}
```

## 바닐라 자바스크립트의 코드 완성 

바닐라 자바스크립트는 [TypeScript JSDoc
Reference](https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html)에 따라 게시된 타입을 사용해 코드 완성을 제공할 수 있습니다.
(예: [Intellisense](https://code.visualstudio.com/docs/editor/intellisense))

예를 들어:

```js
/**  @type {import('fastify').FastifyPluginAsync<{ optionA: boolean, optionB: string }>} */
module.exports = async function (fastify, { optionA, optionB }) {
  fastify.get("/look", () => "at me");
};
```

## API 타입 시스템 문서

이 섹션은 Fastify 버전 3.x에서 사용할 수 있는 모든 타입에 대한 자세한 설명입니다.

모든 `http`, `https`, 그리고 `http2` 타입은 `@types/node`에 의해 추론됩니다.

[제네릭](#generics)은 기본값과 제약 조건 값으로 문서화됩니다. 타입스크립트 제네릭에 대한 더 많은 정보는 이 기사를 읽어보세요.

- [제네릭 파라미터 기본값](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-3.html#generic-parameter-defaults)
- [제네릭 제약 조건](https://www.typescriptlang.org/docs/handbook/generics.html#generic-constraints)

#### import 방법

Fastify API는 `fastify()`메서드로 구동됩니다. 자바스크립트에서는 `const fastify = require('fastify')`를 사용하여 가져옵니다. 타입스크립트에서는 타입을 확인할 수 있도록 `import/from` 문법을 사용하기를 권장합니다. Fastify 타입 시스템에는 지원되는 가져오는 방법이 몇 가지 있습니다. 

1. `import fastify from 'fastify'`

   - 타입은 확인되지만 dot 표기법을 사용하여 접근할 수 없습니다.
   - 예시:

     ```typescript
     import fastify from "fastify";

     const f = fastify();
     f.listen({ port: 8080 }, () => {
       console.log("running");
     });
     ```

   - 구조 분해 할당을 통해 타입에 접근합니다.

     ```typescript
     import fastify, { FastifyInstance } from "fastify";

     const f: FastifyInstance = fastify();
     f.listen({ port: 8080 }, () => {
       console.log("running");
     });
     ```

   - 구조 분해 할당은 메인 API 메서드에서도 작동합니다.

     ```typescript
     import { fastify, FastifyInstance } from "fastify";

     const f: FastifyInstance = fastify();
     f.listen({ port: 8080 }, () => {
       console.log("running");
     });
     ```

2. `import * as Fastify from 'fastify'`

   - dot 표기법을 사용하여 타입을 확인하고 접근할 수 있습니다.
   - 메인 Fastify API 메서드를 호출하려면 약간 다른 구문이 필요합니다(예제 참조).
   - 예시:

     ```typescript
     import * as Fastify from "fastify";

     const f: Fastify.FastifyInstance = Fastify.fastify();
     f.listen({ port: 8080 }, () => {
       console.log("running");
     });
     ```

3. `const fastify = require('fastify')`

   - 이 구문은 유효하며 예상대로 fastify를 가져올 것입니다. 하지만, 타입은 확인되지 **않습니다**.
   - 예시:

     ```typescript
     const fastify = require("fastify");

     const f = fastify();
     f.listen({ port: 8080 }, () => {
       console.log("running");
     });
     ```

   - 구조 분해 할당이 지원되며 타입을 적절하게 확인합니다.

     ```typescript
     const { fastify } = require("fastify");

     const f = fastify();
     f.listen({ port: 8080 }, () => {
       console.log("running");
     });
     ```

#### 제네릭

많은 타입 정의가 동일한 제네릭 파라미터를 공유합니다. 그것들은 모두 이 섹션에 자세히 문서화되어 있습니다.

대부분의 정의는 `@node/types`의 `http`, `https`, 그리고 `http2`에 의존합니다.

##### RawServer

기본 Node.js 서버 타입

기본값: `http.Server`

제약 조건: `http.Server`, `https.Server`, `http2.Http2Server`,
`http2.Http2SecureServer`

제네릭 파라미터 적용: [`RawRequest`][rawrequestgeneric],
[`RawReply`][rawreplygeneric]

##### RawRequest

기본 Node.js 요청 타입

기본값: [`RawRequestDefaultExpression`][rawrequestdefaultexpression]

제약 조건: `http.IncomingMessage`, `http2.Http2ServerRequest`

적용 주체: [`RawServer`][rawservergeneric]

##### RawReply

기본 Node.js 응답 타입

기본값: [`RawReplyDefaultExpression`][rawreplydefaultexpression]

제약 조건: `http.ServerResponse`, `http2.Http2ServerResponse`

적용 주체: [`RawServer`][rawservergeneric]

##### 로거

Fastify 로깅 유틸리티

기본값: [`FastifyLoggerOptions`][fastifyloggeroptions]

적용 주체: [`RawServer`][rawservergeneric]

##### RawBody

content-type-parser 메서드에 대한 제네릭 파라미터

제약 조건: `string | Buffer`

---

#### Fastify

##### fastify<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [Logger][loggergeneric]>(opts?: [FastifyServerOptions][fastifyserveroptions]): [FastifyInstance][fastifyinstance]

[src](https://github.com/fastify/fastify/blob/main/fastify.d.ts#L19)

기본 Fastify API 메서드입니다. 기본적으로 HTTP 서버를 생성합니다. 타입 시스템이 판별(discriminant) 유니온과 오버로딩 메서드를 활용해, 순전히 메서드의 옵션을 기반으로, 어떤 서버 타입(http, https, 또는 http2)이 생성되는지 자동으로 추론합니다.(더 많은 정보는 아래 예제를 확인하세요). 또한 사용자가 Node.js 서버, 요청, 응답 객체를 확장할 수 있도록 광범위한 제네릭 타입 시스템을 지원합니다. 추가로, `Logger` 제네릭은 커스텀 로그 타입을 위해 존재합니다. 자세한 내용은 아래의 예와 제네릭 명세를 참조하세요.

###### 예 1: 표준 HTTP 서버

타입 시스템이 HTTP로 기본 설정되므로 `Server` 제네릭을 지정할 필요가 없습니다.

```typescript
import fastify from "fastify";

const server = fastify();
```

자세한 HTTP 서버 연습은 예제로 배우기 - [Getting Started](#getting-started)를 확인하세요.

###### 예 2: HTTPS 서버

1. `@types/node`와 `fastify`에서 다음 import를 생성합니다.
   ```typescript
   import fs from "fs";
   import path from "path";
   import fastify from "fastify";
   ```
2. [Node.js https 서버 가이드](https://nodejs.org/en/knowledge/HTTP/servers/how-to-create-a-HTTPS-server/) 공식 문서의 단계를 따라 `key.pem`과 `cert.pem` 파일을 생성합니다.
3. 서버를 인스턴스화하고 경로를 추가합니다.

   ```typescript
   const server = fastify({
     https: {
       key: fs.readFileSync(path.join(__dirname, "key.pem")),
       cert: fs.readFileSync(path.join(__dirname, "cert.pem")),
     },
   });

   server.get("/", async function (request, reply) {
     return { hello: "world" };
   });

   server.listen({ port: 8080 }, (err, address) => {
     if (err) {
       console.error(err);
       process.exit(0);
     }
     console.log(`Server listening at ${address}`);
   });
   ```

4. 빌드하고 실행하세요! `curl -k https://localhost:8080`로 쿼리하여 서버를 테스트합니다.

###### 예 3: HTTP2 서버

HTTP2 서버에는 안전한 유형과 안전하지 않은 유형 두 가지가 있습니다. 둘 다 `options`객체의 `http2` 속성을 `true`로 설정해주어야 합니다. `https` 속성은 안전한 http2 서버를 만드는 데 사용합니다. `https` 속성을 생략하면 안전하지 않은 http2 서버를 생성하게 됩니다.

```typescript
const insecureServer = fastify({ http2: true });
const secureServer = fastify({
  http2: true,
  https: {}, // https 섹션의 `key.pem`과 `cert.pem` 파일을 사용합니다.
});
```

HTTP2를 사용에 대한 더 자세한 내용은 Fastify [HTTP2](./HTTP2.md) 문서를 확인하세요.

###### 예 4: 확장 HTTP 서버

서버 타입뿐만 아니라 요청과 응답 타입도 지정할 수 있습니다. 따라서 특수한 속성, 메서드 등을 지정할 수 있습니다! 서버 인스턴스화 시 지정하면, 모든 추가적인 커스텀 타입 인스턴스에서 사용할 수 있게 됩니다.

```typescript
import fastify from "fastify";
import http from "http";

interface customRequest extends http.IncomingMessage {
  mySpecialProp: string;
}

const server = fastify<http.Server, customRequest>();

server.get("/", async (request, reply) => {
  const someValue = request.raw.mySpecialProp; // `customeRequest` 인터페이스 덕분에 TS는 이것이 문자열임을 압니다. 
  return someValue.toUpperCase();
});
```

###### 예 5: 로거 타입 지정

Fastify는 내부적으로 [Pino](https://getpino.io/#/)라는 로깅 라이브러리를 사용합니다. `pino@7`부터 모든 속성은 Fastify 인스턴스를 구성할 때 `logger` 필드를 통해 설정할 수 있습니다. 필요한 속성이 노출되지 않은 경우, [`Pino`](https://github.com/pinojs/pino/issues)에 이슈를 등록하거나 사전에 구성된 외부 인스턴스(또는 다른 호환 가능한 로거)를 Pino에 전달하세요. 이는 동일한 필드로 Fasitfy에 임시 수정을 하는 방법입니다. 이렇게 커스텀 직렬화 도구를 생성할 수도 있습니다. 더 자세한 내용은 [Logging](Logging.md) 문서를 참고하세요.

```typescript
import fastify from "fastify";

const server = fastify({
  logger: {
    level: "info",
    redact: ["x-userinfo"],
    messageKey: "message",
  },
});

server.get("/", async (request, reply) => {
  server.log.info("log message");
  return "another message";
});
```

---

##### fastify.HTTP 메서드

[src](https://github.com/fastify/fastify/blob/main/types/utils.d.ts#L8)

유니온 타입: `'DELETE' | 'GET' | 'HEAD' | 'PATCH' | 'POST' | 'PUT' | 'OPTIONS'`

##### fastify.RawServerBase

[src](https://github.com/fastify/fastify/blob/main/types/utils.d.ts#L13)

`@types/node`모듈의 `http`, `https`, `http2`에 의존합니다.

유니온 타입: `http.Server | https.Server | http2.Http2Server | http2.Http2SecureServer`

##### fastify.RawServerDefault

[src](https://github.com/fastify/fastify/blob/main/types/utils.d.ts#L18)

`@types/node` 모듈의 `http`에 의존합니다.

`http.Server`의  타입 별칭 

---

##### fastify.FastifyServerOptions<[RawServer][rawservergeneric], [Logger][loggergeneric]>

[src](https://github.com/fastify/fastify/blob/main/fastify.d.ts#L29)

Fastify 서버의 인스턴스화에 사용되는 속성의 인터페이스입니다. 메인 [`fastify()`][fastify] 메서드에서 사용합니다. `RawServer`와 `Logger` 제네릭 파라미터는 이 메서드를 통해 전달됩니다.  

타입스크립트를 사용하여 Fastify 서버를 인스턴스화하는 예제를 확인하려면 메인 [fastify][fastify] 메서드 타입 정의 섹션을 확인하세요.

##### fastify.FastifyInstance<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RequestGeneric][fastifyrequestgenericinterface], [Logger][loggergeneric]>

[src](https://github.com/fastify/fastify/blob/main/types/instance.d.ts#L16)

Fastify 서버 객체를 나타내는 인터페이스입니다. 이는 [`fastify()`][fastify] 메서드에서 반환된 서버 인스턴스입니다. 인터페이스 타입이므로 코드에서 `decorate` 메서드를 사용하면 [선언 병합](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)을 통해 확장할 수 있습니다.

제네릭 캐스캐이딩을 통해 인스턴스에 연결된 모든 메서드는 인스턴스화된 제네릭 속성을 상속합니다. 이는 서버, 요청 또는 응답 타입을 지정함으로써 모든 메서드가 해당 객체를 입력하는 방법을 알게 된다는 것을 의미합니다.

더 자세한 가이드는 메인 [Learn by Example](#learn-by-example) 섹션을, 이 인터페이스에 대한 추가적인 세부 정보는 보다 단순한 [fastify][fastify] 메서드 예제를 확인하세요.

---

#### 요청

##### fastify.FastifyRequest<[RequestGeneric][fastifyrequestgenericinterface], [RawServer][rawservergeneric], [RawRequest][rawrequestgeneric]>

[src](https://github.com/fastify/fastify/blob/main/types/request.d.ts#L15)

이 인터페이스에는 Fastify 요청 객체의 속성이 포함되어 있습니다. 여기에 추가된 속성은 제공된 요청 객체의 종류(http vs http2)나 라우트 레벨을 무시합니다. 따라서 `request.body`를 GET 요청에서 호출해도 에러가 발생하지 않습니다. (하지만 GET 요청에 바디를 포함한다면, 행운을 빌겠습니다😉)

`FastifyRequest`객체에 커스텀 속성을 추가해야 하는 경우(예를 들면 [`decorateRequest`][decoraterequest] 메서드를 사용할 때) 이 인터페이스에서 선언 병합을 사용해야 합니다.

기본 예시는 [`FastifyRequest`][fastifyrequest] 섹션에 있습니다. 더 자세한 예시는 예제로 배우기 섹션의 [Plugins](#plugins)를 확인하세요.

###### 예시

```typescript
import fastify from "fastify";

const server = fastify();

server.decorateRequest("someProp", "hello!");

server.get("/", async (request, reply) => {
  const { someProp } = request; // 이 속성을 요청 인터페이스에 추가하려면 선언 병합을 사용해야 합니다.
  return someProp;
});

// 이 선언은 타입스크립트 인터프리터의 스코프 내에 있어야 작동합니다. 
declare module "fastify" {
  interface FastifyRequest {
    someProp: string;
  }
}

// 또는 요청을 다음과 같이 타입으로 작성할 수 있습니다.
type CustomRequest = FastifyRequest<{
  Body: { test: boolean };
}>;

server.get(
  "/typedRequest",
  async (request: CustomRequest, reply: FastifyReply) => {
    return request.body.test;
  }
);
```

##### fastify.RequestGenericInterface

[src](https://github.com/fastify/fastify/blob/main/types/request.d.ts#L4)

Fastify 요청 객체에는 `body`, `params`, `query`, `headers` 네 가지 동적인 속성이 있습니다. 각각의 타입은 이 인터페이스를 통해 할당할 수 있습니다. 이는 개발자가 지정하고 싶지 않은 속성을 무시할 수 있도록 명명된 속성 인터페이스입니다. 생략된 모든 속성의 기본값은 `unknown`입니다. 해당 속성 이름은 `Body`, `Querystring`, `Params`, `Headers`입니다.

```typescript
import fastify, { RequestGenericInterface } from "fastify";

const server = fastify();

interface requestGeneric extends RequestGenericInterface {
  Querystring: {
    name: string;
  };
}

server.get<requestGeneric>("/", async (request, reply) => {
  const { name } = request.query; // 이제 query 속성에 name 속성이 존재합니다.
  return name.toUpperCase();
});
```

이 인터페이스를 사용하는 자세한 예제를 보면 예제로 배우기 섹션의 [JSON 스키마](#jsonschema)를 확인하세요.

##### fastify.RawRequestDefaultExpression\<[RawServer][rawservergeneric]\>

[src](https://github.com/fastify/fastify/blob/main/types/utils.d.ts#L23)

`@types/node` 모듈의 `http`, `https`, `http2`에 의존합니다.

제네릭 파라미터 `RawServer`의 기본값은 [`RawServerDefault`][rawserverdefault]입니다.

`RawServer`의 타입이 `http.Server` 또는 `https.Server`라면, 이 표현은 `http.IncomingMessage`를 리턴합니다. 다른 경우에는 `http2.Http2ServerRequest`를 리턴합니다.

```typescript
import http from 'http'
import http2 from 'http2'
import { RawRequestDefaultExpression } from 'fastify'

RawRequestDefaultExpression<http.Server> // -> http.IncomingMessage
RawRequestDefaultExpression<http2.Http2Server> // -> http2.Http2ServerRequest
```

---

#### 응답

##### fastify.FastifyReply<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>

[src](https://github.com/fastify/fastify/blob/main/types/reply.d.ts#L32)

이 인터페이스는 Fastify가 표준 Node.js 응답 객체에 추가하는 커스텀 속성을 포함합니다. 여기에 추가된 속성은 응답 객체의 종류(http 대 http2)를 무시합니다.

FastifyReply 객체에 커스텀 속성을 추가해야 하는 경우(예: `decorateReply` 메서드를 사용할 때) 이 인터페이스에서 선언 병합을 사용해야 합니다.

[`FastifyReply`][fastifyreply] 섹션에 기본 예제가 제공됩니다. 더 자세한 예제는 예제로 배우기 섹션의 [플러그인](#plugins)을 확인하세요.

###### 예시

```typescript
import fastify from "fastify";

const server = fastify();

server.decorateReply("someProp", "world");

server.get("/", async (request, reply) => {
  const { someProp } = reply; // 이 속성을 응답 인터페이스에 추가하려면 선언 병합을 사용해야 합니다.
  return someProp;
});

// 이 선언은 타입스크립트 인터프리터 작동 범위에 있어야 합니다.
declare module "fastify" {
  interface FastifyReply {
    // 반드시 타입이 아닌 인터페이스를 참조해야 합니다.
    someProp: string;
  }
}
```

##### fastify.RawReplyDefaultExpression<[RawServer][rawservergeneric]>

[src](https://github.com/fastify/fastify/blob/main/types/utils.d.ts#L27)

`@types/node` 모듈의 `http`, `https`, `http2`에 의존합니다.

제네릭 파라미터 `RawServer`의 기본값은 [`RawServerDefault`][rawserverdefault]입니다.

`RawServer`의 타입이 `http.Server` 또는 `https.Server`라면, 이 표현은 `http.ServerResponse`를 리턴합니다. 다른 경우에는 `http2.Http2ServerResponse`를 리턴합니다.

```typescript
import http from 'http'
import http2 from 'http2'
import { RawReplyDefaultExpression } from 'fastify'

RawReplyDefaultExpression<http.Server> // -> http.ServerResponse
RawReplyDefaultExpression<http2.Http2Server> // -> http2.Http2ServerResponse
```

---

#### 플러그인

Fastify를 사용하면 플러그인으로 기능을 확장할 수 있습니다. 플러그인은 경로의 집합, 서버 데코레이터 또는 무엇이든 될 수 있습니다. 플러그인을 활성화하려면 [`fastify.register()`][fastifyregister] 메서드를 사용하세요.

Fastify용 플러그인을 생성할 때 `fastify-plugin` 모듈을 사용하는 것이 좋습니다. 또한, 예제로 배우기의 [플러그인](#plugins) 섹션에 TypeScript와 Fastify를 사용하여 플러그인을 만드는 방법에 대한 가이드가 있습니다.

##### fastify.FastifyPluginCallback<[Options][fastifypluginoptions]>

[src](https://github.com/fastify/fastify/blob/main/types/plugin.d.ts#L9)

[`fastify.register()`][fastifyregister] 메서드 내에서 사용되는 인터페이스 메서드 정의입니다.

##### fastify.FastifyPluginAsync<[Options][fastifypluginoptions]>

[src](https://github.com/fastify/fastify/blob/main/types/plugin.d.ts#L20)

[`fastify.register()`][fastifyregister] 메서드 내에서 사용되는 인터페이스 메서드 정의입니다.

##### fastify.FastifyPlugin<[Options][fastifypluginoptions]>

[src](https://github.com/fastify/fastify/blob/main/types/plugin.d.ts#L29)

[`fastify.register()`][fastifyregister] 메서드 내에서 사용되는 인터페이스 메서드 정의입니다. 이 문서는 더 이상 사용되지 않으므로 `FastifyPluginCallback`과 `FastifyPluginAsync`을 사용하도록 합니다. 일반적인 `FastifyPlugin`이 비동기 함수의 타입을 적절히 추론하지 못하기 때문입니다.

##### fastify.FastifyPluginOptions

[src](https://github.com/fastify/fastify/blob/main/types/plugin.d.ts#L31)

[`fastify.register()`][fastifyregister]의 `options` 매개변수를 객체로 제한하는 데 사용되는 느슨하게 타입된 객체입니다. 플러그인을 생성할 때 옵션을 이 인터페이스의 확장으로 정의하여 등록 메서드에 전달할 수 있도록 합니다.

---

#### 등록하다

##### fastify.FastifyRegister(플러그인: [FastifyPluginCallback][fastifyplugincallback], 옵션: [FastifyRegisterOptions][fastifyregisteroptions])

[src](https://github.com/fastify/fastify/blob/main/types/register.d.ts#L9)

##### fastify.FastifyRegister(플러그인: [FastifyPluginAsync][fastifypluginasync], 옵션: [FastifyRegisterOptions][fastifyregisteroptions])

[src](https://github.com/fastify/fastify/blob/main/types/register.d.ts#L9)

##### fastify.FastifyRegister(플러그인: [FastifyPlugin][fastifyplugin], 옵션: [FastifyRegisterOptions][fastifyregisteroptions])

[src](https://github.com/fastify/fastify/blob/main/types/register.d.ts#L9)

이 타입 인터페이스는 [`fastify.register()`](./Server.md#register) 메서드의 타입을 지정합니다. 타입 인터페이스는 기본 제네릭 `Options`가 있는 함수 시그니처를 반환하며 기본값은 [FastifyPluginOptions][fastifypluginoptions]입니다. 그 함수를 호출할 때 FastifyPlugin 매개변수에서 이 제네릭을 유추하므로 기본 제네릭을 지정할 필요가 없습니다. options 매개변수는 플러그인의 옵션과 두 개의 추가 선택적 속성인 `prefix: string`과 `logLevel`: [LogLevel][loglevel]의 교차점입니다.

다음은 작동 중인 옵션 추론의 예입니다.

```typescript
const server = fastify()

const plugin: FastifyPlugin<{
  option1: string;
  option2: boolean;
}> = function (instance, opts, done) { }

server().register(plugin, {}) // 에러 - options 객체에 필수 속성이 없습니다.
server().register(plugin, { option1: '', option2: true }) // OK - options 객체가 필수 속성을 포함합니다.
```

Fastify에서 TypeScript 플러그인을 생성하는 더 자세한 예제는 예제로 배우기, [Plugins](#plugins) 섹션을 확인하세요.

##### fastify.FastifyRegisterOptions

[src](https://github.com/fastify/fastify/blob/main/types/register.d.ts#L16)

이 타입은 `Options` 제네릭과 내보내기 되지 않은 `RegisterOptions` 인터페이스의 교차점입니다. `RegisterOptions`는 두 가지 선택적 속성 `prefix: string`과 `logLevel`: [LogLevel][loglevel]을 지정하는 인터페이스입니다. 이 타입은 앞서 설명한 교차점을 반환하는 함수로 지정될 수도 있습니다.

---

#### Logger

커스텀 로거에 지정에 대한 자세한 예제는 [로거 타입 지정하기](#example-5-specifying-logger-types)를 확인하세요.

##### fastify.FastifyLoggerOptions<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric]>

[src](https://github.com/fastify/fastify/blob/main/types/logger.d.ts#L17)

내부 Fastify 로거에 대한 인터페이스 정의입니다. 이는 [Pino.js](https://getpino.io/#/) 로거를 모방합니다. 서버 옵션을 통해 활성화 될 때는, 일반 [로거](./Logging.md) 문서에 따라 사용하세요.

##### fastify.FastifyLogFn

[src](https://github.com/fastify/fastify/blob/main/types/logger.d.ts#L7)

Fastify가 로그 메서드를 호출하는 두 가지 방법을 구현하는 오버로드 함수 인터페이스입니다. 이 인터페이스는 FastifyLoggerOptions 객체의 모든 연관된 로그 레벨 속성에 전달됩니다.

##### fastify.LogLevel

[src](https://github.com/fastify/fastify/blob/main/types/logger.d.ts#L12)

`'info' | 'error' | 'debug' | 'fatal' | 'warn' | 'trace'`의 유니온 타입입니다.

---

#### Context

컨텍스트 타입 정의는 타입 시스템의 다른 매우 동적인 부분들과 유사합니다. 라우트 컨텍스트는 라우트 핸들러 메서드에서 사용할 수 있습니다.

##### fastify.FastifyContext

[src](https://github.com/fastify/fastify/blob/main/types/context.d.ts#L6)

기본적으로 `unknown`으로 설정되는 `config`라는 유일한 필수 속성을 가진 인터페이스입니다. 제네릭 또는 오버로드를 사용하여 지정할 수 있습니다.

이 타입 정의는 잠재적으로 불완전합니다. 여러분이 이것을 사용 중이고, 정의를 개선하는 방법에 대한 자세한 내용을 제공할 수 있다면, [fastify/fastify](https://github.com/fastify/fastify)의 메인 저장소에 이슈를 열기를 강력히 권장합니다. 미리 감사드립니다!

---

#### 라우팅

라우팅은 Fastify의 핵심 원칙 중 하나입니다. 이 섹션에 정의된 대부분의 타입은 Fasitfy 인스턴스 `.route`와 `.get/.post/.etc` 메서드에서 내부적으로 사용됩니다.

##### fastify.RouteHandlerMethod<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>

[src](https://github.com/fastify/fastify/blob/main/types/route.d.ts#L105)

라우트 핸들러 메서드에 대한 타입 선언입니다. 두 개의 인수 `request`와 `reply`를 가지는데, 각각 `FastifyRequest`와 `FastifyReply`에 의해 타입이 결정됩니다. 제네릭 파라미터는 이 인수에 전달됩니다. 이 메서드는 동기와 비동기 핸들러에 각각 `void` 또는 `Promise<any>`를 반환합니다.

##### fastify.RouteOptions<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>

[src](https://github.com/fastify/fastify/blob/main/types/route.d.ts#L78)

RouteShorthandOptions를 확장하고 다음 세 가지 필수 속성을 추가하는 인터페이스입니다.

1. `method`: 단일 [HTTP메서드][httpmethods] 또는 [HTTPMethods][httpmethods] 목록
2. `url` 경로에 대한 문자열
3. `handler` 경로 핸들러 메서드, 자세한 내용은 [RouteHandlerMethod][fastify.RouteHandlerMethod]를 참조

##### fastify.RouteShorthandMethod<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric]>

[src](https://github.com/fastify/fastify/blob/main/types/route.d.ts#12)

`.get/.post/.etc` 메서드와 함께 사용할 세 종류의 약식 경로 메서드를 위한 오버로드된 함수 인터페이스입니다.

##### fastify.RouteShorthandOptions<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>

[src](https://github.com/fastify/fastify/blob/main/types/route.d.ts#55)

경로에 대한 모든 기본 옵션을 다루는 인터페이스입니다. 이 인터페이스의 각 속성은 선택 사항이며 RouteOptions와 RouteShorthandOptionsWithHandler 인터페이스의 기본 역할을 합니다.

##### fastify.RouteShorthandOptionsWithHandler<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>

[src](https://github.com/fastify/fastify/blob/main/types/route.d.ts#93)

이 인터페이스는 RouteShorthandOptions 인터페이스에 유일한 필수 속성이며 RouteHandlerMethod 타입인 `handler`를 추가합니다.

---

#### 파서

##### RawBody

`string` 또는 `Buffer`인 제네릭 타입입니다.

##### fastify.FastifyBodyParser<[RawBody][rawbodygeneric], [RawServer][rawservergeneric], [RawRequest][rawrequestgeneric]>

[src](https://github.com/fastify/fastify/blob/main/types/content-type-parser.d.ts#L7)

바디 파서 메서드를 지정하기 위한 함수 타입의 정의입니다. `RawBody` 제네릭을 사용하여 파싱되는 바디의 타입을 지정합니다.

##### fastify.FastifyContentTypeParser<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric]>
 
[src](https://github.com/fastify/fastify/blob/main/types/content-type-parser.d.ts#L17)

바디 파서 메서드를 지정하기 위한 함수 타입의 정의입니다. `RawRequest` 제네릭을 통해 Content의 타입을 지정합니다.

##### fastify.AddContentTypeParser<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric]>

[src](https://github.com/fastify/fastify/blob/main/types/content-type-parser.d.ts#L46)

`addContentTypeParser` 메서드에 대한 오버로드된 인터페이스 함수 정의입니다. `parseAs`가 `opts` 파라미터에 전달되면, `parser` 파라미터에 대해 [FastifyBodyParser][#####fastify.FastifyBodyParser]를 사용합니다. 그렇지 않으면, [FastifyContentTypeParser][##### fastify.FastifyContentTypeParser]를 사용합니다.

##### fastify.hasContentTypeParser

[src](https://github.com/fastify/fastify/blob/main/types/content-type-parser.d.ts#L63)

특정 컨텐츠 타입의 타입 파서의 존재 여부를 확인하는 메서드입니다.

---

#### 에러

##### fastify.FastifyError

[src](https://github.com/fastify/fastify/blob/main/fastify.d.ts#L179)

FastifyError는 상태 코드 및 유효성 검사 결과를 포함하는 커스텀 에러 객체입니다.

Node.js의 `Error` 타입을 확장하며, 두 개의 선택적인 속성인 `statusCode: number`과 `validation: ValidationResult[]`를 추가합니다.

##### fastify.ValidationResult

[src](https://github.com/fastify/fastify/blob/main/fastify.d.ts#L184)

라우트 유효성 검사는 내부적으로 고성능 JSON 스키마 유효성 검사기인 Ajv에 의존합니다.
이 인터페이스는 FastifyError의 인스턴스로 전달됩니다.

---

#### 훅

##### fastify.onRequestHookHandler<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>(request: [FastifyRequest][fastifyrequest], reply: [FastifyReply][fastifyreply], done: (err?: [FastifyError][fastifyerror]) => void): Promise\<unknown\> | void

[src](https://github.com/fastify/fastify/blob/main/types/hooks.d.ts#L17)

`onRequest`는 요청 라이프사이클에서 실행되는 첫 번째 훅입니다. 이전에는 훅이 없었고, 다음 훅은 `preParsing`입니다.

주의: `onRequest` 훅에서, `request.body`는 항상 null입니다. 바디 파싱이 `preHandler` 훅 보다 먼저 일어나기 때문입니다.

##### fastify.preParsingHookHandler<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>(request: [FastifyRequest][fastifyrequest], reply: [FastifyReply][fastifyreply], done: (err?: [FastifyError][fastifyerror]) => void): Promise\<unknown\> | void

[src](https://github.com/fastify/fastify/blob/main/types/hooks.d.ts#L35)

`preParsing`은 요청 라이프사이클에서 실행되는 두 번째 훅입니다. 이전 훅은 `onRequest`이고, 다음 훅은 `preValidation`입니다. 

주의: `preParsing` 훅에서, `request.body`는 항상 null입니다. 바디 파싱이 `preValidation` 훅 보다 먼저 일어나기 때문입니다.

주의: 반환된 스트림에도 `receivedEncodedLength` 속성을 추가해야 합니다. 이 속성은 요청 페이로드를 `Content-Length` 헤더 값과 정확히 매칭하는 데 사용됩니다. 이상적으로 이 속성은 수신된 각 청크마다 업데이트되어야 합니다.

##### fastify.preValidationHookHandler<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>(request: [FastifyRequest][fastifyrequest], reply: [FastifyReply][fastifyreply], done: (err?: [FastifyError][fastifyerror]) => void): Promise\<unknown\> | void

[src](https://github.com/fastify/fastify/blob/main/types/hooks.d.ts#L53)

`preValidation`은 요청 라이프사이클에서 실행되는 세 번째 훅입니다. 이전 훅은 `preParsing`이고, 다음 훅은 `preHandler`입니다.

##### fastify.preHandlerHookHandler<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>(request: [FastifyRequest][fastifyrequest], reply: [FastifyReply][fastifyreply], done: (err?: [FastifyError][fastifyerror]) => void): Promise\<unknown\> | void

[src](https://github.com/fastify/fastify/blob/main/types/hooks.d.ts#L70)

`preHandler`는 요청 라이프사이클에서 실행되는 네 번째 훅입니다. 이전 훅은 `preValidation`이고, 다음 훅은 `preSerialization`입니다.

##### fastify.preSerializationHookHandler<PreSerializationPayload, [RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>(request: [FastifyRequest][fastifyrequest], reply: [FastifyReply][fastifyreply], payload: PreSerializationPayload, done: (err: [FastifyError][fastifyerror] | null, res?: unknown) => void): Promise\<unknown\> | void

[src](https://github.com/fastify/fastify/blob/main/types/hooks.d.ts#L94)

`preSerialization`은 요청 라이프사이클에서 실행되는 다섯 번째 훅입니다. 이전 훅은 `preHandler`이고, 다음 훅은 `onSend`입니다.

주의: 페이로드가 문자열, 버퍼, 스트림 또는 null이면 훅이 호출되지 않습니다.

##### fastify.onSendHookHandler<OnSendPayload, [RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>(request: [FastifyRequest][fastifyrequest], reply: [FastifyReply][fastifyreply], payload: OnSendPayload, done: (err: [FastifyError][fastifyerror] | null, res?: unknown) => void): Promise\<unknown\> | void

[src](https://github.com/fastify/fastify/blob/main/types/hooks.d.ts#L114)

`onSend` 훅으로 페이로드를 변경할 수 있습니다. 이 훅은 요청 라이프사이클에서 실행되는 여섯 번째 훅입니다. 이전 훅은 `preSerialization`이고, 다음 훅은 `onResponse`입니다.

주의: 페이로드를 변경하려면 문자열, 버퍼, 스트림 또는 null로만 변경할 수 있습니다.

##### fastify.onResponseHookHandler<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>(request: [FastifyRequest][fastifyrequest], reply: [FastifyReply][fastifyreply], done: (err?: [FastifyError][fastifyerror]) => void): Promise\<unknown\> | void

[src](https://github.com/fastify/fastify/blob/main/types/hooks.d.ts#L134)

`onResponse`는 요청 훅 라이프사이클의 일곱 번째이자 마지막 훅입니다. 이전 훅은 `onSend`이고, 다음 훅은 없습니다. 

onResponse 훅은 응답이 전송되었을 때 실행되므로 클라이언트에 더 많은 데이터를 보낼 수는 없습니다. 그러나 예를 들어, 통계를 수집하기 위해서 데이터를 외부 서비스로 보내는 데 유용할 수 있습니다. 

##### fastify.onErrorHookHandler<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>(request: [FastifyRequest][fastifyrequest], reply: [FastifyReply][fastifyreply], error: [FastifyError][fastifyerror], done: () => void): Promise\<unknown\> | void

[src](https://github.com/fastify/fastify/blob/main/types/hooks.d.ts#L154)

이 훅은 커스텀 에러 로깅을 수행하거나 에러 발생 시 특정 헤더를 추가하야 하는 경우에 유용합니다.

이것은 오류를 변경하기 위한 것이 아니며, `reply.send`를 호출하면 예외가 발생합니다.

이 훅은 customErrorHandler가 실행된 후에만, 또한 customErrorHandler가 사용자에게 에러를 다시 보내는 경우에만 실행됩니다(기본적으로 customErrorHandler는 항상 사용자에게 에러를 다시 보냅니다).

주의: 다른 훅과 달리, `done` 함수에 에러를 전달하는 것은 지원되지 않습니다.

##### fastify.onRouteHookHandler<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [RequestGeneric][fastifyrequestgenericinterface], [ContextConfig][contextconfiggeneric]>(opts: [RouteOptions][routeoptions] & { path: string; prefix: string }): Promise\<unknown\> | void

[src](https://github.com/fastify/fastify/blob/main/types/hooks.d.ts#L174)

새 라우트가 등록되면 트리거됩니다. 리스너에는 routeOptions 객체가 유일한 파라미터로 전달됩니다. 인터페이스는 동기식이므로 리스너는 콜백을 전달받지 않습니다.

##### fastify.onRegisterHookHandler<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [Logger][loggergeneric]>(instance: [FastifyInstance][fastifyinstance], done: (err?: [FastifyError][fastifyerror]) => void): Promise\<unknown\> | void

[src](https://github.com/fastify/fastify/blob/main/types/hooks.d.ts#L191)

새 플러그인이 등록되고 새 캡슐화 컨텍스트가 생성될 때 트리거됩니다. 훅이 등록된 코드보다 먼저 실행됩니다.

이 훅은 플러그인 컨텍스트가 언제 형성되는지 알아야 하는 플러그인을 개발하거나, 그 특정 컨텍스트에서만 작동하려고 할 때 유용할 수 있습니다.

주의: 플러그인이 fastify-plugin 내부에 래핑된 경우 이 훅이 호출되지 않습니다.

##### fastify.onCloseHookHandler<[RawServer][rawservergeneric], [RawRequest][rawrequestgeneric], [RawReply][rawreplygeneric], [Logger][loggergeneric]>(instance: [FastifyInstance][fastifyinstance], done: (err?: [FastifyError][fastifyerror]) => void): Promise\<unknown\> | void

[src](https://github.com/fastify/fastify/blob/main/types/hooks.d.ts#L206)

`fastify.close()`가 서버를 중지하기 위해 호출될 때 트리거됩니다. 예를 들어 데이터베이스에 대한 연결을 닫기 위해 플러그인에 "종료" 이벤트가 필요할 때 유용합니다.

<!-- Links -->

[fastify]: #fastifyrawserver-rawrequest-rawreply-loggeropts-fastifyserveroptions-fastifyinstance
[rawservergeneric]: #rawserver
[rawrequestgeneric]: #rawrequest
[rawreplygeneric]: #rawreply
[loggergeneric]: #logger
[rawbodygeneric]: #rawbody
[httpmethods]: #fastifyhttpmethods
[rawserverbase]: #fastifyrawserverbase
[rawserverdefault]: #fastifyrawserverdefault
[fastifyrequest]: #fastifyfastifyrequestrawserver-rawrequest-requestgeneric
[fastifyrequestgenericinterface]: #fastifyrequestgenericinterface
[rawrequestdefaultexpression]: #fastifyrawrequestdefaultexpressionrawserver
[fastifyreply]: #fastifyfastifyreplyrawserver-rawreply-contextconfig
[rawreplydefaultexpression]: #fastifyrawreplydefaultexpression
[fastifyserveroptions]: #fastifyfastifyserveroptions-rawserver-logger
[fastifyinstance]: #fastifyfastifyinstance
[fastifyloggeroptions]: #fastifyfastifyloggeroptions
[contextconfiggeneric]: #ContextConfigGeneric
[fastifyplugin]: #fastifyfastifypluginoptions-rawserver-rawrequest-requestgeneric
[fastifyplugincallback]: #fastifyfastifyplugincallbackoptions
[fastifypluginasync]: #fastifyfastifypluginasyncoptions
[fastifypluginoptions]: #fastifyfastifypluginoptions
[fastifyregister]: #fastifyfastifyregisterrawserver-rawrequest-requestgenericplugin-fastifyplugin-opts-fastifyregisteroptions
[fastifyregisteroptions]: #fastifyfastifytregisteroptions
[loglevel]: #fastifyloglevel
[fastifyerror]: #fastifyfastifyerror
[routeoptions]: #fastifyrouteoptionsrawserver-rawrequest-rawreply-requestgeneric-contextconfig
