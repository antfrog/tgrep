# tgrep

[English](README.md) · 한국어 · [日本語](README.jp.md)

`tgrep`은 `grep`으로 원문 파일을 검색하고 [fzf](https://github.com/junegunn/fzf)로 결과를 탐색하는 도구입니다. 검색 결과에 커서를 놓으면 원문과 각 번역본에서 해당 줄과 앞뒤 문맥을 함께 볼 수 있습니다.

![TGREP_TARGET_LANGS=ko,jp로 'sign in'을 검색하고 한국어와 일본어 번역을 한 화면에서 확인하는 모습](assets/tgrep-demo.gif)

## 필요한 도구

다음 명령을 `PATH`에서 실행할 수 있어야 합니다.

- `zsh`는 스크립트를 실행합니다.
- `grep`은 원문 파일을 검색합니다.
- `fzf`는 검색 결과를 탐색하고 미리 보는 화면을 제공합니다.
- `awk`는 미리보기에 표시할 줄을 추출합니다.

## 설치

저장소를 복제한 디렉터리에서 다음 명령을 실행합니다.

```sh
mkdir -p "$HOME/.local/bin"
install -m 755 tgrep "$HOME/.local/bin/tgrep"
```

필요하면 `$HOME/.local/bin`을 `PATH`에 추가합니다. 저장소 디렉터리에서 `./tgrep`을 직접 실행할 수도 있습니다.

## 원문을 검색해 번역 확인하기

예를 들어 영어 원문과 한국어 번역이 각 언어 디렉터리 아래 같은 상대 경로에 있다고 가정합니다.

```text
docs/
├── en/guide/setup.md
└── ko/guide/setup.md
```

```sh
cd docs
tgrep 'sign in'
```

검색어에는 대소문자를 구분하지 않는 확장 정규식(`grep -Ei`)을 사용합니다. 숨김 파일과 디렉터리는 건너뜁니다. Git이 설치되어 있고 문서 디렉터리가 Git 작업 트리 안에 있으면, Git이 추적하지 않는 파일 중 `.gitignore` 등 Git 제외 규칙에 해당하는 파일도 건너뜁니다. 대응하는 번역본이 하나 이상 있는 원문 파일만 검색합니다. 화살표 키로 결과를 이동하고, Enter를 누르면 선택한 결과를 출력합니다. Esc를 누르면 종료합니다. 미리보기는 각 파일의 같은 줄 번호를 기준으로 하므로 번역 과정에서 줄을 추가하거나 삭제하면 표시되는 내용이 어긋날 수 있습니다.

## 설정

`~/.config/tgrep/config`에 기본값을 지정할 수 있습니다. zsh가 이 파일을 읽어 실행하므로 셸에서처럼 따옴표와 `$HOME` 같은 변수를 사용할 수 있습니다.

```sh
TGREP_DOCS_DIR="$HOME/work/docs"
TGREP_ORIGIN_LANG=en
TGREP_TARGET_LANGS=ko,ja
```

| 설정 | 기본값 | 설명 |
| --- | --- | --- |
| `TGREP_DOCS_DIR` | `.` | 언어별 디렉터리를 담고 있는 상위 디렉터리입니다. 상대 경로는 현재 작업 디렉터리를 기준으로 합니다. |
| `TGREP_ORIGIN_LANG` | `en` | 검색할 원문의 언어 디렉터리입니다. |
| `TGREP_TARGET_LANGS` | `ko,ja` | 미리 볼 번역의 언어 디렉터리 목록입니다. 여러 개는 쉼표로 구분합니다. |

환경변수에 비어 있지 않은 값이 있으면 해당 실행에서는 설정 파일보다 그 값이 우선합니다. 예를 들어 상위 디렉터리와 원문 언어는 설정 파일에서 읽고 일본어 번역만 보려면 다음과 같이 실행합니다.

```sh
TGREP_TARGET_LANGS=ja tgrep 'sign in'
```

## 라이선스

[MIT 라이선스](LICENSE)
