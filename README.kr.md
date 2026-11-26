# tgrep

[English](README.md) · 한국어 · [日本語](README.jp.md)

`tgrep`은 `grep`으로 원문 파일을 검색하고 [fzf](https://github.com/junegunn/fzf)로 결과를 탐색하는 도구입니다. 검색 결과에 커서를 놓으면 원문과 각 번역본에서 해당 줄과 앞뒤 문맥을 함께 볼 수 있습니다.
[kubernetes docs](https://github.com/kubernetes/website)를 번역하면서, 해당 단어의 다른 페이지 번역문을 일괄로 찾기 위해서 만든 도구입니다.

[검색부터 번역 파일 열기까지 GIF로 보기](assets/tgrep-demo.gif)

## 필요한 도구

다음 명령을 `PATH`에서 실행할 수 있어야 합니다.

- `zsh`는 스크립트를 실행합니다.
- `grep`은 원문 파일을 검색합니다.
- `fzf` 0.60.0 이상은 검색 결과를 탐색하고 미리 보는 화면을 제공합니다.
- `awk`는 미리보기에 표시할 줄을 추출합니다.

`PATH`에서 `vim`을 실행할 수 있을 때만 좌우 비교 단축키 `F1`·`F2`가 활성화됩니다.

## 설치

저장소를 복제한 디렉터리에서 다음 명령을 실행합니다.

```sh
mkdir -p "$HOME/.local/bin"
install -m 755 tgrep "$HOME/.local/bin/tgrep"
```

필요하면 `$HOME/.local/bin`을 `PATH`에 추가합니다. 저장소 디렉터리에서 `./tgrep`을 직접 실행할 수도 있습니다.

## 검색하기

언어별 디렉터리에 같은 경로의 문서를 둡니다. 예를 들어 영어 원문이 `en/guide/setup.md`라면 한국어 번역은 `ko/guide/setup.md`, 일본어 번역은 `ja/guide/setup.md`에 둡니다.

```sh
cd docs
tgrep 'sign in'
```

`↑`·`↓`로 결과를 고르면 위쪽에 원문과 번역의 같은 줄이 표시됩니다. 결과가 많으면 `setup`처럼 경로 일부나 줄 번호를 입력해 목록을 좁힙니다. `Esc`로 검색을 종료합니다.

![setup으로 결과를 좁히고 guide/setup.md의 6번째 줄을 영어·한국어·일본어로 미리 보는 화면](assets/tgrep-search.png)

검색어는 대소문자를 구분하지 않는 확장 정규식입니다. 번역이 하나 이상 있는 원문만 검색하며, 숨김 경로와 Git 제외 규칙에 해당하는 미추적 파일은 건너뜁니다.

백틱 3개 이상으로 여는 코드 블록은 검색 결과에서 제외합니다. 블록 뒤의 본문은 원래 줄 번호로 표시됩니다.

## 비교하기

검색 결과에서 **`F1`**을 누르면 왼쪽에 원문, 오른쪽에 첫 번째 번역이 열립니다. **`F2`**는 두 번째 번역을 엽니다. 예를 들어 `TGREP_TARGET_LANGS=ko,ja`이면 `F1`은 한국어, `F2`는 일본어입니다. 이 단축키는 `vim`이 설치되어 있을 때 사용할 수 있습니다.

![Vim에서 영어 원문과 한국어 번역의 6번째 줄을 좌우로 비교하는 화면](assets/tgrep-compare.png)

- 긴 줄은 패널 너비에 맞춰 자동으로 줄바꿈됩니다. `↑`·`↓`로 화면 한 줄씩, `Page Up`·`Page Down`으로 한 페이지씩 양쪽을 함께 스크롤합니다.
- `Esc`를 누르면 같은 검색 결과로 돌아옵니다.

두 패널은 줄 번호를 기준으로 맞춰집니다. 번역에서 줄을 추가하거나 삭제했다면 같은 문장이 다른 줄에 있을 수 있습니다.

## 바로 편집하기

검색 결과에서 **`Enter`**를 누르면 `TGREP_TARGET_LANGS` 순서에서 처음 찾은 번역 파일이 `$EDITOR`(기본값 `vi`)로 열립니다. 아래는 영어 원문에서 찾은 6번째 줄의 한국어 번역을 Vim에서 수정하는 모습입니다.

![Vim 입력 모드에서 ko/guide/setup.md의 6번째 줄을 ‘워크스페이스에 로그인합니다.’로 수정한 화면](assets/tgrep-edit.png)

vi, Vim, Neovim, nano, Emacs, VS Code에서는 선택한 줄로 바로 이동합니다. 그 외 편집기에는 파일 경로만 전달합니다.

## 설정

`~/.config/tgrep/config`에 기본값을 지정할 수 있습니다. zsh가 이 파일을 읽어 실행하므로 셸에서처럼 따옴표와 `$HOME` 같은 변수를 사용할 수 있습니다.

```sh
TGREP_DOCS_DIR="$HOME/kubernetes/website/content"
TGREP_ORIGIN_LANG=en
TGREP_TARGET_LANGS=ko,ja
TGREP_ENTER_ACTION=edit
```

| 설정 | 기본값 | 설명 |
| --- | --- | --- |
| `TGREP_DOCS_DIR` | `.` | 언어별 디렉터리를 담고 있는 상위 디렉터리입니다. 상대 경로는 현재 작업 디렉터리를 기준으로 합니다. |
| `TGREP_ORIGIN_LANG` | `en` | 검색할 원문의 언어 디렉터리입니다. |
| `TGREP_TARGET_LANGS` | `ko,ja` | 미리 볼 번역의 언어 디렉터리 목록입니다. 여러 개는 쉼표로 구분합니다. |
| `TGREP_ENTER_ACTION` | `edit` | Enter 키의 동작입니다. `edit`는 첫 번째 번역 파일을 `$EDITOR`(기본값 `vi`)로 엽니다. `print`는 원문 경로, 줄 번호, 일치한 내용을 출력합니다. |
| `TGREP_BEFORE_LINES` | `3` | 일치한 줄 앞에 표시할 줄 수(0 이상)입니다. |
| `TGREP_AFTER_LINES` | `3` | 일치한 줄 뒤에 표시할 줄 수(0 이상)입니다. |
| `TGREP_BEFORE_LINES_{LANGCODE}` | `TGREP_BEFORE_LINES` 값 | 언어별 앞 문맥입니다. `TGREP_BEFORE_LINES_PT_BR=1`이면 `pt-BR`에서 앞 1줄을 표시합니다. |
| `TGREP_AFTER_LINES_{LANGCODE}` | `TGREP_AFTER_LINES` 값 | 언어별 뒤 문맥입니다. `TGREP_AFTER_LINES_KO=5`이면 한국어 번역에서 뒤 5줄을 표시합니다. |

환경변수에 비어 있지 않은 값이 있으면 해당 실행에서는 설정 파일보다 그 값이 우선합니다. 예를 들어 상위 디렉터리와 원문 언어는 설정 파일에서 읽고 일본어 번역만 보려면 다음과 같이 실행합니다.

```sh
TGREP_TARGET_LANGS=ja tgrep 'sign in'
```

## 라이선스

[MIT 라이선스](LICENSE)
