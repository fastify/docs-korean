# Fastify에 기여하기
<a id="contributing"></a>

Fastify에 기여하는 일에 관심을 가져주셔서 감사합니다. 
저희는 여러분의 지식과 지원을 받게 되어 기쁩니다.
이 가이드는 저희를 돕는 여러분을 지원하기 위한 저희의 노력입니다.

> ## Note
> 이것은 비공식 가이드입니다.
> 공식 [기여하기 문서](https://github.com/fastify/fastify/blob/main/CONTRIBUTING.md)의 전문과 [개발자 원천 증명](https://en.wikipedia.org/wiki/Developer_Certificate_of_Origin)을 확인해주시기 바랍니다.

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
기여하는 일에는 길고 짧음이 없습니다. 
저희는 다음과 같은 기여를 기쁘게 받습니다:

* 문서 개선하기: 작은 오타 수정부터 큰 단위의 문서 재작성까지 가능합니다.
* 풀 리퀘스트와 [discussions](https://github.com/fastify/fastify/discussions)의 질문에 답변을 달아 다른 분들을 도울 수 있습니다.
* [알려진 버그](https://github.com/fastify/fastify/issues?q=is%3Aissue+is%3Aopen+label%3Abug)를 수정할 수 있습니다.
* 기존에 잘 알려지지 않은 버그를 최소한의 재현 방법과 함께 이슈에 등록해서 알릴 수 있습니다.

## 기본 규칙과 기대 사항
<a id="contributing-rules"></a>

시작하기에 앞서서, 여러분이 숙지하길 바라는 점들을 알려드리겠습니다.

* 프로젝트에서 상대방을 존중하고 배려해주세요. 
  이 프로젝트는 전세계의 누구에게나 열려 있고 다양한 사람들이 참여하고 있습니다.
  각각의 사람마다 프로젝트를 바라보는 자신만의 시각과 견해를 가지고 있을 수 있습니다. 
  다른 분들의 생각을 듣고 동의하거나 절충안을 찾아서 함께 문제를 해결해 나가길 바랍니다.
* 프로젝트에 참여하시려면 먼저 [작성 원칙](https://github.com/fastify/fastify/blob/main/CODE_OF_CONDUCT.md)을 확인하시고 규칙에 따라 진행하셔야 합니다.
* 풀 리퀘스트를 여시려면 모든 기여가 테스트를 통과해야 한다는 점을 확실하게 해주셔야 합니다. 만약 통과하지 못한 테스트가 있다면 저희가 병합하기 전에 이유를 기술해주셔야 합니다.

## 기여하는 방법
<a id="contributing-how-to"></a>

먼저, [이슈](https://github.com/fastify/fastify/issues) 와 [풀 리퀘스트](https://github.com/fastify/fastify/pulls)를 통해서 다른 분들이 비슷한 문제나 아이디어를 제공하지는 않았는지 확인바랍니다.

만약 여러분의 생각을 찾지 못했고 가이드의 목적과도 맞아 떨어진다고 생각되시면 아래의 방법을 따라주세요.
* **사소한 문제일 경우** 예를 들면, 오타 같은 문제를 수정하는 일은 수정 완료 후 풀 리퀘스트를 요청해주세요
* **중요한 문제일 경우** 예를 들면, 새로운 기능을 추가하는 일과 같은 경우에는 먼저 이슈에 등록해주세요.
  그래야 작업하기 전에 다른 사람들이 그 이슈에 대해 토론할 수 있습니다.

<!--
TODO: 스타일 가이드가 있을 때 다음과 같이 링크를 추가해주세요.
https://github.com/github/opensource.guide/blob/2868efbf0c14aec821909c19e210c3603a4a7805/CONTRIBUTING.md#style-guide
-->

## 환경 설정하기
<a id="contributing-environment"></a>

이 프로젝트의 코드 작성과 문서 작성 스타일을 준수해주세요.
코드와 문서를 자동으로 수정해주는 몇몇 유명한 도구들은 이 프로젝트의 스타일을 따르지 않습니다.
그러므로 이 프로젝트에서 사용하는 스타일과 [StandardJS](https://standardjs.com)의 코드 포멧팅과 일치하지 않는 이상은 사용을 지양해주시길 권장합니다.

### Visual Studio Code 사용하기
<a id="contributing-vscode"></a>

다음으로는 [Visual Studio Code (VSCode) portable](https://code.visualstudio.com/docs/editor/portable)에서 Fastify 개발 환경을 만드는 법을 찾을 수 있습니다.
이 문서는 macOS 환경을 가정하고 있지만 다른 모든 플랫폼에서도 원리는 동일합니다.
다른 플랫폼의 경우 링크로 첨부된 VSCode portable 가이드에서 확인해보세요.

먼저, [VSCode](https://code.visualstudio.com/download)를 다운로드하고 `/Applications/VSCodeFastify/` 폴더에 언팩해주세요.
그러고 나서, 터미널상에서 다음의 명령어를 실행시키고 "found"를 출력해주세요.

```sh
[ -d /Applications/VSCodeFastify/Visual\ Studio\ Code.app ] && echo "found"
```

VSCode portable 가이드에 언급된 것처럼 portable 모드가 정상적으로 작동할 수 있도록 애플리케이션 샌드박스를 해제해야 합니다.
그러기 위해 터미널상에서 다음의 명령어들을 실행시켜 주세요.

```sh
xattr -dr com.apple.quarantine /Applications/VSCodeFastify/Visual\ Studio\ Code.app
```

다음으로, VSCode에서 요구하는 데이터 디렉토리를 만드세요.

```sh
mkdir -p /Applications/VSCodeFastify/code-portable-data/{user-data,extensions}
```

계속하기 전, 터미널의 `PATH`에 `code` 명령어를 추가해야 합니다. 
이렇게 하기 위해 [직접 VSCode를 PATH에 추가](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line)할 것입니다.
위 문서에서 약술한 것처럼 절차는 여러분의 기본 쉘에 따라 다릅니다. 가이드에서 여러분이 선호하는 쉘에 맞는 절차를 따라주셔야 합니다.
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

이제 작성하는 어떤 JavaScript라도 프로젝트 스타일에 맞도록 포맷팅해주는 확장프로그램을 설치해야 합니다.

```sh
code-fastify --install-extension dbaeumer.vscode-eslint
```

위 명령어가 성공적으로 실행되었다면, 다음 명령어를 실행하면 "found"가 출력될 겁니다.

```sh
[ -d /Applications/VSCodeFastify/code-portable-data/extensions/dbaeumer.vscode-eslint-* ] && echo "found"
```

이제 로컬에 클론한 Fastify 프로젝트의 디렉토리에서, VSCode를 열 수 있습니다:

```sh
code-fastify .
```

새 VSCode 윈도우 창이 열리면 사이드 바에서 Fastify 프로젝트 파일들을 볼 수 있습니다.
그렇다고 모든 설정이 끝난 것이 아닙니다! 
VSCode를 사용하기 전에 해야할 기본 설정이 몇개 남아있습니다.

VSCode에서 `cmd+shift+p`를 눌러 명령어 입력 창을 열어 주세요.
`open settings (json)`을 입력하고 검색된 메뉴 중 일치하는 항목을 선택하세요.
편집기를 위한 설정 파일을 열어줄 것입니다.
다음 JSON 코드를 이 문서에 붙여넣고 이미 있는 텍스트를 덮어쓴 다음 저장합니다.

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
`npm i`를 입력해 Fastify 의존성을 설치합니다.
이것으로 Fastify에 기여하기 위한 커스텀 VSCode 인스턴스의 설정을 완료했습니다.
이제 자바스크립트 파일들을 수정하고 저장할 때, 에디터가 자동으로 스타일을 수정해 줄 것입니다.
