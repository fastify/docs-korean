<h1 align="center">Fastify</h1>

## 벤치마크

벤치마킹은 변경 사항이 애플리케이션의 성능에 영향을 얼마나 미칠 지 측정하려고 할 때 굉장히 중요합니다.
여러분의 애플리케이션은 사용자와 기여자의 관점에서 각각 벤치마킹할 수 있도록 저희 팀은 간단한 방법을 제공하고 있습니다.
아래의 것들은 여러분이 벤치마킹을 각각의 브랜치와 Node.JS 버전별로 자동화할 수 있도록 도와줄 것입니다.

그리고 아래는 저희가 사용할 모듈들입니다:

- [Autocannon](https://github.com/mcollina/autocannon): Node.JS로 작성된 HTTP/1.1 테스팅 도구.
- [Branch-comparer](https://github.com/StarpTech/branch-comparer): 여러개의 깃 브랜치를 체크아웃하고 스크립트를 실행 후 결과를 로깅합니다.
- [Concurrently](https://github.com/kimmobrunfeldt/concurrently): 동시 여러 명령어를 실행시킵니다.
- [Npx](https://github.com/npm/npx): npm@5.2.0와 함께 제공된 Node.JS 버전과 상관없이 로컬의 Node.JS로 스크립트를 실행시킬 수 있는 NPM 패키지 실행기입니다.

## 간단히

### 현재 브랜치에서 벤치마크 실행

```sh
npm run benchmark
```

### 다른 Node.JS 버전에서 벤치마크 실행 ✨

```sh
npx -p node@10 -- npm run benchmark
```

## 고급스럽게

### 다른 브랜치들에서 벤치마크 실행

```sh
branchcmp --rounds 2 --script "npm run benchmark"
```

### 다른 Node.JS 버전을 사용하여 다른 브랜치들에서 벤치마크 실행 ✨

```sh
branchcmp --rounds 2 --script "npm run benchmark"
```

### 현재 브랜치와 main 브랜치 비교하기 (Gitflow)

```sh
branchcmp --rounds 2 --gitflow --script "npm run benchmark"
```

또는

```sh
npm run bench
```

### 각각 다른 예제 사용하기

<!-- markdownlint-disable -->

```sh
branchcmp --rounds 2 -s "node ./node_modules/concurrently -k -s first \"node ./examples/asyncawait.js\" \"node ./node_modules/autocannon -c 100 -d 5 -p 10 localhost:3000/\""
```

<!-- markdownlint-enable -->
