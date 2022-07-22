# Fastify에 기여하기
<a id="contributing"></a>

Fastify에 기여하는 일에 관심을 가져주셔서 감사합니다. 
여러분의 지식과 지원을 받게 될 것을 기대합니다. 
이 가이드가 저희를 돕고자하는 여러분들을 도와줄겁니다.

> ## Note
> 이것은 비공식 가이드입니다.
> 그래서 공식 가이드의 [기여하기 문서](https://github.com/fastify/fastify/blob/main/CONTRIBUTING.md) 전문을 확인하시길 바랍니다.
> 그리고 저희의 개발자 full details and our [개발자 원천 증명](https://en.wikipedia.org/wiki/Developer_Certificate_of_Origin) 문서도 
> 확인 바랍니다.

## 목차
<a id="contributing-toc"></a>

- [목차](#table-of-contents)
- [기대하는 기여 방식](#types-of-contributions-were-looking-for)
- [기본 규칙 및 기대 사항](#ground-rules--expectations)
- [기여하는 방법](#how-to-contribute)
- [환경 설정하기](#setting-up-your-environment)
  - [Visual Studio Code 사용하기](#using-visual-studio-code)

## 기대하는 기여 방식
<a id="contribution-types"></a>

간단하게 말하자면, 저희는 어떤 형태의 기여 방식이든 기꺼이 환영합니다. 
기여하는 일에는 길고 짧음이 없습니다. 어떤 방식이든 저희는 기쁘게 받아드립니다:

* 문서 개선하기: 작은 오타 수정부터 큰 단위의 문서 재작성까지 가능합니다.
* pull requests 와 [discussions](https://github.com/fastify/fastify/discussions)에 온 질문에 답변을 달아 다른 분들을 도울 수 있습니다.
* [흔한 버그](https://github.com/fastify/fastify/issues?q=is%3Aissue+is%3Aopen+label%3Abug)를 수정할 수 있습니다.
* 기존에 잘 알려지지 않은 버그를 찾아서 이슈에 등록해서 알릴 수 있습니다.

## 기본 규칙 및 기대 사항
<a id="contributing-rules"></a>

시작하기에 앞서서, 여러분이 숙지하길 바라는 점들을 알려드리겠습니다.

* 프로젝트에서 상대방은 존중하고 배려해주세요. 
  이 프로젝트는 전세계의 누구에게나 열려 있고 다양한 사람들이 참여하고 있습니다.
  각각의 사람마다 프로젝트를 바라보는 자신만의 시각과 견해를 가지고 있을 수 있습니다. 
  그래서 다른 분들이 생각을 듣고 동의하거나 절충안을 찾아서 함께 문제를 해결해 나가길 바랍니다.
* 프로젝트에 참여하시려면 먼저 [작성 원칙](https://github.com/fastify/fastify/blob/main/CODE_OF_CONDUCT.md)을 확인하시고 규칙에 따라 진행하셔야 합니다.
* pull request를 하시려면, 모든 테스트를 먼저 통과하셔야 합니다.
  만약 테스트를 통과하지 못하셨다면, 그 이유에 대해서 기술해주셔서 저희가 merge할 때 확인할 수 있게 해주실길 바랍니다.

## 기여하는 방법
<a id="contributing-how-to"></a>

먼저, [issues](https://github.com/fastify/fastify/issues) 와 [pull requests](https://github.com/fastify/fastify/pulls)를 통해서 다른 분들이 비슷한 문제나 아이디어를 제공하지는 않았는지 확인바랍니다.

만약 여러분의 아이디어를 찾지 못했고, 이 가이드의 목적과도 꼭 맞아 떨어진다고 생각하신다면 아래의 방법을 따라해주세요.
* **사소한 문제일 경우** 예를 들면, 오타 같은 문제를 수정하는 일은 수정 완료 후 pull request를 요청해주세요
* **중요한 문제일 경우** 예를 들면, 새로운 기능을 추가하는 일과 같은 경우에는 먼저 이슈에 등록해주세요.
  그래야 작업하기 전에 다른 사람들이 그 이슈에 대해 토론할 수 있습니다.

<!--
TODO: add link to a style guide, when we have one, here as in
https://github.com/github/opensource.guide/blob/2868efbf0c14aec821909c19e210c3603a4a7805/CONTRIBUTING.md#style-guide
-->

## 환경 설정하기
<a id="contributing-environment"></a>

문서 작성과 코드를 작성할 때 작성 규칙을 준수해주세요.
특정 인기 있는 도구들이 코드를 자동으로 수정해주거나 문법을 수정해줄 수 있습니다.
그러나 이 프로젝트에서 사용하는 스타일과 [StandardJS](https://standardjs.com)의 코드 포멧팅과 일치하지 않는 이상은 사용을 지양해주시길 권장합니다.

### Visual Studio Code 사용하기
<a id="contributing-vscode"></a>

[Visual Studio Code (VSCode) portable](https://code.visualstudio.com/docs/editor/portable)에서 Fastify 개발 환경을 만드는 법을 찾을 수 있습니다.
이 문서는 오직 macOS상에서 개발 환경 설정에 관해 기록되어 있습니다. 하지만 다른 모든 플렛폼에서 환경 설정 원칙은 동일합니다.
링크로 첨부된 VSCode portable 가이드 문서를 통해서 다른 환경에서 설정하는 법을 확인하세요.

먼저, [VSCode](https://code.visualstudio.com/download)를 다운로드하고 `/Applications/VSCodeFastify/` 폴더에 언팩해주세요.
그러고 나서, 터미널상에서 다음의 명령어를 실행시키고 "found"를 출력해주세요.

```sh
[ -d /Applications/VSCodeFastify/Visual\ Studio\ Code.app ] && echo "found"
```

VSCode 포터블 가이드에 따르면, 포터블 모드가 올바르게 작동되게 하기 위해서 어플리케이션의 샌드박스를 해제해야 합니다.
이 문제를 해결하기 위해 터미널상에서 다음의 명령어들을 실행시켜 주세요.

```sh
xattr -dr com.apple.quarantine /Applications/VSCodeFastify/Visual\ Studio\ Code.app
```

다음으로, VSCode에서 요구하는 데이터 디렉토리를 만드세요.

```sh
mkdir -p /Applications/VSCodeFastify/code-portable-data/{user-data,extensions}
```

계속 진행하기에 앞서서, 터미널에서 `code` 사용할 수 있도록 `PATH`를 추가해주세요.
[수동으로 VSCode `PATH` 넣기](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line)를 통해 방법을 확인할 수 있습니다.
위 문서에서는 다양한 쉘 위에서 어떻게 해야하는지 알려주고 있습니다. 그래서 자신이 선호하는 쉘 가이드를 문서에서 찾아 지시 사항에 따라 진행해주세요.
그러나 `code` 도구를 직접 참조하는 대신 별칭을 정의하여 약간 조정할 것입니다. 이것은 현재 사용하고 있을 수 있는 다른 VSCode 설치와 충돌하지 않도록 하기 위한 것이며, 본 가이드를 Fastify에만 적용하기 위한 것입니다.
결론적으로는 다음의 명령어를 사용하기를 권장드립니다.

```sh
alias code-fastify="/Applications/VSCodeFastify/Visual\ Studio\ Code.app/Contents/Resources/app/bin/code"
```

정상적으로 작동했다면 `code-fastify --version` 명령어를 터미널에 넣어 다음처럼 결과를 볼 수 있습니다.

```sh
❯ code-fastify --version
1.50.0
93c2f0fbf16c5a4b10e4d5f89737d9c2c25488a3
x64
```

이제 VSCode 설치를 완료했습니다. 그리고 커맨드 라인을 통해서도 작업을 진행할 수 있습니다. 
프로젝트 스타일에 맞게 포멧팅을 해서 자바스크립트로 코드를 작성하려면 익스텐션을 설치해야 합니다.

```sh
code-fastify --install-extension dbaeumer.vscode-eslint
```

위 명령어가 성공적으로 실행되었다면, 다음의 명령어를 실행시켰을 때 "found"가 정상적으로 출력됩니다.

```sh
[ -d /Applications/VSCodeFastify/code-portable-data/extensions/dbaeumer.vscode-eslint-* ] && echo "found"
```

이제 로컬 폴더에 있는 Fastify 프로젝트를 VSCode에서 열어봅시다.

```sh
code-fastify .
```

새 VSCode 윈도우 창이 열리면 사이드 바에서 Fastify 프로젝트 파일들을 볼 수 있습니다.
그렇다고 모든 설정이 끝난 것이 아닙니다! 
VSCode를 사용하기 전에 해야할 기본 설정이 몇개 남아있습니다.

VSCode에서 `cmd+shift+p` 를 눌러 커맨드 인풋 프롬프트를 열어 주세요.
`open settings (json)` 타이핑해서 넣어주면 메뉴 필터를 통해서 같은 아이템을 선택할 수 있습니다.
이 아이템을 선택하면 설정을 수정할 수 있는 문서가 열립니다. 
다음의 JSON 코드를 문서에 있는 이미 존재하는 모든 코드 위에 덮어 씌어 붙여 넣어주고 저장해 주세요.

```json
{
    "[javascript]": {
        "editor.defaultFormatter": "dbaeumer.vscode-eslint",
        "editor.codeActionsOnSave": {
            "source.fixAll": true
        }
    },

    "workbench.colorCustomizations": {
        "statusBar.background": "#178bb9"
    }
}
```

마침내, 메뉴 바를 통해 "터미널 > 새 터미널"을 선택해서 새 터미널을 에디터에 열 수 있습니다.

이것으로 Fastify에 기여하기 위해 VSCode에서 해야하는 모든 설정을 완료했습니다.
이제 자바스크립트 파일들을 수정하고 저장할 때, 에디터가 자동으로 스타일을 수정해 줄 것입니다.
