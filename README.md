# tgrep

English · [한국어](README.kr.md) · [日本語](README.jp.md)

`tgrep` searches source-language files with `grep` and lets you browse the matches in [fzf](https://github.com/junegunn/fzf). Highlight a match to see the source line and the surrounding lines in each available translation.
I built `tgrep` while translating the [Kubernetes documentation](https://github.com/kubernetes/website) so I could look up translations of the same term across multiple pages at once.

![Searching for 'sign in' with TGREP_TARGET_LANGS=ko,jp and viewing the Korean and Japanese translations together](assets/tgrep-demo.gif)

## Dependencies

The following commands must be on your `PATH`.

- `zsh` runs the script.
- `grep` searches the source files.
- `fzf` displays the interactive results and preview.
- `awk` extracts the lines shown in the preview.

## Install

Run these commands from a clone of this repository.

```sh
mkdir -p "$HOME/.local/bin"
install -m 755 tgrep "$HOME/.local/bin/tgrep"
```

Add `$HOME/.local/bin` to your `PATH` if needed. You can also run `./tgrep` directly from the clone.

## Search a translation

Suppose your documentation has an English original and a Korean translation at the same relative path:

```text
docs/
├── en/guide/setup.md
└── ko/guide/setup.md
```

```sh
cd docs
tgrep 'sign in'
```

Search patterns use case-insensitive extended regular expressions (`grep -Ei`). Hidden files and directories are skipped. When Git is available and the docs directory is in a Git working tree, tgrep skips untracked files excluded by `.gitignore` or other standard Git ignore rules. Only source files with at least one corresponding translation are searched. Use the arrow keys to browse results, Enter to print the selected match, or Esc to quit. The preview uses the same line number in each file, so passages may differ when translations add or remove lines.

## Configuration

Create `~/.config/tgrep/config` to set your defaults. The file is sourced by zsh, so shell quoting and `$HOME` expansion work:

```sh
TGREP_DOCS_DIR="$HOME/work/docs"
TGREP_ORIGIN_LANG=en
TGREP_TARGET_LANGS=ko,ja
```

| Setting | Default | Meaning |
| --- | --- | --- |
| `TGREP_DOCS_DIR` | `.` | Directory containing the language directories. Relative paths start at the current working directory. |
| `TGREP_ORIGIN_LANG` | `en` | Source-language directory to search. |
| `TGREP_TARGET_LANGS` | `ko,ja` | Comma-separated target-language directories to preview. |

A nonempty environment variable overrides the corresponding file setting for that invocation. For example, to preview only Japanese with the root and source language from your config:

```sh
TGREP_TARGET_LANGS=ja tgrep 'sign in'
```

## License

[MIT License](LICENSE)
