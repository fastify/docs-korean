<h1 align="center">Fastify</h1>

## Database

Fastify 생태계는 여러 데이터베이스 엔진 연결을 위한 몇 가지 플러그인을 제공합니다. 해당 가이드에서는 Fastify에서 공식적으로 플러그인을 제공하는 데이터베이스 엔진을 다룹니다.

> Fastify는 데이터베이스에 구애받지 않기에 그대로 사용하는건 얼마든지 가능합니다.
> 이 가이드에 나열된 데이터베이스 플러그인의 예를 따라,
> 누락된 데이터베이스 엔진을 위한 플러그인을 작성할 수 있습니다.

> 만약 Fastify 플러그인을 직접 작성하고 싶다면
> [plugins guide](./Plugins-Guide.md)을 참조해보세요

### [MySQL](https://github.com/fastify/fastify-mysql)

다음 명령어로 플러그인을 설치합니다 `npm i @fastify/mysql`.

*예시:*

```javascript
const fastify = require('fastify')()

fastify.register(require('@fastify/mysql'), {
  connectionString: 'mysql://root@localhost/mysql'
})

fastify.get('/user/:id', function(req, reply) {
  fastify.mysql.query(
    'SELECT id, username, hash, salt FROM users WHERE id=?', [req.params.id],
    function onResult (err, result) {
      reply.send(err || result)
    }
  )
})

fastify.listen({ port: 3000 }, err => {
  if (err) throw err
  console.log(`server listening on ${fastify.server.address().port}`)
})
```

### [Postgres](https://github.com/fastify/fastify-postgres)
다음 명령어로 플러그인을 설치합니다 `npm i pg @fastify/postgres`.

*예시*:

```javascript
const fastify = require('fastify')()

fastify.register(require('@fastify/postgres'), {
  connectionString: 'postgres://postgres@localhost/postgres'
})

fastify.get('/user/:id', function (req, reply) {
  fastify.pg.query(
    'SELECT id, username, hash, salt FROM users WHERE id=$1', [req.params.id],
    function onResult (err, result) {
      reply.send(err || result)
    }
  )
})

fastify.listen({ port: 3000 }, err => {
  if (err) throw err
  console.log(`server listening on ${fastify.server.address().port}`)
})
```

### [Redis](https://github.com/fastify/fastify-redis)
다음 명령어로 플러그인을 설치합니다 `npm i @fastify/redis`

*예시:*

```javascript
'use strict'

const fastify = require('fastify')()

fastify.register(require('@fastify/redis'), { host: '127.0.0.1' })
// 또는
fastify.register(require('@fastify/redis'), { url: 'redis://127.0.0.1', /* other redis options */ })

fastify.get('/foo', function (req, reply) {
  const { redis } = fastify
  redis.get(req.query.key, (err, val) => {
    reply.send(err || val)
  })
})

fastify.post('/foo', function (req, reply) {
  const { redis } = fastify
  redis.set(req.body.key, req.body.value, (err) => {
    reply.send(err || { status: 'ok' })
  })
})

fastify.listen({ port: 3000 }, err => {
  if (err) throw err
  console.log(`server listening on ${fastify.server.address().port}`)
})
```

기본적으로 `@fastify/redis`는 Fastify 서버가 닫혀도 자동으로 클라이언트 연결을 닫지 않습니다.
따라서 자동으로 닫히게 반영하기 위해서는 다음과 같이 클라이언트를 등록해야합니다.

```javascript
fastify.register(require('@fastify/redis'), {
  client: redis,
  closeClient: true
})
```

### [Mongo](https://github.com/fastify/fastify-mongodb)
다음 명령어로 플러그인을 설치합니다 `npm i @fastify/mongodb`

*예시:*
```javascript
const fastify = require('fastify')()

fastify.register(require('@fastify/mongodb'), {
  // force to close the mongodb connection when app stopped
  // the default value is false
  forceClose: true,
  
  url: 'mongodb://mongo/mydb'
})

fastify.get('/user/:id', function (req, reply) {
  // 또는 this.mongo.client.db('mydb').collection('users')
  const users = this.mongo.db.collection('users')

  // id가 ObjectId 형식인 경우 새로운 ObjectId를 생성해야합니다.
  const id = this.mongo.ObjectId(req.params.id)
  users.findOne({ id }, (err, user) => {
    if (err) {
      reply.send(err)
      return
    }
    reply.send(user)
  })
})

fastify.listen({ port: 3000 }, err => {
  if (err) throw err
})
```

### [LevelDB](https://github.com/fastify/fastify-leveldb)
다음 명령어로 플러그인을 설치합니다 `npm i @fastify/leveldb`

*예시:*
```javascript
const fastify = require('fastify')()

fastify.register(
  require('@fastify/leveldb'),
  { name: 'db' }
)

fastify.get('/foo', async function (req, reply) {
  const val = await this.level.db.get(req.query.key)
  return val
})

fastify.post('/foo', async function (req, reply) {
  await this.level.db.put(req.body.key, req.body.value)
  return { status: 'ok' }
})

fastify.listen({ port: 3000 }, err => {
  if (err) throw err
  console.log(`server listening on ${fastify.server.address().port}`)
})
```

### 데이터베이스 라이브러리 플러그인 만들기
이외에도 다른 데이터베이스 라이브러리용 플러그인을 작성할 수 있습니다. (예: Knex, Prisma, or TypeORM)
이번 예시에서는 [Knex](https://knexjs.org/)를 사용해보겠습니다.

```javascript
'use strict'

const fp = require('fastify-plugin')
const knex = require('knex')

function knexPlugin(fastify, options, done) {
  if(!fastify.knex) {
    const knex = knex(options)
    fastify.decorate('knex', knex)

    fastify.addHook('onClose', (fastify, done) => {
      if (fastify.knex === knex) {
        fastify.knex.destroy(done)
      }
    })
  }

  done()
}

export default fp(knexPlugin, { name: 'fastify-knex-example' })
```

### 데이터베이스 엔진 플러그인 만들기

이번 예제에서는 기본적인 MySQL 플러그인을 처음부터 만들어봅니다. (이는
이해를 돕기 위한 예제이므로, 프로덕션 환경에서는 공식 플러그인을 사용해주세요.)

```javascript
const fp = require('fastify-plugin')
const mysql = require('mysql2/promise')

function fastifyMysql(fastify, options, done) {
  const connection = mysql.createConnection(options)

  if (!fastify.mysql) {
    fastify.decorate('mysql', connection)
  }

  fastify.addHook('onClose', (fastify, done) => connection.end().then(done).catch(done))

  done()
}

export default fp(fastifyMysql, { name: 'fastify-mysql-example' })
```

### 마이그레이션

데이터베이스 스키마 마이그레이션은 데이터베이스 관리 및 개발의 필수입니다.
마이그레이션은 반복적이고 테스트 가능한 방식으로 데이터베이스 스키마를 관리하고 데이터 손실을 방지하는 방법을 제공합니다.

본 가이드의 시작부분에서 설명했듯이, Fastify는 데이터베이스 형식에 얽매이지 않으며 어떤 NodeJS 데이터베이스 마이그레이션 툴이든 적용 가능합니다.
Postgres, MySQL, SQLServer 그리고 SQLite를 지원하는 [Postgrator](https://www.npmjs.com/package/postgrator)의 예시를 따라해봅니다.
MongoDB 마이그레이션의 경우 [migrate-mongo](https://www.npmjs.com/package/migrate-mongo)를 참조하세요.

#### [Postgrator](https://www.npmjs.com/package/postgrator)

Postgrator는 SQL 스크립트 디렉토리로 데이터베이스 스키마를 변경하는 NodeJS SQL 마이그레이션 도구입니다.
마이그레이션 폴더내에 각 SQL 파일명은 다음과 같은 패턴으로 작성되어야 합니다: : ` [version].[action].[optional-description].sql`.

**version:** 순차적으로 증가하는 양수 값이어야 합니다. (e.g. `001` 혹은 타입스탬프값).

**action:** 반드시 `do` 혹은 `undo`여야 합니다. `do` 는 버전을 올리며, `undo`
는 반대로 작업을 되돌리는 것을 의미합니다. 통상적으로 타 마이그레이션 도구에서 사용되는 `up` 및 `down` 과 같은 의미입니다.

**optional-description** 마이그레이션에 따른 변경사항을 기술합니다.
선택 사항이지만, 해당 마이그레이션의 변경 사항이 어떤 것인지 쉽게 알아볼 수 있도록 사용해야 합니다.

이번 예시에서는 'users' 테이블을 생성하는 단일 마이그레이션 파일을 만들고, 'Postgrator'를 사용해 마이그레이션을 실행합니다.

> `npm i pg postgrator`를 실행하여 예제 구동에 필요한 의존성을 설치합니다.

```sql
// 001.do.create-users-table.sql
CREATE TABLE IF NOT EXISTS users (
  id SERIAL PRIMARY KEY NOT NULL,
  created_at DATE NOT NULL DEFAULT CURRENT_DATE,
  firstName TEXT NOT NULL,
  lastName TEXT NOT NULL
);
```
```javascript
const pg = require('pg')
const Postgrator = require('postgrator')
const path = require('path')

async function migrate() {
  const client = new pg.Client({
    host: 'localhost',
    port: 5432,
    database: 'example', 
    user: 'example',
    password: 'example',
  });

  try {
    const postgrator = new Postgrator({
      migrationPattern: path.join(__dirname, '/migrations/*'),
      driver: 'pg',
      database: 'example',
      schemaTable: 'migrations',
      currentSchema: 'public', // Postgres and MS SQL Server 만 적용됩니다.
      execQuery: (query) => client.query(query),
    });

    const result = await postgrator.migrate()

    if (result.length === 0) {
      console.log(
        'No migrations run for schema "public". Already at the latest one.'
      )
    }

    console.log('Migration done.')

    process.exitCode = 0
  } catch(err) {
    console.error(error)
    process.exitCode = 1
  }
  
  await client.end()
}

migrate()
```
