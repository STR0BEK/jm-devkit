# jm-project-kit

## 개요

존맛 프로젝트 킷(jm-project-kit)은 새 프로젝트를 시작할 때마다 Claude Code 하네스(CLAUDE.md, 역할별 agent, LSP, MCP)를
손으로 설정하는 일이 반복되는 게 너무너무너무너무 귀찮아서 만들었다.

이 플러그인은 그 과정을 명령 몇 개로 줄인다. 프로젝트의 빌드 파일과 기존 코드에서 스택·규칙을
읽어 `{프로젝트명}-{역할}` agent 와 CLAUDE.md 를 만들고, 개발 중 규칙이 바뀌면 agent 에 반영한다.
모두 직접 호출할 때만 동작한다.
`/lsp-init` 은 LSP 플러그인만 켜 준다. 언어 서버 실행 파일은 직접 설치해야 한다.

## 명령

| 명령 | 하는 일 |
| --- | --- |
| `/project-init` | CLAUDE.md 와 `{프로젝트명}-{역할}` agent 를 처음 만든다 |
| `/agent-update` | 개발 중 정해진 규칙을 프로젝트 agent 에 반영한다 |
| `/lsp-init` | 스택을 감지해 LSP 플러그인을 전역 또는 프로젝트에 켠다 |
| `/mcp-init` | 프로젝트 루트에 DB MCP 서버 예시가 든 `.mcp.json` 을 만든다 (전역 MCP 는 다루지 않는다) |
| `/mcp-update` | `.mcp.json` 에 채워 넣은 서버를 `.claude/settings.json` 의 `enabledMcpjsonServers` 에 등록한다 |

공통 규칙은 `shared/common-rules.md` 에 있다. 각 명령이 시작할 때 읽는다.

agent 틀은 `templates/agents/{역할}.md` 에 있다. `/project-init` 이 프로젝트 agent 를 만들 때 읽는 복사용 원본이며, agent 로 등록되지는 않는다. 별도 전역 설정 없이 이 플러그인만으로 동작한다.

## 설치

```
/plugin marketplace add <github-id>/<저장소 이름>
/plugin install jm-project-kit@jm-devkit
```

플러그인 소스를 고친 뒤에는 `plugin.json` 의 `version` 을 올리고 다시 설치해야 반영된다.
