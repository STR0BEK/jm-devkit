# jm-devkit

Claude Code 플러그인 마켓플레이스다. 새 프로젝트를 시작할 때마다 반복하던 Claude Code 환경 설정(CLAUDE.md, 역할별 agent, LSP, MCP)을 명령 몇 개로 줄이려고 만들었다.

## 플러그인 종류(추후 업데이트 예정)

| 플러그인 | 하는 일 |
| --- | --- |
| [`jm-project-kit`](jm-project-kit/README.md) | CLAUDE.md, 프로젝트 agent, LSP, MCP 설정을 만들고 유지한다 (`/project-init` 등 명령 5개) |
| [`web-lsp`](web-lsp/.lsp.json) | HTML, CSS, JSON 언어 서버를 Claude Code 에 연결한다 |

## 설치

마켓플레이스를 한 번 등록하고, 필요한 플러그인을 설치한다

```
마켓플레이스 등록
/plugin marketplace add STR0BEK/jm-devkit
플러그인 설치
ex) /plugin install jm-project-kit@jm-devkit
ex) /plugin install web-lsp@jm-devkit
ex) /plugin -> Discover탭 -> 검색 -> 플러그인 선택 -> 설치
```

- 세션을 다시 시작하면 적용된다.
- 터미널에서는 앞에 `claude` 를 붙이고 `/` 를 뺀다. 예: `claude plugin install jm-project-kit@jm-devkit`
- private 저장소이면 git 인증이 되어 있어야 한다.
- push 하기 전에 로컬 경로로 먼저 확인할 수 있다. `/plugin marketplace add D:\dev\jm-devkit`

## jm-project-kit 사용 순서

새 프로젝트를 시작할 때:

1. `/project-init` — CLAUDE.md 와 `{프로젝트명}-{역할}` agent 를 만든다.
2. `/lsp-init` — 스택에 맞는 LSP 플러그인을 켠다. 전역과 프로젝트 중에서 고른다.
3. `/mcp-init` — DB MCP 서버 예시가 든 `.mcp.json` 을 만든다.
4. `.mcp.json` 의 `<...>` 자리를 실제 접속 정보로 채운다.
5. `/mcp-update` — 채운 서버를 `.claude/settings.json` 에 등록한다.

개발하는 중에는:

- `/agent-update` — 정해진 규칙, 교정한 내용, 새로 설치한 skill 을 agent 에 반영한다.

## 동작 원칙

- 모든 명령은 직접 호출할 때만 동작한다. 평소 컨텍스트에는 올라가지 않는다.
- 이미 있는 파일은 덮어쓰지 않는다. 바뀌는 항목만 diff 로 보여 주고 확인한 뒤에 반영한다.
- 근거가 없는 항목은 추측해서 채우지 않는다. 빌드 파일, 규칙 문서, 기존 코드에서 확인한 것만 쓴다.
- 환경 설정은 사용자가 직접 한다. 서버, DB, 언어 서버 설치는 안내만 하고 실행하지 않는다.
- 접속 정보 값(비밀번호, 토큰)은 출력하지 않는다.

## web-lsp 준비물

`web-lsp` 는 언어 서버 실행 파일을 설치해 주지 않는다. 아래 명령이 PATH 에 있어야 동작한다. 직접 설치한다.

- `vscode-html-language-server`
- `vscode-css-language-server`
- `vscode-json-language-server`

## 갱신

- 저장소에서 소스를 고치고 해당 플러그인의 `.claude-plugin/plugin.json` 의 `version` 을 올려 push 한다.
- 사용하는 쪽에서 `/plugin marketplace update jm-devkit` 를 실행하고 세션을 다시 시작한다.

## 제거

```
/plugin uninstall jm-project-kit@jm-devkit
/plugin marketplace remove jm-devkit
```

## 저장소 구조

```
jm-devkit/
├─ .claude-plugin/marketplace.json      마켓플레이스 정의 (name: jm-devkit)
├─ jm-project-kit/
│  ├─ .claude-plugin/plugin.json
│  ├─ skills/                           project-init, agent-update, lsp-init, mcp-init, mcp-update
│  ├─ templates/agents/                 agent 틀 4개 (복사용 원본)
│  ├─ shared/common-rules.md            각 명령이 먼저 읽는 공통 규칙
│  └─ README.md
└─ web-lsp/
   ├─ .claude-plugin/plugin.json
   └─ .lsp.json
```

플러그인 폴더에는 README 같은 문서나 `shared/`, `templates/` 를 둬도 된다. Claude Code 는 `skills/`, `agents/`, `commands/`, `hooks/`, `.mcp.json`, `.lsp.json` 처럼 약속된 위치만 읽는다.
